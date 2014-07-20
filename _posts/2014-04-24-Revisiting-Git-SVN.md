---
layout: post
title: "Revisiting Git SVN"
description: "Setting up a shallow clone for Git-SVN"
---

There might come a time when you'll be assigned to a project that is still using SVN. This happened to me a few weeks ago. Because of one reason or another, the project won't be migrated to Git in the near future. I've been using Git for years now, and I love how easy it is to use. Branching, merging, and working even when you don't have a connection are just some of the things I love about Git. That is why I still want to use it.

If you are in a similar situation, don't despair! There is git-svn which you can use for this. Thanks to this [source](http://www.janosgyerik.com/practical-tips-for-using-git-with-large-subversion-repositories/), I was able to set up my local running git-svn.

First thing you need to do is to clone your repository. In my case, the project I wanted to clone was too big that it would probably take me more than a few hours just to clone. If you are in a similar situation, one thing you can do is to just do a shallow clone.

First thing you need to know is how many revisions are there in your repository.

    svn info http://your-svn-repo.com/svn/project-name/trunk

Once you get the latest revision number, you need to specify the start of the revision you want to clone from. For example, if your svn repo has 100000 revisions, and you want to just get the last 20000 revision then you need to pass 80000 in the `git svn clone` command

    git svn clone -r80000:HEAD http://your-svn-repo.com/svn/project-name/trunk project-name

This will only clone the project's trunk beginning from revision 80000 up to the latest change. This is necessary especially if your svn repository has multiple branches. If you need to only work on certain branches then you should clone only those branches.

To do this, you need to update your `.git/config` to look something like this

    [svn-remote "svn"]
        url = http://svn-repo.com/svn/projects/project
        fetch = trunk:refs/remotes/git-svn
        fetch = branches/branch-name:refs/remotes/branch-name

Once you have done this, you need to fetch the revisions from those branches. Again, if your repo is large, it's better to just fetch from a certain revision up to the latest. You can do so through the `-r` flag

    git svn fetch -r 80000

If this doesn't do anything, you need to run the fetch command again without the `-r` flag.

    git svn fetch

This might take a (very) long time depending on how many revisions you want to fetch.

To see whether you successfully fetched the branch:

    git branch -a

You should be able to see the branch you specified in the git config

    remotes/branch-name

You can checkout the branch by

    git checkout -b branch-name-local remotes/branch-name

To know whether you are in the correct branch, run

    git status

And you will see which branch you are currently in. You can now start editing files, and see whether that works.

You might also want to copy the ignored files over to your .gitignore

    git svn show-ignore >> .gitignore

You can either commit this file or add it in `.git/info/exclude`.

If you have changes to commit, you need to commit these locally first

    git add file/you/changed
    git commit -m "My first commit"

Before pushing these changes in production, make sure that you have the latest revisions

    git svn rebase

Once you get the latest changes, you can then commit it in svn through a `dcommit`

    git svn dcommit

And there you have it, a working git-svn.