---
layout: post
title: "'Unsupported locale setting' when using iTerm"
description: "'Unsupported locale setting' when using iTerm"
tags: ["locale", "iterm"]
---

While I was on one of our servers last week, I encountered this issue:

  {% highlight bash %}
  $ env/bin/pip freeze
  Traceback (most recent call last):
    File "env/bin/pip", line 11, in <module>
      sys.exit(main())
    File "/opt/DummyProject/env/lib/python2.7/site-packages/pip/__init__.py", line 215, in main
      locale.setlocale(locale.LC_ALL, '')
    File "/opt/DummyProject/env/lib/python2.7/locale.py", line 539, in setlocale
      return _setlocale(category, locale)
  locale.Error: unsupported locale setting
  {% endhighlight %}

I checked the server and the locale setting is properly setup there. The weird thing is, I was the only one encountering this in my team. After a little googling, I found out that this was because of my locale setting was getting imported. I just needed to disable this in my iTerm to prevent it from happening. To do this:


![Iterm Locale](/assets/images/2016-04-24-locale-iterm.png)


That's it. It's working after that.
