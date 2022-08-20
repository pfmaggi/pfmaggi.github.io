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

## Updates to the themes

The themes are included as git submodules, with their own origin. Currently I'm using [beautifulhugo][10], using my own [fork and branch][11].

Whenever I update the theme, it is just another git repository, but I also need to update the main repository to update it's reference:

### Make changes inside a submodule

- `cd` inside the submodule directory.
- Make the desired changes.
- `git commit` the new changes.
- `git push` the new commit.
- `cd` back to the main repository.
- In `git status` you'll see that the submodule directory is modified.
- In `git diff` you'll see the old and new commit pointers.
- When you `git commit` in the main repository, it will update the
  pointer.

[Source of this information][12]

[10]: https://github.com/halogenica/beautifulhugo
[11]: https://github.com/pfmaggi/beautifulhugo
[12]: https://gist.github.com/gitaarik/8735255#pushing-updates-in-the-submodule
