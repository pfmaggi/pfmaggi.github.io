---
title: Moving to github actions to publish the blog
summary: This blog started locally on my machine a long time ago, when I was mainly using a single computer. Nowadays I work with multiple machines...
date: 2022-08-20T16:00:00+02:00
tags: [Personal, Writing, Automation]
draft: false
author: Pietro F. Maggi
---


## What is this all about

This blog started locally on my machine a long time ago, when I was mainly using a single computer. Nowadays I work with multiple machines and I wanted to move the actual creation of the blog and publishing to github actions to avoid having to keep in sync all my machines.

As a bonus, I'm writing this article from github's editor and the workflow will take care of publishing the article when I push this changes.

## Nuts and Bolts

There is a lot of documentation online. As it is natural, some of it is not up to date. [The best option is to follow github's help][1] and use the default workflow that they make available for Hugo, the static website generator I'm using for this site.

I had to update a few thinks to fit this into my existing workflow.

### HTTPS, APEX and WWW domain

I'm using my own domain for this website, but the pages are hosted by github-pages. Githubs provides a documentation on how to setup your custom domain and it added over time support for the HTTPS protocol managing automatically the certificates with [Let's Encrypt][2]. This has evolved over time and I managed to keep my setup up-to-date. However I saw that I the site was missing a valid certificate when using the `www` subdomain: https://www.pietromaggi.com.

I've looked around and, at the end, it was simply my (obsolete) configuration that needed to be updated as described in [GitHub's documentation][3]. Also [registering my domain helped!][4].

### Renaming the default branch

While I was on GitHub, I though that it was a good idea to move the default branch to `main`. Again, [GitHub's help provides all the information on how to change this][5]. A nice touch is that the guide also includes the instructions on how to update the local repository, an additional step I did locally was to purge the branches to remove all the references to `master`:

{{< highlight shell "linenos=table" >}}
git fetch --prune
{{< / highlight >}}

 My only issue was to remember to update my workflow (that was still using `master`) and to update the name of the branch allowed to be deployed on gh-pages in the [settings][6].

## Cleanup

I had a few leftover articles in the old private repository that I didn't want to transition to this new instance. Obviously I remembered about them only after I pushed a couple of commits to GitHub. So I rewrote the history (`git rebase -i <hash>`) and used [The Silver Searcher][7] to remove the offending files:

{{< highlight shell "linenos=table" >}}
ag -l "draft: true" | xargs rm
{{< / highlight >}}

Additional cleanup is still due in some of the older articles. [Hugo updated/expanded the syntax for the cross-references][8] and I started, slowly, fixing some of the older articles.

## Roadmap

The idea is that this new setup, with a lower friction, should help me write a bit more. That's the plan!

[1]: https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site#publishing-with-a-custom-github-actions-workflow
[2]: https://letsencrypt.org/
[3]: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain-and-the-www-subdomain-variant
[4]: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages
[5]: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-branches-in-your-repository/renaming-a-branch
[6]: https://github.blog/changelog/2021-02-17-github-actions-limit-which-branches-can-deploy-to-an-environment/
[7]: https://github.com/ggreer/the_silver_searcher
[8]: https://gohugo.io/content-management/cross-references/