---
layout: post
title: "Reflection to Tealeaf Course3 Week5 (2/2)"
date: 2015-04-09 15:58:27 +0800
comments: true
categories: [rails, tealeaf]
---

###Continuous Integration(CI) and Continous Delivery(CD)

This part is super exciting so I decide to make it a individual post.

Continuous Integration(CI) and Continous Delivery(CD) are two of most important part in automation development. There are plenty of innovation tools, but I think the work flow is the key to understand.

1. Pull the latest code from GitHub.
2. Create a new feature branch and develop.
3. Finish development and put to a feature named branch on GitHub.
4. Create a PR from this branch to `staging` branch.
5. Wait CI test for staging branch.
6. CI automatically deploy from staging branch to `staging server`.
7. Manual test on staging server.
8. Create PR from staging to `master` branch.
9. trigger CI for master branch and automatically deploy to `production server`.

###Setting CI and CD

Here we use [Circle CI](https://circleci.com) for CI and CD. There is also well known service called [Travis CI](https://travis-ci.org/).

- Create a `staging` branch.
- Follow Circle CI project setting.
- You may need Heroku API key and SSH.
- [Key] Create a `circle.yml` in the root of project, adjust `production_app_name` and `staging_app_name`.

```
machine:
  ruby:
    version: 2.1.5
deployment:
  production:
    branch: master
    commands:
      - heroku maintenance:on --app production_app_name
      - heroku pg:backups capture --app production_app_name
      - git push git@heroku.com:production_app_name.git $CIRCLE_SHA1:refs/heads/master
      - heroku run rake db:migrate --app production_app_name
      - heroku maintenance:off --app production_app_name
  staging:
    branch: staging
    commands:
      - heroku maintenance:on --app staging_app_name
      - git push git@heroku.com:staging_app_name.git $CIRCLE_SHA1:refs/heads/master
      - heroku run rake db:migrate --app staging_app_name
      - heroku maintenance:off --app staging_app_name
```

I got an error when running Circle test: [counldn't find file 'bootstrap']

Solution: Rename file `stylesheets/application.css` to `stylesheets/application.css.scss`. And add Line to the file:

```
@import 'bootstrap-sprockents'
@import 'bootstrap'
```

and remove line `*= require bootstrap`.

###Embeded Badget

I aslo try to use badget in my homepage.

```
%img(src="https://circleci.com/gh/tomohung/myflix.png?circle-token=a7c782deb0d0022335fa1f095e268bb44eb4cab8")
```

Then I can see this passing test badget.

{% img https://circleci.com/gh/tomohung/myflix.png?circle-token=a7c782deb0d0022335fa1f095e268bb44eb4cab8 %}


### Slack ingegration with Github and Circle CI

I failed to use [CCMenu](http://ccmenu.org/) to have a notification. But I use [Slack](https://slack.com/) to integrate GitHub and Circle CI, and that's why I said it's exciting!!

After setting done, the only thing I need to do is `git push origin staging` or `git push origin master`. I'll get notifications in Slack when Github got a push event, and Circle CI run the test and deployment in the same time. Surely I will also get a Circle CI result notification if test and deployment finished.

This is what a programmer should do!! Don't Repeat Yourself!! Bravo!!
