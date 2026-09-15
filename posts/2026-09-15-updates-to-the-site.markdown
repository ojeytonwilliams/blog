---
title: Cleaning up and improving the site 
---

## Performance

Hakyll is a surprisingly big beast.  As I understand it Pandoc is the bulk of it, but still - it's pretty slow to compile. It took ~20 minutes for the action I cobbled together in my [first post](2026-09-11-how-did-this-happen.html) to build the site.

So, what to do?  Well, there's [this guide](https://jaspervdj.be/hakyll/tutorials/github-pages-tutorial.html) which boils down to "build the site yourself, commit it and push the whole thing". This is solid advice, but I want to see if I can get caching to work nicely in practice.  There's already an action called [stack-action](https://github.com/freckle/stack-action) which means there shouldn't be much for me to do.

My [first attempt](https://github.com/ojeytonwilliams/blog/commit/9ef3a4c8e885d8336d4c88ce566379f8e772e3f0) went as well as you might expect (if you read the first post).  I kept things simple and just used the action without any config. Naturally this failed.  This time because the action defaults to passing `--pedantic` to `stack build` and Hakyll isn't pedantic.

My [second attempt](https://github.com/ojeytonwilliams/blog/commit/fdcada7dded0a637b0df017075d48d555d660d3e) worked! Easy as that, thanks to `stack-action` being pretty ergonomic.

It remains to be seen what happens when I start changing code, but hopefully the caching is smart enough to only get invalidated if `stack build` would need re-running.  Let's see! (EDIT: yep! Discussion at the bottom).

## Clean up

RIP lorem-ipsum.

### Update post-deployment

Magic!  The action works beautifully and now building the site takes ~30-40s.  I should say: downloading the cached build takes ~30-40s, building the site itself took less than a second.  It's hardly surprising since it's generating all of 7 pages, but that's pretty great.

Also, as I suspected, you don't need to specify the stack or ghc versions.  They are pinned by the stack lock and everything just works.
