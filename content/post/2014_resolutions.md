---
title: 2014 New Year resolutions
date: 2014-01-01T17:41:32+01:00
author: Pietro F. Maggi
summary:  Looking forwards to what I'd like to build in 2014.
keywords: [Personal, Diary]
tags: [Personal, Diary]
topics: [Personal]
draft: false
type: post
---

## Blog a bit more
That's been one of my goal for a long time, and something that I always suggest to people to increase their *["Luck Surface Area"](http://www.codusoperandi.com/posts/increasing-your-luck-surface-area)*.

In my case I can add an additional goal: keep track of what I do, tricks that I find and document procedure, fixes and workarounds.
That's always a bit of a pain point for me: I manage to do something and then in six months (or six hours) I forgot how I've done it and I need to start from scratch... **Never More**

### Content in Markdown
That's one of the key technical decisions that I already have clear in mind, the content I'm going to produce will be in pure text, using [Markdown](http://daringfireball.net/projects/markdown/) formatting.

### Choosing a blog engine and an hosting platform
I don't want to spend a lot of time setting up a system at this moment, I prefer to use my time for other things!
GitHub offer a nice way to have a [git repository used as a blog source](https://pages.github.com/). It's a no-brainer for me!

Static site generators like [Jekill](http://jekyllrb.com/) are another *no brainer*. In my case I prefer to have some helps so I'm going to use [octopress](http://octopress.org/) starting with the [KoenigsPress](https://github.com/TheChymera/Koenigspress) theme.

### Hosting solutions
GitHub pages

### Setting up my custom domain
At the end I used godaddy.com to buy the domain pietromaggi.com for the next 10 years, I've managed to find a nice coupon and after few minutes I was able to have everything setup correctly.
Some additional information are available on stackoverflow and this [blog](http://learnaholic.me/2012/10/10/deploying-octopress-to-github-pages-and-setting-custom-subdomain-cname/).

### Documenting my writing process
```sh
rake new_post["my new great post"]
rake preview
git status
git add .
git commit -m "Added new great post"
git push origin source
rake gen_deploy
```

**Hopefully I'll finally start to crank out some content!**
