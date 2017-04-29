---
title: Create an Aurelia Application
layout: post
identifier: 2017-03-28-first-post
tags: [aurelia]
excerpt: Create an aurelia application with aurelia-cli
---

### Pre Requisites

* [NodeJS](https://nodejs.org/en/)
  {% highlight shell linenos %}
  Install Brew
  ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

  Install Node
  brew install node
  {% endhighlight %}

* Install aurelia-cli, gulp-cli and typings (if using typescript)
  {% highlight shell linenos %}
  npm i -g aurelia-cli gulp-cli typings
  {% endhighlight %}

### Create an aurelia project

* Create new project
  {% highlight shell linenos %}
  cd <project_folder>

  au new --here
  {% endhighlight %}

  > Follow the prompts to create a new aurelia project

* Running the application
  {% highlight shell linenos %}
  au run --watch
  {% endhighlight %}

* Building the application
  {% highlight shell linenos %}
  au build --env prod
  {% endhighlight %}
