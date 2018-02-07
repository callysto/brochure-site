# Callysto Brochure Site

This repository contains the files to generate a brochure site using hugo. It's
a pretty simple [hugo](https://gohugo.io/) site, so the [hugo
documentation](https://gohugo.io/documentation/) is a good place to get started
understanding how it works, but we'll also give a summary here.

## Hugo

Hugo is a [go](https://golang.org/) program which will run on most machines, but
figuring out a workflow can sometimes be a bit of a challenge (particularly on
Windows). On OS X, homebrew should let you get started quickly with 

```bash
$ brew install hug
```

For other systems, try the [install
documentation](https://gohugo.io/getting-started/installing/).

## Building a Site

The point of static site generators like hugo is that you should be able to
focus on creating content by editing markdown files only. In the simplest case,
Hugo takes those markdown files, applies a theme and generates a finished
product.

The look and feel of the website is controlled by specifying a theme of which
there are a [large number available](https://themes.gohugo.io/). Each theme is
customizable to some extent or you can create your own if they are not flexible
enough. We've picked the [simplicity
theme](https://themes.gohugo.io/simplicity/) as an example.



## Working with the site

First, clone this repository to your machine
```
git clone https://github.com/callysto/brochure-site.git
```
If you look in the themes directory you'll notice that the simplicity theme
directory is empty. Themes are usually added as submodules so that we can track
upstream development as it happens. To grab the latest versions of the themes

```bash
$ git submodule init
$ git submodule update
```

At this point `hugo` should be able to generate a site for you from the existing
content. `hugo serve` will rebuild the files and then start a local webserver
for you to review the site.

```bash
$ hugo serve

Started building sites ...

Built site for language en:
0 draft content
0 future content
0 expired content
1 regular pages created
13 other pages created
0 non-page files copied
5 paginator pages created
3 tags created
1 categories created
total in 12 ms
Watching for changes in /var/tmp/brochure-site/{content,i18n,layouts,themes}
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server
--disableFastRender
Web Server is available at http://localhost:1313/brochure-site/ (bind address
127.0.0.1)
Press Ctrl+C to stop
```

You can copy/paste the address at the bottom of the output (e.g.
http://localhost:1313/brochure-site/) into your browser to see the site.


## Adding content

Most content is added as markdown in the content directory. Each markdown file
contains some metadata giving a title, date, author etc. and then a markdown
version of the text of your post. You can generate a new post populated with
fresh metadata via (e.g.)

```bash
$ hugo new posts/about.md
```

This would create a new file at `content/posts/about.md`, and populate it with
metadata at the top, e.g.

```yaml
---
title: "Documentation"
date: 2018-02-07T13:24:55-08:00
draft: true
---
```

Your markdown can be added to that file. If you leave `hugo serve` running in
another terminal, it should automatically refresh as you save your markdown
file(s).

## Publishing content via Github Pages

Github pages allows you to publish static websites by pointing the service at a
specific branch of one of your repositories. We can use this together with
`hugo` if we are careful about what we put in each branch. The master branch is
where all the new work gets added, but it isn't suitable for publishing because
it contains all of the hugo support and configuration files. What we actually
want to publish is the result of running the `hugo` command which is the content
of the public directory.

We can do this with `git worktrees`. Worktrees allow us to have two working
branches active at the same time: `master` and `gh-pages`.  The `master` branch
consists of everything *except* the public directory, and the `gh-pages` branch
consists of everything inside the public directory. This allows us to make
changes in the `master` branch (to the content and configuration) then run hugo
and publish the results in the `gh-pages` branch (the public directory) via
github pages.

The publish.sh script in this directory manages some of this workflow. To use
it, the basic idea is:

  1. Edit the content or configuration of the site as needed
  2. Commit your changes to the master branch
  3. Push your changes up to the github master branch
  4. run `./publish.sh`

`publish.sh` should refresh the `gh-pages` branch with the latest and greatest
version of the site and push that to the github `gh-pages` branch. This branch
is immediately published by github pages.

## CNAME

Github pages defaults to publishing at e.g. `callysto.github.io`, but we can
make it available at callysto.ca by adding a CNAME file to this repository when
we are ready.
