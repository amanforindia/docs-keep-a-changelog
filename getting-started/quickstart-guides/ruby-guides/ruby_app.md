---
description: >-
  Real-time image resizing, automatic optimization, and file uploading in Ruby
  application using ImageKit.io.
---

# Ruby Application

This is a quick start guide to show you how to integrate ImageKit in the Ruby application. The code samples covered here are hosted on Github -  [https://github.com/imagekit-samples/quickstart/tree/master/ruby/ruby_app](https://github.com/imagekit-samples/quickstart/tree/master/ruby/ruby_app).

This guide walks you through the following topics:

* [Setting up ImageKit Ruby SDK](ruby_app.md#setting-up-imagekit-ruby-sdk)
* [Upload images](ruby_app.md#uploading-images-in-ruby-app)
* [Rendering images](ruby_app.md#rendering-images-in-ruby-app)
* [Applying common image manipulations](ruby_app.md#common-image-manipulation-in-ruby-app)
* [Secure signed URL generation](ruby_app.md#secure-signed-url-generation)
* [Adding overlays to images](ruby_app.md#adding-overlays-to-images-in-ruby-app)

## Setting up ImageKit Ruby SDK

We will create a fresh Ruby application for this tutorial and work with it.

First, we will install the imagekitio gem in our machine by executing the following command on your terminal.

```ruby
gem install imagekitio
```

It will download the latest version(ie. >= 2) of imagekitio sdk gem in our machine. Now let's create a new file `app.rb` and begin to write code in it.
Open the new `app.rb` file with your favorite editor.

We are going to require the gem `imagekitio` at the top of the file as follow:

```ruby
require 'imagekitio'
```

It loads the imagekitio gem in our application. Before the SDK can be used, let's learn about and configure the requisite authentication parameters that need to be provided to the SDK.
Open the `app.rb` file and add your public and private API keys, as well as the URL Endpoint as follows: (You can find these keys in the Developer section of your ImageKit Dashboard)

```ruby
public_key = 'your_public_key'
private_key = 'your_private_key'
url_endpoint = 'your_url_endpoint' # https://ik.imagekit.io/your_imagekit_id

imagekitio = ImageKitIo::Client.new(private_key, public_key, url_endpoint)
```

The imagekitio client is configured with user-specific credentials.

## **Uploading images in Ruby app**

There are different ways to upload the file in imagekitio. Let's upload the remote file to imagekitio using the following code:

```ruby
upload = imagekitio.upload_file(
    file: "https://file-examples.com/wp-content/uploads/2017/10/file_example_JPG_100kB.jpg",
    file_name: "testing",
    response_fields: 'tags,customCoordinates,isPrivateFile,metadata',
    tags: %w[abc def],
)
puts "upload remote file => ", upload
```

The output should like this:
```ruby
upload remote file => 
{:response=>{"fileId"=>"61a87b98dfsc0486be6e2741a", "name"=>"testing.jpg", "size"=>102117, "filePath"=>"/testing.jpg", "url"=>"https://ik.imagekit.io/demo/testing.jpg", "fileType"=>"image", "height"=>700, "width"=>1050, "thumbnailUrl"=>"https://ik.imagekit.io/demo/tr:n-media_library_thumbnail/testing.jpg", "tags"=>["abc", "def"], "AITags"=>nil, "isPrivateFile"=>true, "customCoordinates"=>nil, "metadata"=>{"height"=>700, "width"=>1050, "size"=>102117, "format"=>"jpg", "hasColorProfile"=>true, "quality"=>0, "density"=>72, "hasTransparency"=>false, "exif"=>{}, "pHash"=>"902d9df1fc74367"}}, :error=>nil}
```

Congratulation file uploaded successfully.

## **Rendering images in Ruby app**
Here, declare a variable to store the image URL generated by the SDK. Like this:

```ruby
image_url = imagekitio.url({
   path: 'default-image.jpg',
})
```

Now, `image_url` has the URL `https://ik.imagekit.io/<your_imagekit_id>/default-image.jpg` stored in it.
This fetches the image from the URL stored in `image_url`.

open the url on browser, it should now display this default image in its full size:

![Image in its original dimensions (1000px \* 1000px)](<../../../.gitbook/assets/ruby-app-default-image.png>)

## Common image manipulation in Ruby App

Let’s now learn how to manipulate images using [ImgeKit transformations](../../../features/image-transformations/).

### **Height and width manipulation**

To resize an image along with its height or width, we need to pass the `transformation` as an array to the `imagekitio.url()` method.

Let’s resize the default image to 200px height and width:

```ruby
image_url = imagekitio.url({
  path: 'default-image.jpg',
  transformation: [{
     height: "200",
     width: "200",
   }]
})
```

**Transformation URL:**

```http
https://ik.imagekit.io/violetviolinist/tr:h-200,w-200/default-image.jpg?ik-sdk-version=ruby-1.0.6
```

Refresh your browser with new url to get the resized image.

![Resized image (200px \* 200px)](<../../../.gitbook/assets/ruby-app-resized-image.png>)

### **Chained transformation**

[Chained transformations](https://docs.imagekit.io/features/image-transformations/chained-transformations) provide a simple way to control the sequence in which transformations are applied.

Let’s try it out by resizing an image, then rotating it:

```ruby
image_url = imagekitio.url({
  path: 'default-image.jpg',
  transformation: [{
    height: "300",
    width: "200",
  }]
})
```

**Transformation URL:**

```http
https://ik.imagekit.io/violetviolinist/tr:h-300,w-200/default-image.jpg?ik-sdk-version=ruby-1.0.5
```

**Output Image:**

![Resized and cropped (200px \* 300px)](<../../../.gitbook/assets/ruby-app-resized1-image.png>)

Now, rotate the image by 90 degrees.

```ruby
image_url = imagekitio.url({
  path: 'default-image.jpg',
  transformation: [
  {
    height: "300",
    width: "200",
  },
  {
    rt: 90,
  }],
})
```

**Chained Transformation URL:**

```http
https://ik.imagekit.io/violetviolinist/tr:h-300,w-200:rt-90/default-image.jpg?ik-sdk-version=ruby-1.0.5
```

**Output Image:**

![Resized, then rotated](<../../../.gitbook/assets/ruby-app-resized-rotate-image.png>)

Let’s flip the order of transformation and see what happens.

```ruby
image_url = imagekitio.url({
  path: 'default-image.jpg',
  transformation: [
  {
    rt: 90,
  },
  {
    height: "300",
    width: "200",
  }],
})
```

**Chained Transformation URL:**

```http
https://ik.imagekit.io/violetviolinist/tr:rt-90:h-300,w-200/default-image.jpg?ik-sdk-version=ruby-1.0.5
```

**Output Image:**

![Rotated, then resized](<../../../.gitbook/assets/ruby-app-rotate-resized-image.png>)

## Adding overlays to images in Ruby App

ImageKit.io allows you to add [text](../../../features/image-transformations/overlay.md#text-overlay) and [image overlay](../../../features/image-transformations/overlay.md) dynamically.

#### **Text overlay**

Text overlay can be used to superimpose text on an image. For example:

```ruby
image_url = imagekitio.url({
  path: 'default-image.jpg',
  transformation: [{
    height: "300",
    width: "300",
    overlay_text: 'ImageKit',
    overlay_text_font_size: 50,
    overlay_text_color: '0651D5',
  }],
})
```

**Transformation URL:**

```http
https://ik.imagekit.io/violetviolinist/tr:h-300,w-300,ot-ImageKit,ots-50,otc-0651D5/default-image.jpg?ik-sdk-version=ruby-1.0.5
```

**Output Image:**

![Text Overlay (300px \* 300px)](<../../../.gitbook/assets/ruby-app-with-overlay-image.png>)

#### **Image overlay**

Image overlay can be used to superimpose an image on another image. For example, we will upload a while logo image on [this link](https://ik.imagekit.io/demo/logo-white\_SJwqB4Nfe.png) into our account and use it for the overlay image.

Base Image: `default-image.jpg`

Overlay Image: `overlay-image.png`

```ruby
image_url = imagekitio.url({
  path: 'default-image.jpg',
  transformation: [{
    height: "300",
    width: "300",
    overlay_image: "overlay-image.png"
  }],
})
```

**Transformation URL:**

```http
https://ik.imagekit.io/violetviolinist/tr:h-300,w-300,oi-overlay-image.png/default-image.jpg?ik-sdk-version=ruby-1.0.6
```

**Output Image:**

![Overlay image over another image](<../../../.gitbook/assets/ruby-app-with-overlay-image.png>)

## Secure signed URL generation

You can use the SDK to generate a signed URL of an image, that expires in a given number of seconds.

```ruby
signed_image_url = imagekitio.url({
  path: 'default-image.jpg',
  signed: true,
  expire_seconds: 10,
})
```

The above snippets create a signed URL with an expiry time of 10 seconds.

![Signed URL generation](<../../../.gitbook/assets/ruby-app-with-signed-image.png>)

## **What's next**

The possibilities for image manipulation and optimization with ImageKit are endless. Learn more about it here:&#x20;

* [Image Transformations](https://docs.imagekit.io/features/image-transformations)
* [Image optimization](https://docs.imagekit.io/features/image-optimization)
* [Media Library](https://docs.imagekit.io/media-library/overview)
* [Performance monitoring](../../features/performance-monitoring.md)