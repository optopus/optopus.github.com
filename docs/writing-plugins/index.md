---
layout: layout
title: Writing an Optopus Plugin
---

## The Basics

First let's take a look at a very basic Hello World example:

    module Optopus
      module Plugin
        module HelloWorld
          extend Optopus::Plugin

          plugin do
            nav_link :display => 'HelloWorld', :route => '/hello'
          end

          get '/hello' do
            body "Hello world!"
          end
        end
      end
    end

If you're familiar with Sinatra, this syntax should be very comfortable for you. In fact Optopus is built on extensions to Sinatra. To get going, you're going to want a layout as follows for your plugin:

    hello_world
      |-init.rb
      |-views/

At runtime, Optopus will search for <code>"#{plugin_path}/*/init.rb"</code> and require it. In order for the code to get registered as an extension to the Sinatra app, you have to list the plugin by name in your <code>application.yaml</code> file. Something like this is probably what you want:

    development:
      plugin_path: /home/username/optopus_plugins
      plugins:
        hello_world:

### Using ERB templates

You may either create an inline template like this:

    template :hello_world_index do
      "<div>Welcome to <%= string %></div>"
    end

Or create an ERB file in the views directory of your plugin. You can call the ERB file just like any other Sinatra application:

    get '/hello' do
      erb :hellow_world_index
    end

To work well with many other plugins, it is recommended that you prefix your template files with the name of your plugin.

### Adding additional gems

If you need custom gems, create a Gemfile in your plugin's directory. When you run <code>bundle install</code>, the main <a href="https://github.com/crazed/optopus/blob/master/Gemfile">Optopus Gemfile</a> will search the various plugin_paths and evaluate any Gemfiles that are found. Because of the way we handle multiple Gemfiles, a Gemfile.lock is not included in the repository. Therefore, it is recommended that you specify versions in the actual Gemfile.

### Using the config file in your plugin

To take advantage of <code>application.yaml</code>, you can read any values by using the standard Sinatra settings object. For example, if you're writing a plugin that requires some username and password you can require they exist by utilizing the <code>registered</code> method:

    module Optopus
      module Plugin
        module HelloWorld
          extend Optopus::Plugin

          def make_api_connection
            username = settings.plugins['hello_world']['username']
            password = settings.plugins['hello_world']['password']
            # some magic api connection
          end

          def self.registered(app)
            unless app.settings.plugins.include?('hello_world')
              raise 'Missing configuration!'
            end

            config = app.settings.plugins['hello_world']
            unless config['username']
              raise 'HelloWorld plugin expects a username!'
            end

            unless config['password']
              raise 'HelloWorld plugin expects a password!'
            end

            # Don't forget to call super!
            super(app)
          end
        end
      end
    end

Then, in your <code>application.yaml</code> file, you would have something like this:

    development:
      plugin_path: /home/username/optopus_plugins
      plugins:
        hello_world:
          username: hello
          password: world
