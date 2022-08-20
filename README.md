# Personal blog

This repository contains the source for [my personal blog](http://pietromaggi.com).

## To publish a new post

First of all create a new blog

```sh
hugo new post/<filename>.md
```

edit the file and commit it to github (private repository).

```sh
git add .
git commit -m "<message>"
git push -u origin --all
```

if you want to tag this release, you can use do it, don't forget to push the new tag to bitbucket!

```sh
git tag <new_tag_name>
git push -u origin --tags
```

Now we can use the `deploy.sh` script to push online the new website:

```sh
./deploy.sh ["message"]
```

For more information use [Hugo's documentation](https://gohugo.io/hosting-and-deployment/hosting-on-github/).

## To clone on a new PC

Given that the public folder and the themes I'm using are [git submodules](http://www.git-scm.com/book/en/v2/Git-Tools-Submodules), you need to clone recursively to get both of them:

```sh
git clone git@github.com:pfmaggi/hugo_blog.git <blog_destination>
cd <blog_destination>
git clone git@github.com:pfmaggi/pfmaggi.github.io.git public
git clone git@github.com:pfmaggi/beautifulhugo themes/beautifulhugo
```
