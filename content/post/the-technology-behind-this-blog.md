+++
categories = ["Blog"]
date = "2015-08-14T23:10:28+02:00"
draft = false
description = "A short description of the tools and services I use for building and publishing this blog."
keywords = []
title = "The technology behind this blog"

+++

The blogging framework
----------------------

The framework I have chosen to use for this blog is [hugo](https://www.gohugo.io).
In fact, it is not really a blogging framework, but a _static site generator_.
A static site generator is a tool that builds HTML pages from some other text files
(in the case of hugo, from markdown). It determines how to make the HTML files 
based on the content of the markdown file and a chosen theme. While this is probably
not ideal for pages with content stored in a database, or user logins and such,
it is just perfect for blogging. There is no default
theme in hugo, but there are a few to choose from when downloading, and if none of
them suits the needs of the user, it is also possible to create a new theme.

Hugo itself is written in [go](https://golang.org), and it is _really_ fast.
The reason I chose hugo, and not wordpress for example, is that I heard about
it on some golang related site, and it just caught my interest from the start.
If I had not heard about hugo, my guess is that I would have used 
[Octopress](http://octopress.org) or just not created a blog at all.

Theme
-----

For my blog, I had to chose a theme. I first tried some different themes, and then
found one called hyde, which I liked. After using hyde for a bit, I found a fork
of it called hyde-x, and it is like an extended version of the original theme.
Hyde-x provides google-analysis integration, gravatar integration and many other
really neat features which I love. For the time being, I am quite satisfied with
hyde-x, but I think I will make my own theme in the near future, just to try it
out. If my own theme becomes "good enough", then the plan is to make it the 
default theme on this blog.

Content creation
----------------

Writing content for my blog is really easy and straightforward. An example of
how I would make a post called "Le epic post" is as follows:

* Open a terminal and `cd` into the local copy of the hugo project for this blog.
* Type `hugo new post/le-epic-post.md` to create a skeleton file for my post.
* Open the newly created file in vim or in gedit to edit its content and make it
 a draft.
* Open a new terminal, go to the same folder and type 
 `hugo server --watch --buildDrafts=true` to start serving a local version of
 my blog. The `--watch` option makes the browser refresh whenever I save the
 content file for my post.
* When I have finished my post, I run `hugo undraft content/post/le-epic-post.md`,
 to undraft it, and to give it the current time as release date.

Those five steps is all it takes to make a blog post, and the `--watch` option
is really handy as it allows me to have a browser open and watch the changes 
I make instantaneously.

Publishing 
----------

When I have written a new post, or just made another change to the blog, I have
to publish it. I use [git](https://git-scm.com) for managing my content, and I
found a really useful tutorial on the hugo documentation site, that describes
how to setup [wercker](http://wercker.com) to fetch content from github, build 
it into HTML files (running `hugo build`) and then publish the final pages to
github pages. This setup makes it really simple to publish a new post, since all
I have to do is committing and pushing it from my own laptop to the github 
[repo](https://github.com/pmikkelsen/hugo-blog), and then wait a minute or two,
and its online. _Great_.  
A link to the tutorial can be found 
[here](http://gohugo.io/tutorials/automated-deployments/).

Hosting
-------

I use [github pages](https://pages.github.com/) to host my website. It is a
free service, and it works really well in my opinion. The way it works is that
any github user can create a repository with the name `username.github.io`,
where username has to be the users github name for it to work. The static website
can then be pushed to the master branch of that repo, and the page becomes 
available online at `http://username.github.io` a shot time thereafter. This
process is 100% automated for me by wercker though.

Questions?
----------

If you have any questions or want me to go more in depth about one of the topics
on this page, please feel free to leave a comment, or send me an email.
