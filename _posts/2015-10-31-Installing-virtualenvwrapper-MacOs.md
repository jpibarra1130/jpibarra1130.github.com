---
layout: post
title: "Installing virtualenvwrapper in MacOS"
description: "Installing virtualenvwrapper in mac os"
tags: [mac, virtualenv, virtualenv, python, python3]
---

I'm about to use Python in one of my projects. Because of this, I've been reading about some of the technologies commonly used together with Python. One of these is ```virtualenv```.

[***virtualenv***](http://docs.python-guide.org/en/latest/dev/virtualenvs/) is something similar to Ruby's [RVM](https://rvm.io/). In a nutshell, it is used to isolate the different dependencies used by your projects so they don't interfere with each other. I decided to use [***virtualenvwrapper***](https://virtualenvwrapper.readthedocs.org/en/latest/). This is a wrapper for ```virtualenv``` to make operations like deleting, copying, creating a virtual environment simpler.

I currently have two installations of Python - Python 2.7 and Python 3.5. I wanted to install this via Python 3.

I was following this [tutorial](http://www.simononsoftware.com/virtualenv-tutorial-part-2/) to help me get started. These are the steps I did to install virtualenvwrapper on my Mac OS machine.


- pip3 install virtualenvwrapper
- mkdir ~/.virtualenvs


We need to set the ```WORKON_HOME``` variable needed by ```virtualenv``` to work. In my case, since I'm putting it in ~/.virtualenvs, I added this in my ```~/.bash_profile```

    export WORKON_HOME=$HOME/.virtualenvs

You need to source the virtualenv wrapper script to activate it. If you're not sure where it is, you can simply use the ```which``` command like ```which virtualenvwrapper_lazy.sh```

    source /Library/Frameworks/Python.framework/Versions/3.5/bin/virtualenvwrapper_lazy.sh

I'm using iTerm2 and my terminal crashes, so instead of using the lazy script, I just use the virtualenvwrapper script. I replaced the above line to:

    source /Library/Frameworks/Python.framework/Versions/3.5/bin/virtualenvwrapper_lazy.sh

To check whether virtualenv works for you, you can try to create your own virtual environment

    mkvirtualenv test

And if it succeeds, you have successfully install virtualenvwrapper in your machine.

Just to add on this, I encountered an additional issue where I'm getting this error:

    /usr/bin/python: No module named virtualenvwrapper

I managed to fix it thanks to [this thread in Stackoverflow](http://stackoverflow.com/questions/23997403/installed-virtualenv-and-virtualenvwrapper-python-says-no-module-named-virtuale). I had to set some additional variables so that the script would work correctly.

    export VIRTUALENVWRAPPER_SCRIPT=/Library/Frameworks/Python.framework/Versions/3.5/bin/virtualenvwrapper.sh
    export VIRTUALENVWRAPPER_PYTHON=/Library/Frameworks/Python.framework/Versions/3.5/bin/python3
    export VIRTUALENVWRAPPER_VIRTUALENV=/Library/Frameworks/Python.framework/Versions/3.5/bin/virtualenv
    export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--no-site-packages'

After setting this up, my final ```.bash_profile``` looks something like this:

    export WORKON_HOME=$HOME/.virtualenvs
    export PROJECT_HOME=$DEV_PATH/workspace
    export VIRTUALENVWRAPPER_SCRIPT=/Library/Frameworks/Python.framework/Versions/3.5/bin/virtualenvwrapper.sh
    export VIRTUALENVWRAPPER_PYTHON=/Library/Frameworks/Python.framework/Versions/3.5/bin/python3
    export VIRTUALENVWRAPPER_VIRTUALENV=/Library/Frameworks/Python.framework/Versions/3.5/bin/virtualenv
    export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--no-site-packages'

    source /Library/Frameworks/Python.framework/Versions/3.5/bin/virtualenvwrapper.sh

If you're not sure what to put in some of the variables there, you can simply use ```which```. Hope this helps!