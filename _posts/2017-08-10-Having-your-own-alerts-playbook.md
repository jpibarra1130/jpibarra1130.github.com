---
layout: post
title: "Adding an Alerts Playbook
description: "Why it's important to have a squad alerts playbook."
tags: ["playbook", "alerts", "alerts playbook", "process"]
---

Our squad has a rotating green flag person who is in charge of handling issues in our services when an alert gets triggered. The problem is it’s not that simple to know everything when your squad is handling multiple services. A suggestion from one of my squadmates was to introduce an Alerts Playbook.

***What is an Alerts Playbook?***

This is just a knowledge base for our squad to check what to do when an alert gets triggered. It’s basically a wiki page for every alert that pops up on our notification. Each alert page has the following details:

* Title
    * name of the alerts. I like to put in the exact message that shows up on our alert notification.
* Severity
    * critical / error. Critical is an issue that has to be fixed ASAP. Error is something that can wait until we are in the office.
* Where is this triggered
    * This is where we would normally link the particular code that triggered this alert. As a green flag, I find it easier to read the code that triggered the alert so I know what caused it.
* What the impact is
    * a description of what impact is to our users/services when this alert gets triggered.
* How to investigate and fix
    * Contains some steps or information that will help the person reading the playbook to debug or fix the problem. I would normally put in details like where is the log located, or which server to check out.

The process is simple. If you are going to add an alert to our system, you will have to create this page, and fill in all the details above. I would normally ask one of my squadmates to read through one that I just added to see whether it’s easy enough to understand. I also try to make it as concise as possible. 

Some benefits I noticed by adding this:

* I only add necessary alerts. One of the things that had bogged our squad was the number of alerts popping up on our notification. Putting too many alerts can result to alert fatigue. By filling in those details on the wiki, I become more critical of the alerts that I’m adding to our system. I ask myself whether it really is necessary to have that alert.
* I get the confidence to know where to look and what to do when I'm on green flag and an alert gets triggered. It’s good to know that when something bad happens, I can immediately act on the issue. When an issue happens, you’d definitely want to be fixing it as soon as you can.
* Knowledge is shared. This is the most important bit for me. If I'm on green flag, there is no excuse for me to relay the task to another teammate. It gives me a certain level of autonomy to solve the issue on my own.

This is a simple addition to our process which helped us a lot. Do you have any lightweight process you’ve done on your squad? Let me know! I would love to hear them.