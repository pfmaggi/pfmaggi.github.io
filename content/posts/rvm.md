---
title: Ruby Version Manager (aka RVM) the Swiss army knife of Ruby versions
date: 2013-12-29
author: Pietro F. Maggi
summary: Obsolete - Ruby is such a nice language that having a single version on you computer is not enough! 
tags:
    - "Ruby"
    - "Virtual environments"
    - "RVM"
    - "OS X"
toc: true
---


{{< div class="danger pure-button" >}}
> The information in this article is obsolete.<br>Please refer to RVM and Ruby's documentation for modern best practices.<br>This worked for me long time ago.
{{<div "end" >}}

## How many versions of Ruby do you need on your machine?
Even if OS X 10.9 *Mavericks* came with Ruby v2.0.0-p195, the previous OS X versions where released with Ruby v1.8.7.
On top of this, most of the work I do with [Rhodes](http://github.com/rhomobile/rhodes) is still based on Ruby v1.9.3.
**And** [Ruby v2.1.0 has just been released!](http://www.ruby-lang.org/en/news/2013/12/25/ruby-2-1-0-is-released/) with some nice [changes](https://github.com/ruby/ruby/blob/v2_1_0/NEWS). Particularly interesting in Ruby v2.1 is the [new Garbage Collector](http://tmm1.net/ruby21-rgengc/)

So, **you've to install on your machine more than one Ruby environment!**, that means installing a Ruby Version Manager and then installing the different Ruby engine you want to test/use.

## Choosing a Ruby Version Manager
*"Hey, what do you mean sir, there's more than one?"*  
*"Yes, of course!"*

most common ruby version managers: [Rbenv](https://github.com/sstephenson/rbenv), [RVM](http://rvm.io) and [ChRuby](https://github.com/postmodern/chruby). RVM is the oldest solution and the easiest to have an environment up and running on a single machine to make some tests.
If I've to work on a project with other developers having different environment (dev, test and deployment) I'll probably give a good look to rbenv that integrates with bundler avoiding the RVM gemsets concept and having the gem necessary to a project in the project folder.

## Go with RVM
Even if I like testing new things, when I need to get things done, I prefer the old trusty road then the new flashy one. In this case I'm going to use [**RVM**](http://rvm.io).
So, first step is to install RVM on OS X and this requires a package manager to work more easily, this means [**HomeBrew**](http://brew.sh/).

### First Step: Install HomeBrew
Installing HomeBrew is very easy:
```bash
$ ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"
```

You need to have XCode installed and the command line tools, [HomeBrew website makes a good description of these steps](https://github.com/Homebrew/homebrew/wiki/Installation).

### Second Step: Install RVM

We need to install some prerequisite using HomeBrew:

```bash
$ brew tap homebrew/versions
$ brew install gcc46
```

then install RVM itself together with ruby 1.9.3:

```bash
$ curl -sSL https://get.rvm.io | bash -s stable --ruby=1.9.3
```

and set it up as default

```bash
$ rvm alias create default 1.9.3
```

Install others ruby to play with them

```bash
$ rvm get stable
$ rvm list known
```

## Upgrade a ruby to the latest patch available
Nowadays ruby 1.9.3-patch484 seems pretty solid, but a lot of activities is on ruby 2.0.
If you want keep your ruby up-to-date, RVM can help you keeping it aligned to the latest available patch with the command:

```bash
$ rvm get stable # always check that you've the latest rvm available before starting
$ rvm list
$ rvm list known
$ rvm upgrade 2.0.0
```

This will be followed by some questions:

```bash
$ rvm upgrade 2.0.0
Are you sure you wish to upgrade from ruby-2.0.0-p247 to ruby-2.0.0-p353? (Y/n): Y
Installing new ruby ruby-2.0.0-p353
Searching for binary rubies, this might take some time.
Found remote file https://rvm.io/binaries/osx/10.8/x86_64/ruby-2.0.0-p353.tar.bz2
Checking requirements for osx.
Updating certificates in '/usr/local/etc/openssl/cert.pem'.
Requirements installation successful.
ruby-2.0.0-p353 - #configure
ruby-2.0.0-p353 - #download
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 8892k  100 8892k    0     0   601k      0  0:00:14  0:00:14 --:--:--  652k
ruby-2.0.0-p353 - #validate archive
ruby-2.0.0-p353 - #extract
ruby-2.0.0-p353 - #validate binary
ruby-2.0.0-p353 - #setup
ruby-2.0.0-p353 - #making binaries executable.
ruby-2.0.0-p353 - #downloading rubygems-2.2.0
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  400k  100  400k    0     0   373k      0  0:00:01  0:00:01 --:--:--  624k
ruby-2.0.0-p353 - #extracting rubygems-2.2.0.
ruby-2.0.0-p353 - #removing old rubygems.
ruby-2.0.0-p353 - #installing rubygems-2.2.0...
ruby-2.0.0-p353 - #gemset created /Users/pietro/.rvm/gems/ruby-2.0.0-p353@global
ruby-2.0.0-p353 - #importing gemset /Users/pietro/.rvm/gemsets/global.gems..
ruby-2.0.0-p353 - #generating global wrappers.
ruby-2.0.0-p353 - #gemset created /Users/pietro/.rvm/gems/ruby-2.0.0-p353
ruby-2.0.0-p353 - #importing gemsetfile /Users/pietro/.rvm/gemsets/default.gems evaluated to empty gem list
ruby-2.0.0-p353 - #generating default wrappers.
Updating certificates in '/etc/openssl/cert.pem'.
mkdir: /etc/openssl: Permission denied
pietro password required for 'mkdir -p /etc/openssl':
Can not create directory '/etc/openssl' for certificates.
Migrating gems from ruby-2.0.0-p247 to ruby-2.0.0-p353
Free disk space 50566MB, required 6MB.
Are you sure you wish to MOVE gems from ruby-2.0.0-p247 to ruby-2.0.0-p353?
This will overwrite existing gems in ruby-2.0.0-p353 and remove them from ruby-2.0.0-p247 (Y/n): Y
Moving ruby-2.0.0-p247@global to ruby-2.0.0-p353@global...............................................................................|
Making gemset ruby-2.0.0-p353@global pristine.
Moving ruby-2.0.0-p247 to ruby-2.0.0-p353.............................................................................................|
Making gemset ruby-2.0.0-p353 pristine.
Do you wish to move over wrappers? (Y/n): Y
Do you also wish to completely remove ruby-2.0.0-p247 (inc. archive)? (Y/n): Y
Removing ruby-2.0.0-p247.
Successfully migrated ruby-2.0.0-p247 to ruby-2.0.0-p353
Upgrade complete!
```

## What you can do to restart an RVM setup from scratch
Having an RVM environment growing over time can bring a bit of cruft into the system. If you're starting to encounter more and more errors working on the setup, you can think to cleanup your setup and restart from scratch:

```sh
$ rvm get stable
$ rvm cleanup
```
### Usage:
```bash
$ rvm cleanup {all,archives,repos,sources,logs,gemsets,links}
```
    
### Description:
Cleans up the directory tree for the specified item.
For gemsets removes remove only those without matching ruby.

```bash
$ rvm cleanup all
Cleaning up rvm archives
Cleaning up rvm repos
Cleaning up rvm src
Cleaning up rvm log
Cleaning up rvm tmp
Cleaning up rvm gemsets
Cleaning up rvm links
Cleanup done.
```
