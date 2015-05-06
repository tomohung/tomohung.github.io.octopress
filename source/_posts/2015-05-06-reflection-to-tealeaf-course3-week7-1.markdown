---
layout: post
title: "Reflection to Tealeaf Course3 Week7 (1/3)"
date: 2015-05-06 14:20:15 +0800
comments: true
categories: [rails, tealeaf]
---

Last week, we use Stripe for charge in UsersController#create, it's a bit little complex code here. We need to wrap it.

Why we need to wrap it into a class:

1. Easy to maintain code, isolate controller's and business logic. Remember we knew that `fat models, thin controllers`, here is a step further concept, we encapsulate code into `models/stripe_wrapper.rb`

2. Easy to test, isolate test between different object is always a good idea.

#How to Wrap

For `module StripeWrapper` is just a namespace. Then we sort the code in UsersController#create,

```
module StripeWrapper
  class Charge
    attr_reader :response, :status
    
    def initialize(response, status)
      @response = response
      @status = status
    end

    def self.create(options = {})      
      begin
        response = Stripe::Charge.create(
          amount: options[:amount], # amount in cents, again
          currency: "usd",
          source: options[:source],
          description: options[:description]
        )
        new(response, :success)
      rescue Stripe::CardError => e
        new(e, :error)
      end
    end

    def success?
      status == :success
    end

    def error_message
      response.message
    end
  end

  def self.set_api_key
    Stripe.api_key = ENV["STRIPE_SECRET_KEY"]
  end
end
```

Then we can call method `StripeWrapper::Charge(options)`.

`new(repose, :sucess)` and `new(e, :error)` return an `StripeWrapper::Charge` object result.

#How to Test StripeWrapper

Test return object `charge`:

```
describe StripeWrapper do
  describe StripeWrapper::Charge do
    it 'makes successful charge' do
      charge = StripeWrapper::Charge.create(amount: 100, source: valid_token)
      expect(charge.response.amount).to eq(100)
      expect(charge.response.currency).to eq('usd')
      expect(charge).to be_success
    end
  end
end
```

But there is a problem, everytime we test, it will send a request to Stripe server. We don't want to do this and want to reduce test consumer time, use gem `vcr`.

```
$ gem install vcr
$ gem install webmock
```

Then add `vcr: true` to spec

```
it 'make successful charge', vcr: true do
  #something
end
```

vcr will record the reponse in `spec/cassettes`, when next time it find that test emit the same request again, it will not send the request outside to the server, but replaying saved response.

Here is vcr setting:

```ruby spec_helper.rb
require 'vcr'

VCR.configure do |c|
  c.cassette_library_dir = 'spec/cassettes'
  c.hook_into :webmock
  c.configure_rspec_metadata!
  c.ignore_localhost = true
end
```

#How to test Controller#create

Since we have test StripeWrapper, we don't want to test it again in Controller, or hit the Stripe server again. Use [Test Double](http://en.wikipedia.org/wiki/Test_double) `stub` here.

For example, if we have a `PaymentController`:

```
describe PaymentController do
  describe 'POST create' do
    it 'sets the flash success message' do
      charge = double("charge")
      charge.stub(:successful?).and_return(true)
      StripeWrapper::Charge.stub(:create).and_return(charge)
      post :create, token: "fake_token"
      expect(flash[:success]).to be_present
    end
  end
end
```

1. Create a test stub `charge`, and stub a method `successful?` and return `true`.
2. stub `:create` method to `StripeWrapper::Charge` and return `charge`.

Now we strike `post :create`, in PaymentController#create when running to code StripeWrapper::Charge, it will return a result `charge` with `charge.successful?` is `true`.

We do this is because make this controller#create test itself independently, and all we need to do is set StripeWrapper return successful.

> StripeWrapper will make a HTTP request, we can use `vcr` to record this, but it's not necessary becuase this test is already done in stripe_wrapper_spec. So here we choose to use `stub`.

#How to do Feature Test with JavaScript

Since we use javascript in view form, we need to turn on `:js`

```
feature 'visitor make payment' do
  scenario 'with valid card number', js: true do
    visit new_payment_path
    fill_in "Credit Card Number", with "424242424242424242"
    fill_in "Security Code", with "123"
    select "3 - March", form: "date_month"
    select "2015", from: "date_year"
    click_button "Submit Payment"

    expect(page).to have_content "Successful charge."
  end
end
```

Install gem

```
gem 'selenium-webdriver'
gem 'capybara-webkit'
```

And set in `spec_helper.rb`

```ruby
Capybara.javascript_driver = :webkit # For default javascript server
Capybara.server_port = 52662 # For Selenium
```

`webkit` is offered by `Qt` framework, it's faster than `Selenium` and invisible. `Selenium` process is visible, so it's suit for debug.

Careful when test in Email Invitation, I need to use `Selenium` instead of `webkit`, otherwise the test will be failed.

```
scenario 'User successfully invites friend and invitation is accepted', {js: true, vcr: true, driver: :selenium} do
```

##Setting [database_cleaner](https://github.com/DatabaseCleaner/database_cleaner)

It will lead a problem when we run javascript test, refer from RSpec doc:

> drivers which run the server in a different thread may not share the same transaction as your tests, causing data not to be shared between your test and test server.

The problem is when running javascript, activerecord may delete database before javascript test finish.

We can use `database_cleaner` to do `truncation` instead of `transaction`. Manual create and destroy records.

```
RSpec.configure do |config|
  config.use_transactional_fixtures = false

  config.before(:suite) do
    DatabaseCleaner.clean_with(:truncation)
  end

  config.before(:each) do
    DatabaseCleaner.strategy = :transaction
  end

  config.before(:each, :js => true) do
    DatabaseCleaner.strategy = :truncation
  end

  config.before(:each) do
    DatabaseCleaner.start
  end

  config.after(:each) do
    DatabaseCleaner.clean
  end
end
```
