---
title: "Nbgitpuller"
date: 2018-01-29T10:42:46-08:00
draft: false
---

## NBGitPuller
[NBGitPuller](https://github.com/data-8/nbgitpuller) is a very useful server
extension which uses specially constructed URLs to allow students to be guided
through the process of cloning out git repositories. As an example, an
instructor might maintain a collection of notebooks for their class, which they
will update and extend through the term. By giving a specially constructed URL
to their students, each student will automatically be given their own copy of
the files at the start of term and can keep it in sync (without losing their
changes) as the term progresses.

To use it, you need to construct URLs which specify the syzygy server you want
to use and the repository you want to interact with. The URL will look something
like 
```http
https://ganymede.syzygy.ca/jupyter/hub/user-redirect/git-pull?repo=https://github.com/pimsmath/public-notebooks&branch=master
```

The raw URLs are intimidatingly long, but they can be broken apart into simple
parts. Everything before the question mark specifies which jupyterhub service
you want to use (ganymede.syzygy.ca). After the question mark are a series of
key=value pairs, which specify options for nbgitpuller

  * **`repo=https://github.com/pimsubc/public-notebooks`** is required and
    specifies the URL of the git repository you want to clone.
  * **`branch=master`** is an optional git branch name (default "master")
  * **`subPath=path/to/file.ipynb`** is an optional path within the repository to
    restrict the clone to a specific file or directory of interest.

When someone clicks on the link, they are taken to ganymede.syzygy.ca which then
examines the URL parameters and decides what to do next.

## nbgitpuller service demonstration

The following link demonstrates the nbgitpuller service on ganymede.syzygy.ca by
cloning out a [simple python3 example
notebook](https://github.com/pimsmath/public-notebooks/blob/master/nbpuller-example1.ipynb).
To try the service out, simply click on the following link:

  * <a href="https://ganymede.syzygy.ca/jupyter/user-redirect/git-pull?repo=https://github.com/pimsmath/public-notebooks&branch=master&subPath=nbpuller-example1.ipynb" target="_blank">nbgitpuller example</a>

Clicking on the link should trigger the following actions

  1. Take you to [ganymede.syzygy.ca](https://ganymede.syzygy.ca)
  2. Authenticate you via google (if you aren't already)
  3. Start your server (if it isn't already started)
  4. Clone out a copy of the notebook to
     `public-notebooks/nbpuller-example1.ipynb`
  5. Open the notebook in your browser.

The full URL used in the link is
```html
https://ganymede.syzygy.ca/jupyter/user-redirect/git-pull?repo=https://github.com/pimsmath/public-notebooks&branch=master&subPath=nbpuller-example1.ipynb
```

Splitting this apart, we can see the pattern described above:

  * **`https://ganymede.syzygy.ca/jupyter/user-redirect/git-pull`** refers to the
    syzygy instance (ganymede.syzygy.ca).
  * **`repo=https://github.com/pimsmath/repo=public-notebook`** tells nbpuller which
    repository to use.
  * **`branch=master`** specifies the master branch within the repository.
  * **`subPath=nbpuller-example1.ipynb`** tells nbpuller to clone just that one
    file.


## How NBGitPuller Works

As its name implies, NBGitPuller works by constructing and executing git
commands. [Git](https://git-scm.com) is a distributed version control system
which lets you keep track of changes and merge them with other people's.  The
initial click on an NBGitPuller link will take the user to the relevant
JupyterHub, log them in (if necessary) then try to grab a clone of the
repository. If you're not familiar with git, this is essentially a copy of the
remote files in the repository with the advantage that both the user and the
repository owner can continue to modify their own copies of the files
then ultimately merge them together (inside the user's clone). To do all of this
NBGitPuller implements a subset of the git commands so that subsequent clicks on
a link by the user will try to merge changes according to this plan:


  * If content has changed in both places, prefer local (user) changes over remote
changes.
  * If a file was deleted locally but present in the remote, remote file is
restored to local repository. This allows users to get a 'fresh copy' of a file
by just deleting the file locally & clicking the link again.
  * If a file exists locally but is untracked by git (maybe someone uploaded it
manually), then rename the file, and pull in remote copy.

Since git is doing all of the heavy lifting here it is also possible to use git
directly to do more advanced tasks (e.g. different merging strategies) with the
caveat that mixing ordinary git commands and NBGitPuller is not a good idea.
Once you start using git "manually" for that repository you should avoid using
the NBGitPuller links as the merging patterns will almost certainly be
different. As the [NBGitPuller
documentation](https://github.com/data-8/nbgitpuller) says "[this] is going to
cause surprises on an ongoing basis, and should be avoided."

