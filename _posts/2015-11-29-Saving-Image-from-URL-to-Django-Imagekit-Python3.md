---
layout: post
title: "Download and Save Image into Model using django-imagekit and Python 3"
description: "Download and Save Image into Model using django-imagekit and Python 3"
tags: [python3, python, django-imagekit, save image]
---

I am currently working on a project where I need my model to have an avatar. I am using Python 3 and Django for this project. The thing is, I needed some data for this project. So I decided to create a script that will read the data from Wikipedia's API and save it in my database.

I am currently using [django-imagekit](https://github.com/matthewwithanm/django-imagekit) to handle the image processing for my model's avatar. My model looks something like this:

{% highlight python %}

from django.db import models
from imagekit.models import ImageSpecField
from imagekit.processors import ResizeToFill

class Person(models.Model):
    avatar = models.ImageField(upload_to='avatars', default='')
    avatar_thumbnail = ImageSpecField(source='avatar',
                                      processors=[ResizeToFill(300, 200)],
                                      format='JPEG',
                                      options={'quality': 80})

{% endhighlight %}


With Wikipedia's API, I can somehow get the image URL for the article I'm looking for. My problem was how would I be able to save the image from the image URL. It took me a while to do this. The closest I found was this answer from [Stackoverflow](http://stackoverflow.com/a/6566011/2264354). I had to modify it because I am using Python 3 in my project.


{% highlight python %}

from django.core.files.base import ContentFile
from io import BytesIO
from urllib.request import urlopen

input_file = BytesIO(urlopen(img_url).read()) # img_url is simply the image URL

person = Person.objects.create()
person.avatar.save("image.jpg", ContentFile(input_file.getvalue()), save=False)
person.save()

{% endhighlight %}


I streamed the image using the builtin [`BytesIO`](https://docs.python.org/3/library/io.html#binary-i-o) object in Python 3 and put it onto Django's [`ContentFile`](https://docs.djangoproject.com/en/1.8/ref/files/file/#the-contentfile-class) before finally saving it in the Person object.

This worked well for me. I am not sure whether there's a better way of doing this but if there is, I would love to know!