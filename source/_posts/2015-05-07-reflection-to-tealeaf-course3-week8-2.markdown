---
layout: post
title: "Reflection to Tealeaf Course3 Week8 (2/2)"
date: 2015-05-07 15:43:10 +0800
comments: true
categories: [rails, tealeaf]
---

#Handle Payments Failures

Here we review what Jason Fried said in [How Do You Process Credit Card](https://signalvnoise.com/posts/753-ask-37signals-how-do-you-process-credit-cards):

> The one thing we’re often surprised by is how many accounts have charge issues so it’s important to really think about the error handling and customer experience issues related to declined cards. 

So there are thousands of reasons a credit card may become declined after user register our website. It's important to handle these payment failures.

Stripe also provide the test situation for us.

> Card Number: `4000000000000341`  
> Description: `Attaching this card to a Customer object will succeed, but attempts to charge the customer will fail.`

This special card number will create a customer but charge will fail.

We may produce failed webhook event by Stripe Dashboard.

Open `Payments` on dashboard and click `Create Payment`, input the above invalid Card Number info and click Done. Then we can find a failure charge in `Events & Webhooks`.

As the previous post, we can get failed data as `:event_data` for test:

```ruby spec/requests/deactivate_user_with_payment_failure
describe 'customer on failed charge' do
  let(:event_data) do
    {
      "id" => "evt_15ycOeLCTUylKIRlJom1jayI",
      "created" => 1430718340,
      "livemode" => false,
      "type" => "charge.failed",
      "data" => {
        "object" => {
          "id" => "ch_15ycOdLCTUylKIRlq8G764Gc",
          "object" => "charge",
          "created" => 1430718339,
          "livemode" => false,
          "paid" => false,
          "status" => "failed",
          # .......
          "application_fee" => nil,
          "refunds" => {
            "object" => "list",
            "total_count" => 0,
            "has_more" => false,
            "url" => "/v1/charges/ch_15ycOdLCTUylKIRlq8G764Gc/refunds",
            "data" => []
          }
        }
      },
      "object" => "event",
      "pending_webhooks" => 1,
      "request" => "iar_6AyLkbPGvZltpn",
      "api_version" => "2015-04-07"
    }
  end
end
```

Notice that `:event_data` type is `charge.failed`.

Then we add event `charge.failed` in `strip.rb`

```ruby config/initializers/stripe.rb
StripeEvent.configure do |events|
  events.subscribe 'charge.failed' do |event|
    user = User.find_by(customer_token: event.data.object.customer)
    user.deactivate!
  end
end
```

We simplpy deactivate user here, usually, it should send a warning mail to the user.

Here is the request test:

```ruby spec/requests/deactivate_user_with_payment_failure
describe 'customer on failed charge' do
  let(:event_data) do
    #...
  end

  it 'deactivates a user with the webhook data from stripe for charge failed', vcr: true do
    user = Fabricate(:user, customer_token: "cus_6Awy7ItkTK5HCv", active: true)
    post '/stripe_events', event_data
    expect(user.reload).not_to be_active
  end
end
```

We need to add column `active` for `User` to store the status.

```ruby
add_column :users, :active, :boolean, default: true
```

Make the `active` column default is true, otherwise Rails will set `false` for default value.

#A Short Conclusion for Stripe

We cover Stripe for last three weeks.

- In week 6, we introduce Stripe for charge. How to build a custom form to charge. And test by using `vcr` for replay response.

- In week 7, we refactor code into `UserSignup`, as an `Service Object` of OOP concept. And we need to use javascript driver `webkit` and `selenium` in RSpec because of Stripe api.

- In week 8, we use `Subscription` to charge monthly instead of charge once. And we handle events by `Webhook`, to deal with `charge.succeeded` and `charge.failed` events.
