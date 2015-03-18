---
layout: post
title: "Using Swift in an iOS Application to Upload an Image to a RESTful API"
description: "Using Swift in iOS app to Upload an Image to a RESTful API"
tags: [swift, ios]
---

Last time, I wrote a post about how to create a [RESTful API using Rails and Paperclip](http://jpibarra1130.github.io/2014/09/27/Image-Upload-Using-Rails-API-And-Paperclip/). The second part of my task is to figure out a way on how to upload an image taken from my phone's Camera using Swift.

To send the request, I used NSMutableURLRequest to handle the JSON request

    var request = NSMutableURLRequest(URL: NSURL(string: url))
    request.HTTPMethod = "POST"
    request.addValue("application/json", forHTTPHeaderField: "Content-Type")
    request.addValue("application/json", forHTTPHeaderField: "Accept")

To prepare the HTTPBody, I had to encode the image to a Base64 string. This is done through the following:

    var imageData = UIImageJPEGRepresentation(image)
    var base64String = imageData.base64EncodedStringWithOptions(NSDataBase64EncodingOptions.fromRaw(0)!) // encode the image

After doing this, I've put the encoded image into a Dictionary in the appropriate parameter. I serialized it into a JSON representation which I assigned to the HTTPBody.

    var err: NSError? = nil
    var params = ["image":[ "content_type": "image/jpeg", "filename":"test.jpg", "file_data": base64String]]
    request.HTTPBody = NSJSONSerialization.dataWithJSONObject(params, options: NSJSONWritingOptions(0), error: &err)!

I used the shared session object to create a task to send the request. I created a request model that I can convert to a MutableURLRequest. You can see how it looks like [here](https://gist.github.com/jpibarra1130/f163e0e07a3417983059).

    var session = NSURLSession.sharedSession()
    var task = session.dataTaskWithRequest(request.toMutableURLRequest(), completionHandler: { data, response, error -> Void in
        var strData = NSString(data: data, encoding: NSUTF8StringEncoding)
        var err: NSError?

        // process the response
    })

    task.resume() // this is needed to start the task

 After the request has completed, the block I passed via the completionHandler will be executed. From there, I was able to evaluate the response. So that's how I was able to send the image taken from my phone's camera to the server.

 ***UPDATE: I modified the post to use UIImageJPEGRepresentation instead of UIImagePNGRepresentation as suggested by Paul Senzee. I also added more detail to make the sample code clearer.***