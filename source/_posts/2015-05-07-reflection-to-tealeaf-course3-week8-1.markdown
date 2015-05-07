---
layout: post
title: "Reflection to Tealeaf Course3 Week8 (1/2)"
date: 2015-05-07 10:40:17 +0800
comments: true
categories: [rails, tealeaf]
---

#Subscription

This week it's still about Stripe. Last week, we use Stripe to charge once, and Stripe also offer us to charge  monthly. This is called `Subscription`.

First following the [instruction here](https://stripe.com/docs/subscriptions) to set Plan.

Then write the test for `customer charge`

```ruby
describe StripeWrapper, vcr: true do
  describe "StripeWrapper::customer" do
    it 'create a customer with valid card' do
      user = Fabricate(:user)
      charge = StripeWrapper::Charge.customer(source: valid_token, email: user.email)
      expect(charge).to be_success
    end

    it 'does not create a customer with declined card' do
      user = Fabricate(:user)
      charge = StripeWrapper::Charge.customer(source: invalid_token, email: user.email)
      expect(charge).not_to be_success
    end

    it 'return error message with declined card' do
      user = Fabricate(:user)
      charge = StripeWrapper::Charge.customer(source: invalid_token, email: user.email)
      expect(charge.error_message).to be_present
    end

    it 'returns the customer token for a valid card' do
      user = Fabricate(:user)
      charge = StripeWrapper::Charge.customer(source: valid_token, email: user.email)
      expect(charge.customer_token).to be_present
    end
  end
end
```

`StripeWrapper::Charge.customer` is written like this:

```ruby models/stripe_wrapper.rb
module StripeWrapper
  class Charge
    attr_reader :response, :status

    def self.customer(options = {})    
      begin
        response = Stripe::Customer.create(
          source: options[:source],
          plan: "BASE",
          email: options[:email]
        )
        new(response, :success)
      rescue Stripe::CardError => e
        new(e, :error)
      end
    end
  end
end
```

Here is a tip, in Week7, since we merge code into `UserSignup` service object, although we change behavior from `charge` to `customer`, it's unrelated to `UsersControler#create` code. 

```ruby users_controller.rb
  #some code...
  user_signup_service = UserSignup.new(@user)
  result = user_signup_service.sign_up(params[:stripeToken], params[:token])
```

We only need to modified code in `UserSignup.sign_up`.

```ruby services/user_signup.rb
  def sign_up(stripe_token = nil, invitation_token = nil)
    if @user.valid?      
      charge = customer_charge_user_with_stripe(stripe_token)
      if charge.success?
        #do something...
      else
        #do something...
      end
    else
      #do something...
    end
  end

  def customer_charge_user_with_stripe(token)
    StripeWrapper::Charge.customer(
      source: token,
      email: @user.email
    )
  end
```

#Stripe Webhooks

We can review Stripe payments on Stripe's dashboard, but what if we want to track and save these records in our website?

Here we instroduce [Webhook](https://stripe.com/docs/webhooks)

We need Stripe to trigger an event for our server, then we can hold response from Stripe into our database.

1. Go `Account setting`->`Webhooks`->`Add endpoint...`
2. Input your website url. <- explain it later
3. Select test mode, and press Done
4. Click `Send test webhook`

For developement, we use [RequestBin](http://requestb.in/) here to inspect HTTP requests.

Create a RequestBin Url for Stripe endpoint, from the link above, and input the url to Step 2. Click `Send test webhook` and go back refresh RequestBin page, you will see the response.

Instead of this, we also can review the response from Stripe `dashboard`->`Events & Webhooks`, select one of record and check the `Webhook Details`.

Why we want to check response here? It's just prepared for test data.

For example, create a RSpec file for `succeeded payment`, and convert response to a fake data `:event_data`

```ruby 
describe "Create payment on successful charge" do
  let(:event_data) do
    {
      "id"=> "evt_15yYzgLCTUylKIRln74HkdCq",
      "created"=> 1430705260,
      "livemode"=> false,
      "type"=> "charge.succeeded",
      "data"=> {
        "object"=> {
          "id"=> "ch_15yYzgLCTUylKIRlP66CKpX5",
          "object"=> "charge",
          "created"=> 1430705260,
          "livemode"=> false,
          "paid"=> true,
          "status"=> "succeeded",
          "amount"=> 99,
          "currency"=> "usd",
          "refunded"=> false,
          "source"=> {
            "id"=> "card_15yYzeLCTUylKIRll7zhkfMs",
            "object"=> "card",
            #.........
            "dynamic_last4"=> nil,
            "metadata"=> {},
            "customer"=> "cus_6Aupm1THLvHTnO"
          },
          "captured"=> true,
          "balance_transaction"=> "txn_15yYzgLCTUylKIRluBT7Obmz",
          "failure_message"=> nil,
          "failure_code"=> nil,
          "amount_refunded"=> 0,
          "customer"=> "cus_6Aupm1THLvHTnO",
          "invoice"=> "in_15yYzgLCTUylKIRlcnjWhIBb",
          "application_fee"=> nil,
          #..............
          "refunds"=> {
            "object"=> "list",
            "total_count"=> 0,
            "has_more"=> false,
            "url"=> "/v1/charges/ch_15yYzgLCTUylKIRlP66CKpX5/refunds",
            "data"=> []
          }
        }
      },
      "object"=> "event",
      "pending_webhooks"=> 1,
      "request"=> "iar_6AupyN8zrE6fF4",
      "api_version"=> "2015-04-07"
    }    
  end

  #some spec here...
end
```

According to the data, the event type is `charge.succeeded`.

In order to receive Stripe Webhook event, we need the gem `stripe_event`

```
gem 'stripe_event'
```

In config/routes.rb

```
mount StripeEvent::Engine, at: '/stripe_events' # provide a custom path
```

Get mark here for the path `/stripe_events`, because it's the endpoint for setting webhook in Stripe's account setting. Create a webhook on Stripe, for example, my website is `https://tomo-myflix.herokuapp.com`, then endpoint url is `https://tomo-myflix.herokuapp.com/stripe_events`


Then Stripe_Event help us to deal with stripe events here:

```ruby config/initializers/stripe.rb
StripeEvent.configure do |events|
  events.subscribe 'charge.succeeded' do |event|
    user = User.find_by(customer_token: event.data.object.customer)
    Payment.create(user: user, amount: event.data.object.amount, reference_id: event.data.object.id)
  end
end

```
`customer_token` is created when creating `User`. Add this column in `User` to track Stripe record. And code this in service object `UserSignup.sign_up`:

```ruby models/user_signup.rb
#....
  def sign_up(stripe_token = nil, invitation_token = nil)
    if @user.valid?      
      charge = customer_charge_user_with_stripe(stripe_token)
      if charge.success?
        @user.customer_token = charge.customer_token
        @user.save
# ....
```

`customer_token` and `amount` can get from response `event.data.object`.


If your development follow the TDD process, here should code the test first like this

```ruby spec/requests/create_payment_on_successful_charge_spec.rb
describe "Create payment on successful charge" do
  let(:event_data) do 
    # setting in above, ignore here.
  end

  it 'creates a payment with webhood from stripe for charge succeed', vcr: true do
    post '/stripe_events', event_data
    expect(Payment.count).to eq(1)
  end

  # other tests....
end  
```

Notice that we use `post '/stripe_events', event_data` to simulate Stripe emit a post to our server. So this is only for test to trigger StripeEvent in `initializers/stripe.rb`.

So far, we can create `Payment` by Stripe Webhook.

#ngrok for local test

When we run the server on localhost, there is no way to let Stripe directly emit the post to our local machine. And we can use `ngrok` to do the response transfer.

Download [ngrok](https://ngrok.com/) and install.

```ruby
$ngrok 3000
```

Get an URL from ngrok, and set this url to Stripe Webhook endpoint. Run the rails server, and operate app by manual to check if payment record be saved.

Just a memo here, becuase I can't use ngrok on my Mac(looks like security problem). but it's ok because I test successful on Heroku.

The most important here is to set the app stirpe events URL in Stripe, for example my end point is:

```
https://tomo-myflix.herokuapp.com/stripe_events
```


#ATDD for payment view

The process for `admin views payment` is not very complex, so we can just write the spec outside-in, some also call this `ATDD`.

Here is a post talking about the [differences between TDD, ATDD, BDD](http://gaboesquivel.com/blog/2014/differences-between-tdd-atdd-and-bdd/).

```ruby
feature 'Admin sees payments' do
  scenario 'admin can see payments' do
    user = Fabricate(:user)
    payment = Payment.create(user: user, reference_id: 'fake_reference_id', amount: 99)
    sign_in(Fabricate(:admin))
    visit admin_payments_path
    expect(page).to have_content("$0.99")
    expect(page).to have_content(user.email)
    expect(page).to have_content(user.full_name)
    expect(page).to have_content('fake_reference_id')
  end

  scenario 'user cannot see payments' do
    sign_in(Fabricate(:user))
    visit admin_payments_path
    expect(page).to have_content("You do not have access right.")
  end
end
```
