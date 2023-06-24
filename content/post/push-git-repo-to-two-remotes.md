---
title: "Push Git Repo to Two Remotes"
date: 2023-06-24T18:15:43+05:30
lastmod: 2023-06-24T18:15:43+05:30
draft: false
keywords: [git push]
description: ""
tags: [git]
categories: [version-control]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true
autoCollapseToc: false
postMetaInFooter: true
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---
Sometimes you may want to push your code to two different remote repositories, for example, if you want to use one service for hosting your code and another for deploying it. In this post, I will show you how to do that using git.
<!--more-->

## Adding Multiple Push URLs

The easiest way to push your code to two remotes is to add multiple push URLs for a given remote. For example, if you have a remote called `origin` that points to your main repository, you can add another push URL for a different repository using this command:

```bash
git remote set-url --add --push origin git://existing/repo.git
git remote set-url --add --push origin git://another/repo.git
```

This will add a second push URL to your `origin` remote, so when you run `git push origin`, it will push your code to both repositories. You can verify the current push URLs for a remote by running `git remote -v`.

You can add as many push URLs as you want for a single remote, but keep in mind that this will affect all the branches that use that remote as their upstream. If you want more fine-grained control over which branches go to which remotes, you can use the next method.

## Creating Separate Branches

Another way to push your code to two remotes is to create separate branches for each remote and set their upstreams accordingly. For example, if you have a branch called `master` that you want to push to your main repository, and another branch called `deployment` that you want to push to your deployment repository, you can do something like this:

```bash
# create the deployment branch from master
git checkout -b deployment master

# add the deployment remote
git remote add deployment git://another/repo.git

# set the upstream for the deployment branch
git push --set-upstream deployment deployment
```

This will create a new branch called `deployment` that tracks the `deployment` remote, and push it there. Now, whenever you want to update your deployment repository, you can simply run `git push` from the `deployment` branch.

You can also merge changes from your `master` branch into your `deployment` branch before pushing, or use rebase or cherry-pick if you prefer. The advantage of this method is that you can have different histories and configurations for each remote.

## Conclusion

In this post, I showed you two ways to push your code to two remotes using git: adding multiple push URLs for a single remote, or creating separate branches for each remote. Both methods have their pros and cons, so choose the one that suits your workflow best.

I hope you found this post useful and learned something new.

## References

- [Git: Push to multiple remotes at once - Leigh McCulloch](https://leighmcculloch.com/posts/git-push-to-multiple-remotes-at-once/).
- [github - Git - Pushing code to two remotes - Stack Overflow](https://stackoverflow.com/questions/14290113/git-pushing-code-to-two-remotes).
- [How To Use git with Multiple Remote Repositories - How-To Geek](https://www.howtogeek.com/devops/how-to-use-git-with-multiple-remote-repositories/).
- [Git: Pushing to two repos in one command - Stack Overflow](https://stackoverflow.com/questions/5620525/git-pushing-to-two-repos-in-one-command).
