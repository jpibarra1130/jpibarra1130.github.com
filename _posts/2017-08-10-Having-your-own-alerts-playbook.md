---
layout: post
title: "Adding an Alerts Playbook"
description: "Why it's important to have a squad alerts playbook."
tags: ["playbook", "alerts", "alerts playbook", "process"]
---

Each week, we have someone on our squad assigned to be the green flag. This person is in charge of handling issues in our services while the rest of the squad is focused on working on our delivery for the sprint. The problem is our squad handles multiple services, and sometimes, it becomes challenging for the green flag to fix an issue on a service he or she hardly touches.

A suggestion from one of my squad-mates to help solve this was to start having an Alerts Playbook.

***What is an Alerts Playbook?***

This is a section on our wiki which contains information on the alerts that show up on our notification. It has a page for every alert that we have on our system. Each alert page has the following details:

* Alert Message

  This is typically the message that shows up on our alerts channel.
  Severity
  The classification of the alert. Is it a critical issue that has to be fixed ASAP or an error that can wait until we are back in the office?

* Where is this triggered?

  I would normally link the particular code that triggered the alert in this section. As a developer, I find it easier to understand the alert by looking at the particular code that triggered it.

* Impact
  
  A description of what the impact of the issue is to the users of our services.

* How to investigate and fix?
  
  This contains relevant information that will help the person reading the playbook to debug or fix the issue. I would put in details like where is the log located, or which server to check out. If I’m faced with an issue, I hate having to go on a hunt for the server information so having this info on hand saves me some time.


The process is simple. If you are going to add an alert to the system, you will have to create this page, and put in the relevant details. Ideally, we would ask one of our squad-mates to read through the page we have just added to see whether it is understandable.

Some benefits I noticed by adding this

* I only add necessary alerts. One of the things that had bogged our squad before was the number of alerts popping up on our notification. Putting too many alerts can result to alert fatigue. By filling in those details on the wiki, I become more critical of the alerts that I’m adding to our system. I ask myself whether it really is necessary to have that alert.

* I get the confidence to know that I have a place to check out when something bad happens and I am the one who’s supposed to fix it.

* Knowledge is shared. I am able to impart some of the things that only I used to know with the rest of the squad by documenting it here. And if I am not around, I know that my squad-mates will be able to solve the issues I used to solve by myself.


So there you have it. A simple tweak we added in our process to help boost the effectiveness of our squad. Hope this helps!