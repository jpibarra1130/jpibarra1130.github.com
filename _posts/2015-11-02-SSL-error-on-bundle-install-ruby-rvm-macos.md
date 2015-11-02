---
layout: post
title: "SSL Error on bundle install in Ruby for RVM Users"
description: "SSL Error on bundle install in Ruby for RVM Users"
tags: [ruby, rvm, bundle, bundler, ssl issue, ssl]
---

I encountered a weird error the other day when I tried to run a ```bundle install``` command in one of my Ruby on Rails project. The error looks like this:

[!] There was an error parsing `Gemfile`: SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed. Bundler cannot continue.

         #  from <repository>:5
         #  -------------------------------------------
         #  require 'open-uri'
         >  versions = JSON.parse(open('https://pages.github.com/versions.json').read)
         #
         #  -------------------------------------------


I stumbled upon this [issue](https://gist.github.com/luislavena/f064211759ee0f806c88) while I was looking for the solution. Unfortunately, that didn't work for me. I saw another solution that worked for my case in [stackoverflow](https://stackoverflow.com/questions/19150017/ssl-error-when-installing-rubygems-unable-to-pull-data-from-https-rubygems-o/27298259). This is really helpful if you are using RVM in your Mac OS.

What I did to make it work is to simply do the following:

        $ rvm get stable
        $ sudo rvm osx-ssl-certs update all
        $ rvm rubygems latest

After this, I could start running ```bundle install``` properly again.
