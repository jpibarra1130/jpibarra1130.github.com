---
layout: post
title: "Enabling Cron In Your Docker Container"
description: "How to enable cron in your Docker Container"
tags: ["cron", "docker"]
---

I was working on a task a couple of days ago where I needed to enable cron in my docker container. I was using a slimmed down image of ubuntu which does not have `cron` included out-of-the-box. I had to install a couple of things to make `cron` work on my docker container.

I modified my `Dockerfile` to look like something below:

      RUN apt-get install -y sudo # needed this because I didn't have sudo installed yet
      RUN apt-get install -y cron # this is to install cron
      RUN apt-get install -y rsyslog # for logging cron
      RUN echo "cron.* /var/log/cron.log" >> /etc/rsyslog.conf # to enable cron logs via rsyslog

      RUN echo "<username> ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers # I need sudo rights for the user I have in my docker container

      ENTRYPOINT exec /usr/local/config/run.sh # bashscript entrypoint - you can put it wherever you want as long as it's available in the image


I needed to run a couple of commands that's why I used a bash script to serve as my container's `ENTRYPOINT`.

My `run.sh` looks like this:
      #!/bin/bash

      expected_env='prod' # what is the environment you want the cron to work

      if [ $ENV == $expected_env ]; then
        echo "Setup cron"
        sudo service rsyslog start

        crontab -u <username> <path_to_crontab_file> # crontab -u paoloibarra /usr/local/config/crontab
        sudo service cron start
      else
        echo "Skip cron: environment is $ENV"
      fi

      # ... other things you need to start your app

I've added a checker in my script because I only wanted rsyslog, and cron to run on production.

My crontab file looks like this.

      */1 * * * * echo "job every minute" # please make sure that you have one empty line after this



This should make cron work on your docker image. Please note that if you want this to work on `docker-compose`, you just need to pass in ENV as prod, or remove the restriction for the environment in the `ENTRYPOINT` script.

