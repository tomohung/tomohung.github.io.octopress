---
layout: post
title: "Reflection to Tealeaf Course3 Week6 (2/2)"
date: 2015-05-06 09:50:50 +0800
comments: true
categories: [rails, tealeaf]
---

These week we have two main part: [CarrierWave][] for uploading images as an admin, [Stripe][] for payments

[CarrierWave]: https://github.com/carrierwaveuploader/carrierwave
[Stripe]: http://stripe.com

The first part about CarrierWave for uploading images as an Admin is [here](http://tomohung.com/blog/2015/05/06/reflection-to-tealeaf-course3-week6/).

#Stripe for Payments


Two articles about payments:

- [How Do You Process Credit Card](https://signalvnoise.com/posts/753-ask-37signals-how-do-you-process-credit-cards) by Jason Fried

> The one thing we’re often surprised by is how many accounts have charge issues so it’s important to really think about the error handling and customer experience issues related to declined cards. 

- [Payment Card Industry Data Security Standard by wiki](http://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)

###Stripe & Figaro

```
gem 'stripe'
gem 'figaro'
```

`$ figaro install`

Use Figaro to set Environment variables, Do Not hard code it!!

```
#config/application.yaml
test:
  STRIPE_SECRET_KEY: sk_test_xxxxxxxxx
  STRIPE_PUBLISHABLE_KEY: pk_test_xxxxxxxxxx

development:
  STRIPE_SECRET_KEY: sk_test_xxxxxxx
  STRIPE_PUBLISHABLE_KEY: pk_test_xxxxxxx

production:
  STRIPE_SECRET_KEY: sk_live_xxxxxx
  STRIPE_PUBLISHABLE_KEY: pk_live_xxxxxxxx

```

> If Stripe account doesn't activate, we only can use test key `sk_test_xxxxxxx` even ENV = production

I also add this file `config/initailizers/figaro.rb`

```
Figaro.require_keys("STRIPE_SECRET_KEY", "STRIPE_PUBLISHABLE_KEY")
```

It's convenient to check if you forget to set Environment variables on deploy machine(ex. Circle CI or Heroku).

Figaro also provide a way to set Heroku ENV.

`$ figaro heroku:set -e production`

### Stripe Custom Form

1. Setting form view

```
<script type="text/javascript" src="https://js.stripe.com/v2/"></script>

<script type="text/javascript">
Stripe.setPublishableKey("#{ENV['STRIPE_PUBLISHABLE_KEY']}");
</script>

= javascript_include_tag 'payment'

```

> When deploy to Heroku, I got this error `Failed to load resource: the server responded with a status of 404 (Not Found)`

> This is becuase assets pipeline, add this to the file

```ruby config/initializers/assets.rb
Rails.application.config.assets.precompile += ['payment.js']
```


Key Points:

- use `id: 'payment-form'` in form_form, this is corespond to `assets/javascripts/payment.js` content.
- use `for` and `id` to connect label and input
- `data-stripe = "number"` and `data-stripe="cvc"`
- `data: { stripe: "exp-yaer"}` and `data: { stripe: "exp-month"}`

Attributes of `data-stripe` is for Stripe to know credit infomation. 

Then in our custom form:

```haml
%section.register.container
  .row
    .col-sm-10.col-sm-offset-1
      = bootstrap_form_for @user, layout: :horizontal, html: {id: 'payment-form'} do |f|
        %header
          %h1 Register
        %fieldset
          = f.email_field :email, label: "Email Address"
          = f.password_field :password
          = f.text_field :full_name, label: 'Full Name'
          = hidden_field_tag :token, @invitation_token
        %fieldset.credit_card
          %span.payment-errors
          .form-group
            %label.control-label.col-sm-2(for="credit_card_number") Credit Card Number
            .col-sm-6
              %input.form-control(type="text" data-stripe="number" id="credit_card_number")
          .form-group
            %label.control-label.col-sm-2(for="security_code") Security Code
            .col-sm-6
              %input.form-control(type="text" data-stripe="cvc" id="security_code")
          .form-group
            %label.control-label.col-sm-2 Expiration
            .col-sm-3
              = select_month(Date.today, {add_month_numbers: true},  class: 'form-control', data: { stripe: "exp-month"})
            .col-sm-2
              = select_year(Date.today.year, {start_year: Date.today.year, end_year: Date.today.year + 4}, class: 'form-control', data: { stripe: "exp-year"} )
        %fieldset.actions.control-group.col-sm-offset-2
          .controls
            %input(type="submit" value="Sign Up" class="btn btn-default")
```

###Setting in Controller Action

```
# Set your secret key: remember to change this to your live secret key in production
# See your keys here https://dashboard.stripe.com/account/apikeys
Stripe.api_key = "sk_test_yNosIrCvrAuVttjO60dxy7zy"

# Get the credit card details submitted by the form
token = params[:stripeToken]

# Create the charge on Stripe's servers - this will charge the user's card
begin
  charge = Stripe::Charge.create(
    :amount => 1000, # amount in cents, again
    :currency => "usd",
    :source => token,
    :description => "Example charge"
  )
rescue Stripe::CardError => e
  # The card has been declined
end
```

Now we click submit button, charge will be deliever to Stripe.

> Notice again, don't foget to set ENV on Circle CI or Heroku if you deploy code to there.
