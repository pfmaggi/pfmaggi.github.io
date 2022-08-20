# Personal blog

This repository contains the source for [my personal blog](http://pietromaggi.com).

## To publish a new post

Create a new article in the `./content/post/` folder and push it to github.

A workflow monitors the pushes on the `main` branch and will take care of publishing the new version of the site.

For more information use [Hugo's documentation](https://gohugo.io/hosting-and-deployment/hosting-on-github/).

## To clone on a new PC

Given that the public folder and the themes I'm using are [git submodules](http://www.git-scm.com/book/en/v2/Git-Tools-Submodules), you need to clone recursively to get both of them:

```sh
git clone git@github.com:pfmaggi/pfmaggi.github.io.git <blog_destination>
cd <blog_destination>
git submodule update --init --recursive
```
