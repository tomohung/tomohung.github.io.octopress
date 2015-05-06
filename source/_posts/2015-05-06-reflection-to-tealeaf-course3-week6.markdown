---
layout: post
title: "Reflection to Tealeaf Course3 Week6 (1/2)"
date: 2015-05-06 08:57:30 +0800
comments: true
categories: [tealeaf, rails]
---

These week we have two main part: [CarrierWave][] for uploading images as an admin, [Stripe][] for payments

[CarrierWave]: https://github.com/carrierwaveuploader/carrierwave
[Stripe]: http://stripe.com

#CarrierWave for uploading images as an Admin

###Admin

In order to controll user is a admin or not, the best practice is to build a admin its own self routes and controllers.

In `routes.rb`

```
namespace :admin do
  resources :todos, only: [:index, :destroy]
end
```

Then `rake routes`, we got

```
admin_todos  admin/todos  admin/todos#index
```

Build the controller file in the path: `/controllers/admin/todos_controller.rb`

```
class Admin::TodosController < ApplicationController
end
```

> It's aother rails convention for the path `/controllers/admin/` and class name `Admin::`.

Then add `admin` column to migration

`$ rails g migration add_admin_to_users`

```
add_column :users, :admin, :boolean
```

Rails will create a method for all boolean attributes: `admin?`

###Secure Access for different roles

Next, we can create a `AdminController` for all other controllers belongs to Admin.

```
class AdminController < ApplicationController
  before_filter :ensure_admin

  def ensure_admin
    if !current_user.admin?
      #do something
    end
  end
end
```

Then other controllers belong to Admin could be like this:

```
class Admin::TodoController < AdminController
  def index
    @todo = Todo.all
  end
end
```

###CarrierWave upload images to AWS S3

Install gem

```
gem 'carrierwave'
gem 'mini_magick'
```

Add column to stored migration, for example: videos

`$rails migration add_large_cover_to_videos`


```
add_column :videos, :large_cover, :string
```

In `models/video.rb`

```
mount_uploader :large_cover, LargeCoverUploader
```

Create `app/uploaders/large_cover_uploader.rb`, and use `mini_magick to resize image`

```
class LargeCoverUploader < CarrierWave::Uploader::Base
  include CarrierWave::MiniMagick
  process :resize_to_fill => [665, 375]
end
```

Finally, set CarrierWave for AWS S3, in `initializers/carrier_wave.rb`

```
CarrierWave.configure do |config|
  config.fog_provider = 'fog/aws'                        # required
  config.fog_credentials = {
    provider:              'AWS',                        # required
    aws_access_key_id:     'xxx',                        # required
    aws_secret_access_key: 'yyy',                        # required
    region:                'eu-west-1',                  # optional, defaults to 'us-east-1'
    host:                  's3.example.com',             # optional, defaults to nil
    endpoint:              'https://s3.example.com:8080' # optional, defaults to nil
  }
  config.fog_directory  = 'name_of_directory'                          # required
  config.fog_public     = false                                        # optional, defaults to true
  config.fog_attributes = { 'Cache-Control' => "max-age=#{365.day.to_i}" } # optional, defaults to {}
end
```

Unfortunately, my AWS account has been suspended, so I change the code to store at local

```
CarrierWave.configure do |config|
  if Rails.env.staging? || Rails.env.production?
    config.storage = :file
    config.enable_processing = true
  else
    config.storage = :file
    config.enable_processing = Rails.env.development?
  end
end
```

This code also show that if you want to deal with different environment for `product` or `development`

> Usually, only upload small size files direct through web page, we can use other AWS S3 client to upload big file.

Another tip here, don't forget to add new attributes to `params.require`, otherwise upload will be failed.

```
params.require(:video).permit(:title, :description, :category_id, :large_cover, :small_cover, :video_url)
```

###RSpec Feature Test

Key Points:

- attach_file
- have_selector

```
feature 'Admin adds new video' do
  scenario 'Admin successfully adds a new video' do
    admin = Fabricate(:admin)
    dramas = Fabricate(:category, title: 'Dramas')

    sign_in(admin)
    visit new_admin_video_path

    fill_in 'Title', with: 'Monk'
    select 'Dramas', from: 'Category'
    fill_in 'Description', with: 'SF detective'
    attach_file 'Large cover', 'spec/support/uploads/monk_large.jpg'
    attach_file 'Small cover', 'spec/support/uploads/monk.jpg'
    fill_in 'Video URL', with: 'http://www.example.com/video.mp4'
    click_button 'Add Video'

    sign_out
    sign_in

    visit video_path(Video.first)
    expect(page).to have_selector("img[src='/uploads/monk_large.jpg']")
    expect(page).to have_selector("a[href='http://www.example.com/video.mp4']")
  end
end
```
