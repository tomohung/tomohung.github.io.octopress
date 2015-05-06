---
layout: post
title: "Reflection to Tealeaf Course3 Week7 (3/3)"
date: 2015-05-06 17:22:47 +0800
comments: true
categories: [rails, tealeaf]
---

#Messsage Expectation

We has metioned [Test Double at post (1/3)](http://www.tomohung.com/blog/2015/05/06/reflection-to-tealeaf-course3-week7-1/), and here is another kind of Test Double, called `Mock Objects`.

> Assert communication, not response

In previous post, we have moved the code into `UserSignup` services obejct and test it. And here all we need to test is ensure that `:sign_up` method is called in `UsersController#create`.

```
describe UsersController do
  describe 'POST create' do
    context 'with valid input' do

      it 'should logged_in if create user' do
        result = double(:sign_up, successful?: true)
        expect_any_instance_of(UserSignup).to receive(:sign_up).and_return(result)
        expect_any_instance_of(User).to receive(:id).and_return(1)
        post :create, user: Fabricate.attributes_for(:user)
        expect(session[:user_id]).to eq(1)
      end
    end
  end
end
```

`expect_any_instance_of(UserSignup).to receive(:sign_up).and_return(result)` is called `Message Expectation` in RSpec, a kind of `Mock Object`.

Refer to RSpec, it defines

> ###Mock Objects and Test Stubs

> The names Mock Object and Test Stub suggest specialized Test Doubles. i.e. a Test Stub is a Test Double that only supports method stubs, and a Mock Object is a Test Double that supports message expectations and method stubs.

> There is a lot of overlapping nomenclature here, and there are many variations of these patterns (fakes, spies, etc). Keep in mind that most of the time we're talking about method-level concepts that are variations of method stubs and message expectations, and we're applying to them to one generic kind of object: a Test Double.

#Test Minimalist

Here we got 3 resources of discussion test.

- [Why you don't get mock objects](https://www.youtube.com/watch?v=R9FOchgTtLM) by Gregory
- [Mock Aren't Stub](http://martinfowler.com/articles/mocksArentStubs.html) by Mark Folwer
- [The Magic tricks of Testing](https://www.youtube.com/watch?v=URSWYvyc42M) by Sandi Mets

Summary for these three resources.

I use Sandi's picture for the start: 

{% img http://jnoconor.github.io/images/unit-testing-chart-sandi-metz.png %}

Use this week assignment, class `UserSignup` for example:

- Incoming Query: We test UserSignup method `sign_up`, and expect it returns an successful object with valid info. This spec is testing this object state.

- Incoming Command: We also test UserSignup method `sign_up`, and expect it should create an user with valid info. This is what Sandi said: side effects for outside.

> Incoming Command and Incoming Query are all what Mark Folwer said State or Stub or Classical.

- Sent to Self: According to Sandi's opinion, it's not necessary to test all private methods in class. Maybe for the complex method, we should think if we can abstract some logic into a new class, and give that class another tests if we need it.

- Outgoing Query: Sandi ignore this, I guess she means that once the object send a new query to the other object, all we need to do is make sure that the other object works fine. And this should be test on that object, so we don't need to test outgoing query.

- Outgoing Command: Once we let an object send a message to the other object do some side effects, all we need to do is make sure that this object do send a message. And this is method message expectation in RSpec, also Mark Folwer said behavior or Mockist or 'Mock'.

Gregory and Sandi all said that Mock Object can loose each objects association when testing. Once we are testing behavior( or maybe I can say testing interface), we don't need to bind the test and method with specific variable, but bind with an interface. It looks like apply OO concept on testing - try to loose connection with each objects, also loose each object's tests.


Kevin also metions this:

> a pure "mockist" way of doing test can be difficult in Rails apps, because in rails apps persistence is handled by the framework and for small apps most of object collaboration / message sending are about persistence. If you mock those interfaces, you end up mocking the guts of rails and that typically doesn't end up well.  this is also why we only touch this topic in the context of non rails objects like service objects.