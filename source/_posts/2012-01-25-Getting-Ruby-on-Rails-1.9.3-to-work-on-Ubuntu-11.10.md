---
title: "Getting Ruby on Rails 1.9.3 to work on Ubuntu 11.10"
extid: posts/9217
author: johan
date: 2012-01-25 00:18:44
updated: 2012-01-25 00:18:44
slug: Getting-Ruby-on-Rails-1.9.3-to-work-on-Ubuntu-11.10
tags: [linux, ubuntu, ruby on rails]
---

About a year ago, I wrote a [post on how to get Ruby on Rails to work on Ubuntu 10.10](/posts/Getting-Ruby-on-Rails-to-work-on-Ubuntu-10.10). I’ve since updated it a bit. Today, after doing a clean install of Ubuntu 11.10, I tried to follow my own recipe to install Ruby on Rails, but as it turns out a few things have changed. No biggies, the recipe is still mostly valid, but I thought it might be a good idea to write a new post with updated instructions instead of just changing the old one. 

Just as the last time, this is just what happened to work for me, I’m making no claims that this is the best, or even a good way to do it. 

##### 1 - Prerequisites

First of all, it's a good idea to install some prerequisites, that will be needed anyway:

`$ sudo apt-get install vim-gnome curl git git-core libxslt-dev libxml2-dev libsqlite3-dev`

Technically, gvim (vim-gnome) is not a prerequisite, but it's still nice to have! Some of these you might already have installed, in that case, congratulations.

##### 2 - Install RVM

The first thing you want to do is to install the [Ruby Versioning Manager](http://rvm.beginrescueend.com/), or RVM. I basically followed the instructions on the [Installing RVM page](http://rvm.beginrescueend.com/rvm/install/), but these are the steps I took:

1.  `$ bash -s stable < <(curl -s [https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer](https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer))`Make some changes in .bashrc:

    *   Add to the end:  
`[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm"`Change the line that reads `[ -z "$PS1" ] && return` to `if [[ -n "$PS1" ]] ; then`Add before the row you added to the end (on it's own row): `fi`Restart the terminal, or just run `$ source "$HOME/.rvm/scripts/rvm`Check that rvm works, run `$ type rvm | head -1`. You should get the result "RVM is a function"
It might be a good idea to run `$ rvm notes`, just to make sure everything is fine and you haven't missed anything so far.

##### 3 - Install Ruby 1.9.3 with RVM

1.  `$ rvm package install zlib`  
`$ rvm pkg install openssl`
2.  Time to install Ruby 1.9.3! `$ rvm install 1.9.3 --with-openssl=$HOME/.rvm/usr` *Note: The openssl stuff is not needed for rails to work, but if you want to use [Heroku](http://heroku.com/) to publish your stuff, you'll need it, so might as well install it right away!*`$ rvm --default use 1.9.3`

##### 4 - Install RubyGems

Dowload [RubyGems](http://rubygems.org/) from [rubyforge.org](http://rubyforge.org/frs/?group_id=126) and unzip it. Then run `~/rubygems-1.8.15$ ruby setup.rb`.

##### 5 - Install Rails

Ok, final step. Run `$ gem install rails` to install Rails. This takes a little while, but when it's done everything should work. You can check that everything works by creating a new rails application in a directory of your choice: `$ rails new test_app`, or just jump to ["The first application" chapter in the Rails Tutorial](http://ruby.railstutorial.org/ruby-on-rails-tutorial-book#sec:the_first_application).

And yeah, some of these steps might not actually be necessary, or may break your computer. Also, these steps installs RVM, Ruby and Rails only for the current user, not system-wide.
<div style="margin: 0px; padding: 0px; float: none; display: inline;" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:6b9c89c3-0b98-451c-ab29-43a588fad7a0" class="wlWriterEditableSmartContent">Tags: [linux](/tags/linux), [ubuntu](/tags/ubuntu), [ruby on rails](/tags/ruby+on+rails)</div>
