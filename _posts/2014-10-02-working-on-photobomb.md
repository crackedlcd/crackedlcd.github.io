---
layout: post
title: Working On Photobomb - Serving Image Assets
date: 02 Oct 2014
excerpt: When I started Photobomb I wanted to work extensively with images. At the time images were more interesting to work with solely because user uploaded images should not exist inside the database, only the path to the image should be stored. However, the actual image needs to be stored *somewhere,*  whether it may be on the server or someplace else entirely. 
---

{{ page.title }}
================

**{{ page.date | date_to_string }}**

## Starting Photobomb

When I started Photobomb I wanted to work extensively with images. At the time images were more interesting to work with solely because user uploaded images should not exist inside the database, only the path to the image should be stored. However, the actual image needs to be stored *somewhere,*  whether it may be on the server or someplace else entirely. So when developing Photobomb I had two issues to initially solve:

1. Image uploading
2. Image storing

Image Uploading
---------------

Thanks to the Paperclip gem this was the trivial bit. All I needed to do was add `has_attached_file :content` to the Image model, and configure it accordingly.

The code is as follows:

{% highlight ruby %}

has_attached_file :content,
 :styles => { :thumb => "300x300>" }, :default_url => "/images/missing.jpg", 
 :path => "upload/:class/:id/:hash.:extension",
 :hash_secret => "thehashsecret"

{% endhighlight %}
The image filenames are generated through a hash so there wouldn't be any unforseen naming conflicts.

Image Storage
-------------

This is where things got interesting. Since Photobomb is hosted through Heroku it meant that image uploads should not be stored on the server directly, since the filesystem is read only. Thankfully, there is a really simple solution to this: Amazon S3.

With Paperclip setting up Photobomb to save user uploaded images to S3 is simple. All I needed to do is set `storage => :s3`,  set the `host_name` property to the S3 bucket's server (in my case it was `'s3-us-west-2.amazonaws.com`), and set the bucket property as `bucket => "bucketname"`.

However, there's a new issue that pops up. Amazon S3 is great for hosting images and assets, things like Javascript and CSS files, but **it is not meant to host and serve files, just to store them.** This means that pages render times are slow, and nothing kills a user's experience like slow load times. This is where Amazon Cloudfront comes in.

Asset Serving
-------------

Cloudfront to the rescue! Instead of serving and images and assets directly with S3, using Cloudfront means that the S3 bucket only has to serve images and assets to the edge locactions, which are meant to handle data transfers and requests. This made Photobomb load roughly **twice as fast.** That's quite an improvement. 