---
title: How this site came to be
---

## Introduction

I'll likely dive into the why of the blog in a later post, but here's the how.  At least, it will be after a brief justification.

I'm learning Haskell.  Slowly and painfully, but that's what I'm doing.  As such, I thought it might be fun (ha!) to set up a blog using a Haskell powered static site generator. I also thought it might be fun (or useful) to document the process.  This will not be a how-to guide; it will be a lot of self-recrimination about the steps I took. Some of them quite silly. Okay, that's enough ado.

## Haykll

Yes, I know it's called Hakyll.  I know that _now_, but I spent an inordinate amount of time trying to figure out why searching for [Hakyll](https://www.stackage.org/lts-24.58/hoogle?q=haykll) on Stackage yielded nothing, despite the fact it had to be there!

So, what lead me to waste my time with a typo?  After setting up the site locally, I wanted to get deploying as soon as possible.  The site was as simple as it was going to be and so this was the best time to get to deploying it.  So I did. [This is the first commit with an action](https://github.com/ojeytonwilliams/blog/commit/59d6376e84c7f878caf751f23c7a2129d4092d84), that had broken yaml, so [here is the yaml for first valid action I pushed](https://github.com/ojeytonwilliams/blog/blob/2b27cf4a043758cf117c92cd668de1d32bb357fd/.github/workflows/deploy-pages.yaml). Valid, but incorrect (as the little red cross attests): "Executable named site not found on path: ..." and, because I'd somehow forgotten how to debug, I thought "huh, maybe Hakyll isn't in that snapshot". So I went looking.  Badly.

### Digression on stack exec

`stack exec <name-of-bin>` slightly surprising if you haven't thought about it (ahem).  My naive assumption was that it would, well, execute something stack related. It does, but you can `exec` basically anything.  `stack exec ls` will list the files in your directory (assuming there isn't a different `ls` binary that you just built with stack). The idea is that any sub-commands will be executed in the same environment

If you're curious you can use `stack path --bin-path` to see the `PATH` value it uses.  Since stack prepends `.stack-work/...` to your path it will pick up any binaries you built for this project preferentially over other binaries later on the path.  As a result any other command invocations will get this path, so if your project builds a bunch of binaries then they will all be on the path and execute as you might expect. This is good!

 There is a small footgun, though. If the binary doesn't exist in the project, but a version does live somewhere else on the path, then that version will be executed.  Chances are this isn't what you want.

## Getting somewhere


Some time later I started thinking again, went back to see how other people set up their actions and realised that everyone else was pinning ghc and stack.  I still don't 100% get this, since I thought that was `stack.yaml.lock`'s job. I will experiment with leaving them out. Anyway, that takes us to [this commit](https://github.com/ojeytonwilliams/blog/commit/ee1fa38cbfec891bf9feb52a95eff4124ce438fa). Still no!

A bit more staring at other people's actions and head-scratching eventually revealed that I'd missed a step. `stack build` needed to happen before `stack exec` because `site` is an executable and it didn't exist.  The error was pretty clear, I just didn't read it properly.  Fixing that took me to [this commit](https://github.com/ojeytonwilliams/blog/commit/c3e6ee513c0c804292e8119aa70bbea0fab02a83) and this failure:

```
Error: Error: Failed to create deployment (status: 404) with build version c3e6ee513c0c804292e8119aa70bbea0fab02a83. Request ID E839:274D5A:2BE1F5B:8DE03C4:6AA1D65E Ensure GitHub Pages has been enabled: https://github.com/ojeytonwilliams/blog/settings/pages
```

Progress! Finally an error that even I could not misinterpret.

## Jekyll?

After enabling pages, I triggered a deploy by pushing a commit to allow me to trigger deploys _without_ pushing a commit. And... still no.  This time I got a page (this one!), but it was just the raw `index.html` including `$partial(...)` amongst other things I wasn't expecting. This was peculiar, but not too hard to debug. In part because when I checked the build logs I saw "Build with Jekyll" making it obvious that the pages configuration was out of whack.  Going back to the settings page I noticed "Learn how to add a Jekyll theme to your site.". Uh oh.

Yep, while I'd created the action (correctly, finally), I had not configured pages to use the action when deploying.  Fixing that was simply a matter of changing the "Source" from "Deploy from a branch" to "GitHub Actions".  And it was done.


## Coda on general ignorance

One of the interesting things about trying to do something in an area you know little about is just how stupid it makes you.  If you're at least aware of most of the moving parts, you find yourself staring fixedly at a ton of different black boxes of mystery, any of which could be broken. Since you don't understand any of them (not really), you can't be sure what should be ignored and what should be prioritised first.  So you (well, I) guess.  And that goes as well as you might expect.

I'm not claiming this is a particularly novel or deep observation. However, it does continue to surprise me and it does reinforce the idea that learning stuff is quite helpful. If I had know nothing about actions or pages coming into this, I suspect I would have been somewhat overwhelmed.

And with that, I shall stop.
