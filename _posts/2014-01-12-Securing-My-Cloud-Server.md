---
layout: post
title: "Securing My Cloud Server"
description: "How to secure a private cloud server"
tags: [sysadmin]
---

I'm finally on the deployment stage of my app. I have been working on this application on and off for a few months now. I was hoping to deploy it on January 1 but an impromptu trip stopped me from doing so. But nevertheless, here I am and deploying it.

I was thinking of using Heroku because of the ease of deployment. But I wanted to try my hand in setting up my own cloud server that was why I ended up getting my own remote server. I ended up choosing [SimplerCloud](http://www.simplercloud.com) as per the recommendation of a former colleague. It would make sense for me to use it since my target audience is in SEA, and because the machine is located in Singapore it will lessen the latency. I ordered the Extra Small with 1GB Ram, 20GB SSD, 1 Core which was running on Ubuntu Saucy (13.10).

Once my server was up and running, my first task was to make it secure. I ran across this [article](http://robmclarty.com/blog/how-to-setup-a-production-server-for-rails-4) before, and decided to revisit it to help me secure my server. Here are a few steps of what I did to accomplish this.

For SimplerCloud, you will need to login to the server as root@xxx.xxx.xxx.xxx supplying the generated password coming from the dashboard.

The moment I was in the server, I proceeded to creating a new user

    sudo adduser paolo

Afterwhich, I granted the new user sudo rights

    sudo usermod -aG sudo paolo

Once the user has sudo rights, I went inside the user's home folder and copied my machine's public key

    /home/paolo/.ssh/authorized_keys


After creating a new user, my next stop was to edit the sshd_config.

**Before I started editing, I created a backup of the sshd_config.** This is in the event that if something goes wrong then I can always revert back to the old config.

    cp /etc/ssh/sshd_config > /etc/ssh/sshd_config.bak

After creating the backup, I started editing the file by disallowing users to directly ssh as root. I disabled this by changing *PermitRootLogin* to *no*

    PermitRootLogin no

Another thing I did was to change the port so that it will be harder for other people to get in my server. I changed it by modifying *Port* to a different one other than default, e.g. 8765

    Port 8765

Another precaution I did was to to allow only certain users to access my server. This can be set by specifying the users in *AllowUsers*. You can specify multiple users by separating each user with space.

    AllowUsers paolo deploy

After this, I enabled *RSAAuthentication* so that only those machines with keys specified in the user's authorized_keys file will be allowed to get in

    RSAAuthentication yes

After applying these changes, I restarted ssh by running:

    service ssh restart

**Don't exit the terminal yet.** To confirm whether I configured everything correctly, I decided to test it out by opening a new terminal. This is to see whether I would be able to get in using the port I specified.

    ssh -p 8765 paolo@xxx.xxx.xxx.xxx

Since my public key was in the authorized_keys file of user *paolo*, the server did not ask for my password anymore. Once I was in, I tried typing

    sudo su

Because I was able to change to the *root* user I think I have finished the first few steps to securing my server. Now, I can proceed with actually deploying my application!