---
layout: post
title: "Migrating your Rails App from MySQL to PostgreSQL"
description: "Migrating rails application from MySQL to PostgreSQL "
tags: [rails, ruby, postgresql, postgres]
---

My friends and I had decided to use PostgreSQL instead of MySQL because we were all interested in trying it out. Since this was a relatively new project with pretty straight-forward use cases, we decided that there shouldn't be any harm in trying it out. The next step was to proceed to migrate our application from MySQL to PostgreSQL. And it had proven to be quite simple.

The first thing I did was to replace the gem in our Gemfile from ``mysql2`` to ``pg``

    gem 'pg'

The next part was updating our ``database.yml``. I only had to update the ``adapter`` and ``encoding`` part of our YAML file.

    development:
      adapter: postgresql
      encoding: unicode
      database: db_name
      pool: 5
      username: <username>
      password:
      host: localhost

    test:
      adapter: postgresql
      encoding: unicode
      database: diwata_test
      pool: 5
      username: <username>
      password:
      host: localhost

Once the database.yml was updated, I ran:

    rake db:create:all

... to create all the database the app needed. Subsequently, I also ran

    rake db:migrate

... to create the necessary tables in the database.

To make sure that everything was ok, I ran ``rspec`` to know whether all the tests were passing. And sure enough, all the tests passed.

Now, our application is using PostgreSQL.