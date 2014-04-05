---
layout: post
title: "Retrieving Vimeo ID"
---

I have a usecase in my side project wherein I needed to get the thumbnail for a vimeo video. Getting this is relatively easy through vimeo's [Video Request API](http://developer.vimeo.com/apis/simple#video-request).

I think it's trickier to extract the video ID from the vimeo URL. There are multiple valid Vimeo URLs. Based on this [link](http://stackoverflow.com/questions/10488943/easy-way-to-get-vimeo-id-from-a-vimeo-url), here are a few of the valid links:

> http://vimeo.com/12345
> http://player.vimeo.com/video/12345
> http://player.vimeo.com/video/12345?test=value1
> http://player.vimeo.com/video/12345/?test=value1
> http://vimeo.com/channels/vimeogirls/12345
> http://vimeo.com/groups/shortfilms/videos/12345

I tailored this [gist](https://gist.github.com/wwdboer/4943672) by wwdboer in [Ruby](https://gist.github.com/jpibarra1130/9993586) to extract the correct id from a given vimeo URL.

    def self.vimeo_id(url)
      match = url.match(/https?:\/\/(?:[\w]+\.)*vimeo\.com(?:[\/\w]*\/?)?\/(?<id>[0-9]+)[^\s]*/)
      match[:id] if match.present?
    end

This method will return the id if present, and ```nil``` if not.