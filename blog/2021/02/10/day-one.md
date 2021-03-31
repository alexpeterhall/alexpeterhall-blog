---
title: Day One
description: First post on the bloggy blog
date: 2021-02-10
readingTime: 1 minute
tags: ["website", "hosting"]
---

It's the first day after I've committed to blogging in a completely non-committal way (haven't made this public yet) so I'll write a quick update. This site is built with [Eleventy](https://www.11ty.dev/), which I've never used before yesterday, so I spent a bit of time learning how it works. I actually bootstrapped this site with [eleventy-base-blog](https://github.com/11ty/eleventy-base-blog) to get started. Out of the box it's using [Nunjucks](https://mozilla.github.io/nunjucks/) as a template engine, which I also have no experience with.

So far it all seems pretty straight forward so I'm working on setting up some bare-bones templates and styles and then I plan to make this my new live website. My current website is two static HTML pages styled with [Bootstrap](https://getbootstrap.com/) and is just a landing page and an online version of my résumé. I think it will be a nice change of pace to have a more casual and personal site and leave the résumé stuff on my Linkedin profile.

The other thing I'm looking into is switching web hosts. I'm currently using a traditional host that I've hosted some CMS websites with for many years. I've retired all of those sites and don't need the traditional [LAMP stack](<https://en.wikipedia.org/wiki/LAMP_(software_bundle)>) host anymore. Since static-site generators and [Jamstack](https://jamstack.org/what-is-jamstack/) are all the rage, I'm considering moving over to one of the popular players in that space. Netlify and Vercel typically top the lists but I also see Cloudflare Pages has an offering and I already have a Cloudflare account that serves up my current site on their CDN. A bit more research is required, but it would be nice to have a more modern host where I can deploy to production by pushing directly to a GitHub repository.
