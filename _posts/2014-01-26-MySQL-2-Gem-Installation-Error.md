---
layout: post
title: "MySQL 2 Gem Installation Error"
description: "Fix the MySQL 2 Gem installation issue"
tags: [sysadmin, ruby, mysql]
---

I've encountered this error a few times before whenever I had a need to setup a new machine for my projects.

    An error occurred while installing mysql2 (0.3.14), and Bundler cannot continue.
    Make sure that `gem install mysql2 -v '0.3.14'` succeeds before bundling.

Normally, specifying the MySQL lib folder during the installation is enough to solve the issue.

    gem install mysql2 -v '0.3.14' -- --with-mysql-lib=/usr/lib/mysql/lib

The other week I encountered this again when I was deploying my application for the first time in my server. The solution above didn't solve the issue because I still encountered it whenever I had to redeploy. What worked for me to permanently solve this is by installing the *libmysqlclient-dev* package in my server.

    sudo apt-get install libmysqlclient-dev

Hopefully, I won't have to search around google when I encounter this again to save myself (or you) a few minutes.