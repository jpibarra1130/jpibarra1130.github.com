---
layout: post
title: "Deploying a Rails Application with Capistrano 3, RVM, Nginx and Unicorn"
description: "Deploying a Rails Application on your Cloud Server with Capistrano 3, Nginx and Unicorn"
tags: [sysadmin, rails, ruby, capistrano 3, nginx, unicorn, git, deploy]
---

If you are planning to deploy your Rails application using Capistrano 3, RVM, Unicorn, and Nginx then this is for you. You can follow these steps to help you automate your deployment to your cloud server. If you want to secure your server, you can read through [this](http://paoloibarra.com/2014/01/12/Securing-My-Cloud-Server/).

## Install RVM

Log in to your system and install RVM. RVM has a good installation guide which you can find in detail [here](https://rvm.io/rvm/install).

Or, you can just run this on your server.

    $ curl -sSL https://get.rvm.io | bash -s stable --ruby

To verify
    
    $ rvm version

    rvm 1.25.14 (stable) by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.com> [https://rvm.io/]

## Install Git

For Ubuntu:

    $ sudo apt-get install git

If you are running on CentOS

    $ sudo yum install git

To check whether it installed correctly
    
    $ git --version

    git version 1.8.3.2

## Setup Nginx

Nginx is a reverse proxy server. It basically serves all the client requests coming in to your server, and directs it to the appropriate backend server. In this case, your backend server would be Unicorn which hosts your Rails application.

Installing Nginx is simple with the package management tool. 

For Ubuntu

    sudo apt-get install nginx

For CentOS

    sudo yum install nginx

Once you have this installed, you will need to add ```default.conf```.

    $ sudo vim /etc/nginx/conf.d/default.conf

Add the following content. Make sure to edit it to match your configurations

    upstream unicorn {
      server unix:/tmp/unicorn.[APP_NAME].sock fail_timeout=0;
    }

    server {
      listen 80 default deferred;
      # server_name example.com;
      root [APP_DIRECTORY]/[APP_NAME]/current/public; # e.g. /var/apps/rails_blog/current/public

      location ^~ /assets/ {
        gzip_static on;
        expires max;
        add_header Cache-Control public;
      }

      location ~ ^/(robots.txt|sitemap.xml.gz)/ {
        root [APP_DIRECTORY]/[APP_NAME]/current/public; # e.g. /var/apps/rails_blog/current/public
      }

      try_files $uri/index.html $uri @unicorn;
      location @unicorn {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_pass http://unicorn;
      }

      error_page 500 502 503 504 /500.html;
      client_max_body_size 4G;
      keepalive_timeout 10;
    }    

After adding this config, try to start nginx

    sudo service nginx start

You can check whether nginx is running correctly

    $ ps aux|grep nginx

You should see nginx listed in the result.

## Setup your App

### Configure Unicorn in your Rails application

Add the ```unicorn``` gem into your ```Gemfile```

    $ vim Gemfile
    
    gem 'unicorn'

Then add the unicorn configuration:

    $ vim config/unicorn/production.rb

    root = "[APP_DIRECTORY]/[APP_NAME]/current" # e.g. /var/apps/rails_blog/current
    working_directory root

    pid "#{root}/tmp/pids/unicorn.pid"

    stderr_path "#{root}/log/unicorn.log"
    stdout_path "#{root}/log/unicorn.log"

    worker_processes 8 # update this with your preference
    timeout 30
    preload_app true

    listen '/tmp/unicorn.[APP_NAME].sock', backlog: 64

    before_fork do |server, worker|
      Signal.trap 'TERM' do
        puts 'Unicorn master intercepting TERM and sending myself QUIT instead'
        Process.kill 'QUIT', Process.pid
      end

      defined?(ActiveRecord::Base) and
        ActiveRecord::Base.connection.disconnect!
    end

    after_fork do |server, worker|
      Signal.trap 'TERM' do
        puts 'Unicorn worker intercepting TERM and doing nothing. Wait for master to send QUIT'
      end

      defined?(ActiveRecord::Base) and
        ActiveRecord::Base.establish_connection
    end

    # Force the bundler gemfile environment variable to
    # reference the capistrano "current" symlink
    before_exec do |_|
      ENV['BUNDLE_GEMFILE'] = File.join(root, 'Gemfile')
    end

### Add Capistrano 3 to your app

Add the gem

    $ vim Gemfile

    group :development do
      gem 'capistrano-rails'
      gem 'capistrano-rvm'
      gem 'capistrano3-unicorn'
    end

Generate the necessary Capistrano files

    $ bundle exec cap install

Update the generated Capfile
    
    $ vim Capfile
    
    require 'capistrano/setup'
    require 'capistrano/deploy'
    require 'capistrano/rvm'
    require 'capistrano/bundler'
    require 'capistrano/rails/assets'
    require 'capistrano/rails/migrations'
    require 'capistrano3/unicorn'

    # Load custom tasks from `lib/capistrano/tasks' if you have any defined
    Dir.glob('lib/capistrano/tasks/*.rake').each { |r| import r }    

Update deploy.rb

    $ vim config/deploy.rb

    lock '3.3.5'

    set :application, [APP_NAME]
    set :repo_url, 'git@github.com:[GITHUB_ACCOUNT_NAME]/[APP_NAME].git' # change this to your git server

    ask :branch, proc { `git rev-parse --abbrev-ref HEAD`.chomp }.call

    set :use_sudo, false
    set :bundle_binstubs, nil
    set :linked_files, fetch(:linked_files, []).push('config/database.yml')
    set :linked_dirs, fetch(:linked_dirs, []).push('log', 'tmp/pids', 'tmp/cache', 'tmp/sockets', 'vendor/bundle', 'public/system')

    after 'deploy:publishing', 'deploy:restart'

    namespace :deploy do
      task :restart do
        invoke 'unicorn:stop'
        invoke 'unicorn:reload'
      end

      task :stop do
        invoke 'unicorn:stop'
      end
    end

Update capistrano production.rb

    $ vim config/deploy/production.rb

    set :port, 22
    set :user, [username] # e.g. 'deploy'
    set :deploy_via, :remote_cache
    set :use_sudo, false
    set :branch, [BRANCH_TO_DEPLOY] # e.g. 'master'

    server [HOST_IP], # e.g. 192.xxx.xxx.xxx
      roles: [:web, :app, :db],
      port: fetch(:port),
      user: fetch(:user),
      primary: true

    set :deploy_to, "[APP_DIRECTORY]/#{fetch(:application)}" # e.g. "/var/apps/rails_blog"

    set :ssh_options, {
      forward_agent: true,
      auth_methods: %w(publickey),
      user: 'deployer',
    }

    set :rails_env, :production
    set :conditionally_migrate, true

## Setup Git Server

To make sure that the deployment script runs smoothly, you need to add your server's pub key to your git server. 

Generate your pub key if you have not done so:

    ssh-keygen -t rsa -C "[EMAIL_ADDRESS]"

This will generate a public key: ```~/.ssh/id_rsa.pub```

Copy the content of this file onto your git server profile.

## Verify deploy

Once everything has been setup, try to verify everything by running this

    cap production deploy:check

If everything runs smoothly, then you are more or less set.

## Deploy application

To deploy this in production
    
    cap production deploy

That should deploy your app in production. Once you access the IP address of your server on a browser, you should be greeted by your app's home page.

Many thanks to [Chuck J Hardy](https://gist.github.com/ChuckJHardy/f44dda5f94c6bbdba9a4) for his guide which helped me tremendously.