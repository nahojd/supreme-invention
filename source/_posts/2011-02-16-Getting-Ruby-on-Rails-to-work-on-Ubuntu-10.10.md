---
title: "Getting Ruby on Rails to work on Ubuntu 10.10"
author: johan
date: 2011-02-16 16:56:16
updated: 2012-01-25 00:21:04
slug: Getting-Ruby-on-Rails-to-work-on-Ubuntu-10.10
tags: [linux, ruby on rails, ubuntu]
---

*UPDATE 2012-01-25 These instructions are now deprecated, as I’ve written an [updated post on how to get Ruby on Rails working on Ubuntu 11.10](/posts/Getting-Ruby-on-Rails-1.9.3-to-work-on-Ubuntu-11.10).*

*UPDATE 2011-06-21 – I noticed that the install script at beginrescueend.com has changed. I have updated this post to reflect this.*

Since I'm still on parental leave, and just have too much god damn time on my hands, I thought I'd finally give [Ruby on Rails](http://rubyonrails.org/) a good try, so first I finished [Rails for Zombies](http://railsforzombies.org/), and then I started on the [Ruby on Rails Tutorial](http://ruby.railstutorial.org/) (and by the time I've finished that, I'll have come up with something to build). Since Rails feels kind of Linux-ish, and I like working in Linux anyway (despite being a .NET developer), I jumped at the chance of using my [Ubuntu 10.10](http://www.ubuntu.com/desktop/get-ubuntu/download) installation for this.

To my surprise, however, this proved quite the challenge. It turns out that Ubuntu only has built in support for Ruby 1.8.7, and naturally I wanted to use [1.9.2](http://www.ruby-lang.org/en/news/2010/08/18/ruby-1-9.2-released/). So, since I'll have to do this on at least one other computer, and probably again, as I regularly reinstall my computers, I thought I'd post the steps I used to get everything working. Possibly someone else might find this useful, as well.

*Note, everytime I start some code with the $ sign, it represents a prompt.*

### 1 - Prerequisites

First of all, it's a good idea to install some prerequisites, that will be needed anyway:
`$ sudo apt-get install vim-gnome curl git git-core libxslt-dev libxml2-dev libsqlite3-dev`

Technically, gvim (vim-gnome) is not a prerequisite, but it's still nice to have! Some of these you might already have installed, in that case, congratulations.

### 2 - Install RVM

The first thing you want to do is to install the [Ruby Versioning Manager](http://rvm.beginrescueend.com/), or RVM. I basically followed the instructions on the [Installing RVM page](http://rvm.beginrescueend.com/rvm/install/), but these are the steps I took:

1. <strike>`$ bash < <( curl http://rvm.beginrescueend.com/releases/rvm-install-head )`</strike> <— Does not work anymore!
`$ bash < <(curl –sk https://rvm.beginrescueend.com/install/rvm)`
2. Make some changes in .bashrc:
Add to the end: `[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm"`
Change the line that reads `[ -z "$PS1" ] && return` to `if [[ -n "$PS1" ]] ; then`
Add before the row you added to the end (on it's own row): `fi`
3. Restart the terminal, or just run `$ source "$HOME/.rvm/scripts/rvm`
4. Check that rvm works, run `$ type rvm | head -1`. You should get the result "RVM is a function"
5. It might be a good idea to run `$ rvm notes`, just to make sure everything is fine and you haven't missed anything so far.

### 3 - Install Ruby 1.9.2 with RVM

1. `$ rvm package install openssl`
2. Time to install Ruby 1.9.2!
`$ rvm install 1.9.2 --with-openssl=$HOME/.rvm/usr`
*Note: The openssl stuff is not needed for rails to work, but if you want to use [Heroku](http://heroku.com/) to publish your stuff, you'll need it, so might as well install it right away!*
3. `$ rvm --default use 1.9.2`

### 4 - Install RubyGems

Dowload [RubyGems](http://rubygems.org/) from [rubyforge.org](http://rubyforge.org/frs/?group_id=126) and unzip it. Then run `~/rubygems-1.5.2$ ruby setup.rb`.

### 5 - Install Rails

Ok, final step. Run `$ gem install rails` to install Rails. This takes a little while, but when it's done everything should work. You can check that everything works by creating a new rails application in a directory of your choice: `$ rails new test_app`, or just jump to ["The first application" chapter in the Rails Tutorial](http://ruby.railstutorial.org/ruby-on-rails-tutorial-book#sec:the_first_application).

And yeah, some of these steps might not actually be necessary, or may break your computer. Also, these steps installs RVM, Ruby and Rails only for the current user, not system-wide.