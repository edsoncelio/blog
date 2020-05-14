---
title: "Day 01 - Deploying an Ruby App from Gitlab to Heroku"
date: 2020-01-06T20:17:02-03:00
categories: ["2019", "devops"]
tags: ["en","30daysofdevops","gitlabci", "heroku"]
draft: false
---


## Problem 

Deploy an ruby application hosted in Gitlab to Heroku, using Gitlab CI/CD.

---

## Solution

### Configurating Heroku account

[Heroku](https://www.heroku.com/) is a PaaS with support to several programmming languages.

In first place, you need to create the heroku application, at **New> Create new app**:
![Create new app](/static/new_project_heroku.png)

Setting up the app name (in my case, it's **demo-app-gitlab**), and create:
![Setting up the app name](/static/create_app_heroku.png)

Since we will use a ruby project, you need to configure the [buildpack](https://devcenter.heroku.com/articles/buildpacks) to the language, accessing **Settings>Add buildpacks**:
![Add buildpack](/static/heroku_buildpack.png)


It's done, now is time to setting up your token to acess this app with the Gitlab.

Go to **[Account](https://dashboard.heroku.com/account)>API Key**, and generate a new API key, and save that.


### Configurating Gitlab account 

Gitlab CI is a tool inside [Gitlab](http://gitlab.com) to provide continuous integration, continuous delivery and continous deployment. It's works using a file
called `.gitlab-ci.yml` placed at the repository's root.

To this project, we will deploy an ruby application (you can use the [sample application](https://github.com/heroku/ruby-getting-started)).

First, create your secret variable with the heroku API Key accessing **https://gitlab.com/<your-username>/<your-repository-name>/-/settings/ci_cd > Variables**:
![Variable gitlab](/static/variable_gitlab.png)

**Observation:** the name of your variable is `HEROKU_STAGING_API`

Create an repository with your project, and place the file `.gitlab-ci.yml` with the content inside:

```
develop:
  stage: deploy
  script:
  - gem install dpl
  - dpl --provider=heroku --app=demo-app-gitlab --api-key=$HEROKU_STAGING_API_KEY
  only:
  - develop

```

Some important notes:

* `--app=demo-app-gitlab`: this is the name of your app created at heroku
* `--api-key=$HEROKU_STAGING_API_KEY`: this is the variable used to connect with our heroku app
* `only: - develop` : this is about deploy your app just when has a pull in `develop` branch 


## References

* [https://docs.gitlab.com/ee/ci/](https://docs.gitlab.com/ee/ci/)
* [https://devcenter.heroku.com/articles/buildpacks](https://devcenter.heroku.com/articles/buildpacks)
* [https://docs.gitlab.com/ee/ci/yaml/](https://docs.gitlab.com/ee/ci/yaml/)
