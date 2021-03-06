# CarrierWave for Mongoid

This gem adds support for Mongoid and MongoDB's GridFS to [CarrierWave](https://github.com/jnicklas/carrierwave/)

This functionality used to be part of CarrierWave but has since been extracted into this gem.

## Installation

    gem install carrierwave-mongoid

## Requiring the gem

    require 'carrierwave/mongoid'

## Using Bundler

    gem 'carrierwave-mongoid', :require => 'carrierwave/mongoid'

## Using MongoDB's GridFS store

You'll need to configure the database and host to use:

```ruby
CarrierWave.configure do |config|
  config.grid_fs_database = 'my_mongo_database'
  config.grid_fs_host = 'mongo.example.com'
end
```

The defaults are `carrierwave` and `localhost`.

And then in your uploader, set the storage to `:grid_fs`:

```ruby
class AvatarUploader < CarrierWave::Uploader::Base
  storage :grid_fs
end
```

Since GridFS doesn't make the files available via HTTP, you'll need to stream
them yourself. In Rails for example, you could use the `send_data` method. You
can tell CarrierWave the URL you will serve your images from, allowing it to
generate the correct URL, by setting eg:

```ruby
CarrierWave.configure do |config|
  config.grid_fs_access_url = "/image/show"
end
```

## Changes from earlier versions of CarrierWave with Mongoid support built in

This version supports ONLY version of mongoid ~> 2.1

You can use `upload_identifier` to retrieve the original name of the uploaded file.

The default mount column used to be the name of the upload column plus  `_filename`. Now it is simply the name of the column. Most of the time, the column was called `upload`, so it would have been mounted to `upload_filename`.
If you'd like to avoid a database migration, simply use the `:mount_on` option to specify
the field name explicitly. Therefore, you only have to add a `_filename` to your column name. For example, if your column is called `:upload`:

```ruby
class Dokument
  mount_uploader :upload, DokumentUploader, mount_on: :upload_filename
end
```

## Known issues and limitations

It is recommended that you disable Mongoid's auto_validation on the mounted column.

Note that embedded documents files aren't saved when parent documents are saved.
You must explicitly call save on embedded documents in order to save their attached files.
You can read more about this [here](https://github.com/jnicklas/carrierwave/issues#issue/81)
