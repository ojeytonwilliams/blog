---
title: .gitignore vs /git/ignore
---

AKA how to have a "scratchpad" that's ignored across multiple repos.  Credit to my pal [Mrugesh Mohapatra](https://mrugesh.dev/) for gently pointing me in the right direction.

## The problem

It's often useful to store some notes about what you're working on.  If that is a git repository, the best place for those notes is the repository itself (so it's clear what the notes pertain to). However, that can (will) lead to you committing, and likely pushing, things that nobody else was supposed to see.  At best that's a bit embarrassing.  At worst it's a security breach.  So, first, what NOT to do.

## Global .gitignore

If you're like me, you've skimmed the [gitignore docs](https://git-scm.com/docs/gitignore), but you haven't read the damn thing.  You might have read that git picks up

> Patterns read from a .gitignore file in the same directory as the path, or in any parent directory

and skimmed the parenthetical comments (after all, who would put anything useful between brackets).  If you did this, you might have thought that putting a `.gitignore` above all your repos would do the trick.  Nope.  Here's what is in the brackets:

> up to the top-level of the working tree

That is to say: up to and including the directory with `.git` in it.  Any `.gitignore`s above that do not matter in the slightest.

## /git/ignore

If you're not like me, you will have read the bullet point that starts with

> Patterns which a user wants Git to ignore in all situations

Which is exactly what I want to do.  The docs explain it nicely, but I'll reiterate here.

Git has a global ignore file that defaults to `<config-dir>/git/ignore` where `<config-dir>` is `{$XDG_CONFIG_HOME:-$HOME/.config}`, in bash speak. That is to say: `$XDG_CONFIG_HOME` if it exists or `$HOME/.config` if it does not.  You can configure this by setting `git config --global core.excludesFile`, but typically the defaults will do just fine.

Now you can add

```gitignore
/.scratchpad
```

to your global ignore file and not have to worry about committing anything you write there.  And by "there" I mean any `.scratchpad` folder in any repo.

## Unignoring

The global ignore file has the lowest precedence, so it's easy to negate the ignore pattern by adding

```gitignore
!/.scratchpad  
```

to either a given repo's `.git/info/exclude` file or it's `.gitignore`. 

## dot-files

I've seen some advice to use `~/.gitignore` as your global ignore file (i.e. configure `core.excludesFile` to point to that file).  Don't do this.  That `.gitignore` should be where you ignore all but your dot-files (`.bashrc` et al.) which is in direct opposition to "ignore just this one folder". If you want to commit your ignore file then make _it_ into another dot-file.  I.e. add it to your home `.gitignore` like this:

```gitignore
# ignore everything
*

# allow specific dotfiles
!.bashrc
# nested folders need their patterns negating one by one.
# first the config folder (but no files)
!.config
# then just the git subfolder (still no files)
!.config/git
# finally all the files in the subfolder
!.config/git/*
```

and now you can make your home folder into a git repo and commit those specific files and just those files.

