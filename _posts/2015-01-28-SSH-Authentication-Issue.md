---
layout: post
title: "SSH Authentication Issue"
description: "SSH Authentication Issue"
tags: [ssh, rsa authentication, pubkey, error]
---

Last week, I was setting up a passwordless ssh in one of our servers. I've already updated the necessary configuration in ```/etc/ssh/sshd_config```

When I checked the log

    $ sudo tail -f secure audit/audit.log

I noticed this error:

    Authentication refused: bad ownership or modes for file /home/deployer/.ssh/authorized_keys

After checking, I found out that this has something to do with the group permission on my ssh files. To fix this, I changed the ownership of the folder and file to my user account. 

    $ chmod 700 ~/.ssh # owner can read and write
    $ chmod 600 ~/.ssh/authorized_keys # owner can read, write and execute

Modifying the permission in the file fixed my issue. I was able to get in without having to key-in the password.