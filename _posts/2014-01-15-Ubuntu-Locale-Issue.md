---
layout: post
title: "Ubuntu: Locale Issue"
---

I was encountering a locale issue on my Ubuntu server earlier. The error looks something like this:

    unsupported locale setting
    Traceback (most recent call last):
      File "/usr/lib/python3/dist-packages/CommandNotFound/util.py", line 24, in crash_guard
        callback()
      File "/usr/lib/command-not-found", line 69, in main
        enable_i18n()
      File "/usr/lib/command-not-found", line 40, in enable_i18n
        locale.setlocale(locale.LC_ALL, '')
      File "/usr/lib/python3.3/locale.py", line 541, in setlocale
        return _setlocale(category, locale)
    locale.Error: unsupported locale setting

This was caused by these missing locale settings: LC_ALL and LANG. One way to solve this is by adding an export line for these variables on ~/.bash_profile or ~/.bashrc file.

    export LC_ALL=en_US.UTF-8
    export LANG=en_US.UTF-8

But because I don't want to be doing this for all my (future) users, I had to look for a config file which would have a system-wide effect.

I found out about [/etc/environment](https://help.ubuntu.com/community/EnvironmentVariables#A.2BAC8-etc.2BAC8-environment) which is used for system-wide environment setting. I managed to solve the issue by appending these in that file.

    LC_ALL=en_US.UTF-8
    LANG=en_US.UTF-8

