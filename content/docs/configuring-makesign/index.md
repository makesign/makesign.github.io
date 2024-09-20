---
title: 'Configuring-Makesign'
date: 2019-02-11T19:27:37+10:00
weight: 2
---


# MakeSign Configuration

MakeSign is written in Ruby on Rails, which is why Ruby on Rails needs to be installed on your device. 

The following applications need to be installed to use MakeSign: 

- Install [Node.js](https://nodejs.org/en/download/package-manager), with at least version 22.9.0 
- Install [Yarn](https://classic.yarnpkg.com/lang/en/docs/install/#windows-stable), with the current version
- Install Ruby, at least ruby 3.3.3
- Install Rails, at least Rails 7.1.4

For inline commands we 

After installing them, you can git clone the Repository.

The first steps after cloning will be the following inline commands:

- to generate every file:
```
bundle install
```

- installing Bootstrap:
```
./bin/rails css:install:bootstrap
```

- installing the JavaScript Build of Bootstrap:
```
./bin/rails javascript:install:esbuild
```

- adding Yarn to Bootstrap:
```
  yarn add bootstrap @popperjs/core @hotwired/turbo-rails @hotwired/stimulus
```

With this, MakeSign should be able to run. Use the following commands to start test it via Localhost: 

```
bin/rails s
```
or, to include CSS Styling:
```
bin/dev
```

Ruby on Rails will ask you to migrate. The command for it is the following:
```
bin/rails db:migrate
```

The Active Storage is currently used localy on your disk. To include an external Storage System go to **storage.yml** to change access key id, secret access key, region, bucket id to your preference. 