---
layout: post
title: "Image Upload Using Rails API and Paperclip"
description: "Image uploading using a RESTful API via Rails and Paperclip"
---

I am working on a mini-project with my friends and part of it requires us to make a RESTful API. We opted to use Ruby on Rails for the backend since we have a tight schedule and using this stack would simplify the development. For my first task, I need to create an endpoint which will allow me to upload an image together with the object's properties.

I used [Paperclip](https://github.com/thoughtbot/paperclip) to help manage the image file. I was stumped on how to accomplish this task for a while because this was the first time I'd be writing an endpoint that allowed me to upload an image. After researching for a while, I saw this [blog](http://blag.7tonlnu.pl/blog/2014/01/22/uploading-images-to-a-rails-app-via-json-api/) which showed me how to upload a file.

The main idea is to encode the file to Base64, and set the correct attributes (content-type, filename) so we can parse it in the server-side correctly.

A sample request would look something like this:

    /api/v1/items/create.json

Request Payload:

    {
      "image":
        {
          filename: "original_filename.jpg",
          content: "<Base64Encoded data>",
          content_type: "<image content type>" # e.g. image/jpeg
        }
    }


Your controller should be able to process this.

    class API::V1::ItemsController < ApplicationController

      def create
        result = { status: "failed" }

        begin
          params[:image] = parse_image_data(params[:image]) if params[:image]
          item = Item.new
          item.image = params[:image]

          if item.save
            result[:status] = "success"
          end
        rescue Exception => e
          Rails.logger.error "#{e.message}"
        end

        render json: result.to_json
      ensure
        clean_tempfile
      end

      # This part is actually taken from http://blag.7tonlnu.pl/blog/2014/01/22/uploading-images-to-a-rails-app-via-json-api. I tweaked it a bit by manually setting the tempfile's content type because somehow putting it in a hash during initialization didn't work for me.
      def parse_image_data(image_data)
        @tempfile = Tempfile.new('item_image')
        @tempfile.binmode
        @tempfile.write Base64.decode64(image_data[:content])
        @tempfile.rewind

        uploaded_file = ActionDispatch::Http::UploadedFile.new(
          tempfile: @tempfile,
          filename: image_data[:filename]
        )

       uploaded_file.content_type = image_data[:content_type]
        uploaded_file
      end

      def clean_tempfile
        if @tempfile
          @tempfile.close
          @tempfile.unlink
        end
      end
    end

So there you have it, a controller that handles image upload.