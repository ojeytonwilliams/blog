---
title: Cleaning up and improving the site 
---

## Performance

Hakyll is a surprisingly big beast.  As I understand it Pandoc is the big beast and Hakyll uses it, but still - it's pretty slow to compile. It took ~20 minutes for the action I cobbled together in my [first post](2026-09-11-how-did-this-happen.html) to build the site.

So, what to do?  Well, there's [this guide](https://jaspervdj.be/hakyll/tutorials/github-pages-tutorial.html) which boils down to "build the site yourself, commit it and push the whole thing".  This is solid advice, but I want to see if I can get caching to work nicely in practice.  As it happens, there's an action called [stack-action](https://github.com/freckle/stack-action) which looks promising.

My [first attempt](https://github.com/ojeytonwilliams/blog/commit/9ef3a4c8e885d8336d4c88ce566379f8e772e3f0) went as well as you might expect (if you read the first post).  I kept things simple and just used the action without any config. Naturally this failed.  This time because the action defaults to passing `--pedantic` to `stack build` and Hakyll isn't pedantic.

My [second attempt](https://github.com/ojeytonwilliams/blog/commit/fdcada7dded0a637b0df017075d48d555d660d3e) worked! Easy as that thanks to `stack-action` being pretty ergonomic.

It remains to be seen what happens when I start changing code, but hopefully the caching is smart enough to only get invalidated if `stack build` would need re-running.  Let's see!


## Clean up

RIP lorem-ipsum.
