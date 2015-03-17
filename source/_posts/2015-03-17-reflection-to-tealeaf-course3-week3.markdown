---
layout: post
title: "Reflection to Tealeaf Course3 Week3"
date: 2015-03-17 09:41:26 +0800
comments: true
categories: [rails, tealeaf]
---

[In week 2](http://www.tomohung.com/blog/2015/03/11/reflection-to-tealeaf-course3-week2/), Tealeaf talked about how to test controller, and this week, I got a more comprehensive test consideration for controller and user interaction.

###For Controller Test

- Everytime add a function feature, also need to add a test for it.
- Everytime find a bug, also add a test for it.

###DRY

RSpec also offer two ways to DRY code.

#### - Macro

```
$ mkdir spec/support
$ touch macros.rb
```

And we can move repeatedly code into here, for example, we usually need to set a login user:

```
def set_current_user(user = nil)
  session[:user_id] = (user || Fabricate(:user)).id
end
```

and find user

```
def current_user
  User.find(session[:user_id])
end
```

#### - Shared Examples

```
$ touch spec/support/share_examples.rb
```

A test method may be called through different controllers:

```
shared_examples 'requre_sign_in' do
  it 'redirects to the root page'
    action
    expect(response).to redirect_to root_path
  end
end
```

Be careful: `action` is a **variable**, then when using it:

```
it_behaves_like 'require_sign_in' do
  let(:action) { get :index }
end
```

###Feature Specs

There are two methods to test user interactions, one is `request spec` and this is deprecated by **Capybara**. The other one is `feature specs`, recommended by **Capybara**.

**Capybara** offer functions that we can click link/button, fill text field...etc, to interactive with UI.

#### - Install Capybara gem

```
group :test
  gem 'capybara'
end
```

Also add this line to test helper file

```
requrire 'capybara/rails'
```

And

```
$mkdir spec/features
$touch user_signs_in_spec.rb
```

#### - A sample for user operation:

```
feature 'user interacts with the queue' do 

  scenario 'user adds and reorder videos in the queue' do
    category    = Fabricate(:category)
    monk        = Fabricate(:video, title: 'monk', category: category)
    futurama    = Fabricate(:video, title: 'futurama', category: category)
    south_park  = Fabricate(:video, title: 'south park', category: category)

    sign_in
    expect(page).to have_content 'You have signed in.'

    add_video_to_queue(monk)
    expect(page).to have_content(monk.title)

    visit video_path(monk)
    expect(page).not_to have_content('+ My Queue')

    add_video_to_queue(futurama)
    add_video_to_queue(south_park)

    set_video_order(monk, 3)
    set_video_order(futurama, 1)
    set_video_order(south_park, 2)

    expect_video_order(monk, 3)
    expect_video_order(futurama, 1)
    expect_video_order(south_park, 2)
  end
end

def add_video_to_queue(video)
  visit home_path
  find("a[href='/videos/#{video.id}']").click
  expect(page).to have_content(video.title)
  click_link '+ My Queue'
end

def set_video_order(video, order)
  within(:xpath, "//tr[contains(.,'#{video.title}')]") do
    fill_in 'queue_items[][position]', with: order
  end
end

def expect_video_order(video, order)
  expect(find(:xpath, "//tr[contains(.,'#{video.title}')]//input[@type='text']").value).to eq(order.to_s)
end

```

here I choose to use `:xpath`.

#### - One level for scenario

In above sample, I extract methods `add_video_to_queue`, `set_video_order`, and `expect_video_order`. This is we want to keep a principle `Abstract one level for scenario`. Encapsulating logic code and keep main code readable on the same abstract level can help reader to review.


###Rails Conventions

#### - Merge Params

```
= form_tag update_queue_path do
  ...
  %td= text_field_tag 'queue_items[][position]', queue_item.position
  =hidden_field_tag 'queue_items[][id]', queue_item.id

  =submit_tag 'Update'
```

Then we will get a hash value `params[queue_items] = {position: xxx, id: xxx}`


#### - select_tag

```
%td= select_tag 'queue_items[][rating]', options_for_video_reviews(queue_item.rating), include_blank: true
```

And `options_for_video_reviews`:

```
def options_for_video_reviews(selected = nil)
  options_for_select((1..5).map { |num| [pluralize(num, 'Star'), num]}, selected)
end
```

Be careful that `[pluralize(num, 'Star'), num]` is a pair value.


#### - ActiveRecord Transactions

A sample from Rails Tutorial

```
ActiveRecord::Base.transaction do
  david.withdrawal(100)
  mary.deposit(100)
end
```

If any one of `david.withdrawl` or `mary.deposit` raise an exception(**Be careful: there must be a raising error to trigger transaction**), all database record in this action will be rollback. It deal with batch action with database. So I have to care about what functions will raise an exception when fail. Ex. `update` return a fail, and `update!` raise an exception.