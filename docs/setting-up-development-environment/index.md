---
layout: layout
title: Setting up an Optopus Development Environment
---

## Prerequisites
Before continuing, you should ensure you have working installations of PostgresQL and ElasticSearch.

## Getting started
The easiest way to get your development environment going is to start by cloning the repository:

    $ git clone git://github.com/optopus/optopus.git

Once you have the code in place, you will want to build your dependencies:

    $ cd optopus
    $ bundle install

Now you want to set up your databases.yaml and application.yaml configuration files, an easy way to get started is to copy the examples:

    $ cp config/application.yaml.example config/application.yaml
    $ cp config/databases.yaml.example config/databases.yaml

## Basic configuration
Assuming you have local authentication set up for postgres, all you need for your databases.yaml is something similar to this:

    test:
      adapter: postgresql
      database: optopus_test

    development:
      adapter: postgresql
      database: optopus_dev

**Note**: you will have to create the databases as a super user before trying to use them.

Your application.yaml, should look like this:

    test:
      elasticsearch:
        url: http://localhost:9200
    development:
      elasticsearch:
        url: http://localhost:9200
      authorization:
        type: database

This tells Optopus where your elasticsearch system is configured and that we will be using the database for authorization.

## Running database migrations
To get your database primed with the appropriate tables, you will want to run:

    $ bundle exec rake db:migrate

## Creating an admin user
Before you can really start to get things going, you will want to create an admin user in the database. The easiest way is to just use the rake task available:

    $ bundle exec rake development:create_admin USERNAME=admin PASSWORD=admin

**Note**: if you do not supply a username/password it will default to admin/password

## Starting the development server
Finally, all you need to do is run:

    $ bundle exec shotgun

Since you're using shotgun, you won't need to reload the server to pick up code changes.

## Extra

### Adding seed data
If you want to generate random nodes in your database, you can run:

    $ bundle exec rake db:seed NODES=1000

That will load 1000 random nodes into your database with a few locations set up.

### Running integration tests
To run integration tests, use:

    $ bundle exec rake test

### Submitting pull requests
Please be sure to submit pull requests against feature branches. Example:

    # do your work, then create a new branch
    $ git checkout -b add-new-awesome-feature

    # check things in and commit them, then make sure to pull the latest head
    $ git remote add upstream git://github.com/optopus/optopus.git
    $ git pull --rebase upstream master
