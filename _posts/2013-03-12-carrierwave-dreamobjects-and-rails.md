---
layout: default
title: Using CarrierWave with DreamObjects in Rails
---

I just started using [DreamObjects](https://www.dreamhost.com/cloud/storage/) for cloud storage. It is compatible with most [Amazon S3](https://aws.amazon.com/s3/) implementations, but at the moment it is about 20% of the price.

I needed to use it with [CarrierWave](https://github.com/jnicklas/carrierwave), which is a Ruby gem for uploading files. It natively supports S3, but needed some extra configuration to work properly with DreamObjects. This is what I did to get CarrierWave, DreamObjects, and Rails working together correctly.

Obviously you will need a DreamObjects account and a Rails app with CarrierWave installed.

I needed to create the file `config/initializers/carrierwave.rb`, but I am led to believe that in some cases it already exists.

I used the following for the contents of the newly-created file:

```ruby
CarrierWave.configure do |config|
  config.fog_credentials = {
    provider: "AWS",
    aws_access_key_id: "<YOUR ACCESS KEY>",
    aws_secret_access_key: "<YOUR SECRET KEY>",
    connection_options: { ssl_version: :TSLv1 }, # SSLv3 gets a handshake error on DreamObjects
    host: 'objects.dreamhost.com'
  }
  config.fog_directory = '<YOUR BUCKET NAME>.objects.dreamhost.com'
  config.fog_public = true # Change to false if you don't want files to be public.
  config.fog_authenticated_url_expiration = 600
  config.asset_host = "http://<YOUR BUCKET NAME>.objects.dreamhost.com"
end
```


Also, in my Uploader file, I changed `storage :file` to `storage :fog`.

That was it! Seems pretty simple, but it took me ages to figure out due to the lack of relevant documentation.
