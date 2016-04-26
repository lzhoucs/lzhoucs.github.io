---
layout:     post
title:      Setting up GitHub Pages site locally on Windows
date:       2016-04-26
summary:    Step by step guide on how to setup Jekyll based GitHub Pages site on your local windows machine.
categories: tech devtool
tags:       GitHub-Pages Jekyll Windows
---
As we know that we can set up a local version of our `Jekyll` based `GitHub Pages` site to test changes locally before pushing to GitHub.
The tricky thing is how to set it up on a Windows machine, given `ruby` and `python` as well as a few other components are not built-in
to Windows, and getting those missing pieces installed and work together on Windows can be tricky.

### Install prerequisite softwares

There are a few required softwares we need to install before setting up GitHub Pages.

#### ruby 2.2.x

It is important, at this time of writing, not to install ruby 2.3, although it is the latest stable release version. It is because a
few `GitHub Pages` dependencies are not working with ruby 2.3 yet and having a ruby 2.3 will cause a few errors during the process of
setting up `GitHub Pages`.

The recommend way of installation is to simply use a [ruby installer](http://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-2.2.4-x64.exe).
After launching the installer, next all the way to the end and it is recommended not to check any options as shown below:

![Ruby Installation](/images/posts/ruby-install-1.png)

After ruby is installed, manually add `C:\Ruby22-x64\bin` to your `Path`.

#### python 2.7.x

`python 3` may also work, but I didn't test it. It is recommended to use `python 2.7.x` as it has been around for a while. Download the [official installer](https://www.python.org/ftp/python/2.7.11/python-2.7.11.msi) and
double click to install.

After it is installed, manually add `C:\Python27` and `C:\Python27\Scripts` to your `Path`.

#### RubyInstaller Development Kit (DevKit)

The RubyInstaller Development Kit (DevKit) is a MSYS/MinGW based toolkit than enables you to build many of the native C/C++ extensions available for Ruby.
Different version of ruby requires different DevKit. For ruby 2.2.x, [DevKit-mingw64-64-4.7.2](http://dl.bintray.com/oneclick/rubyinstaller/DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe) should be used.

Double click the installer downloaded to unzip it to a location, for example: `C:\RubyDevKit`. Then use any terminal/command line of your choice:
{% highlight sh %}
cd C:/RubyDevKit
{% endhighlight %}

Then run the following command to auto-detect ruby installation path and set it in a configuration file `config.yml`:
{% highlight sh %}
ruby dk.rb init
{% endhighlight %}

Finally install the DevKit and bind it to your ruby installation:
{% highlight sh %}
ruby dk.rb install
{% endhighlight %}

### Install GitHub Pages

After required dependencies are installed, we can now move on to the main piece - `GitHub Pages`.

#### Install `Bundler` gem
{% highlight sh %}
gem install bundler
{% endhighlight %}

#### `github-pages` Gemfile
Make sure your Gemfile exist in root dir of your local jekyll site. Create one if it doesn't exist. Make sure the following lines are there in your Gemfile:
{% highlight sh %}
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
{% endhighlight %}

#### Install GitHub Pages using `bundle`

The last step of setting up GitHub Pages locally, is to run the following command:
{% highlight sh %}
bundle install
{% endhighlight %}

### Build your local site for preview

Go to the root dir of your local jekyll site, and then run the following to build:
{% highlight sh %}
bundle exec jekyll serve
{% endhighlight %}

Finally preview the site in your browser at `http://localhost:4000` by default.

### Reference
  - [Setting up your GitHub Pages site locally with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/) 
  - [Run Jekyll on Windows](http://jekyll-windows.juthilo.com/) 


