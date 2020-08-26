---
datePublished: 2018-06-21
dateModified: 2018-06-21
title: Don’t Let Your Love for Code Make You a Bad Engineer
author: cade-scroggins
description: >-
  At the end of the day, our clients don’t pay us to code—they pay us to solve
  problems.
---

If someone offers me \$10k to help them sell their hand knit mittens online, I’m
not going to build a custom e-commerce website from scratch. Instead, I’ll shoot
them a link to Shopify and advise them to keep their coin. What do you do,
however, if you’ve never heard of Shopify or the plethora of other WYSIWYG
e-commerce solutions?

At Spraoi, we provide an “insurance as a service” platform called Kwikcover.
Kwikcover is built such that we can add, remove and reuse features on a
per-client basis. Oh, and it scales—not just in the number of users that can buy
insurance at the same time, but also in the number of engineering teams that can
work on the codebase simultaneously without stepping on each others toes.

To achieve this, we launched domain specific APIs (read: “microservices”) for
users, products, policies and payments (among others), but it wasn’t immediately
obvious how we should tie these services together.

When one of our UIs makes a request with a user id, product id, and some payment
info (i.e someone is buying insurance), which API should handle it? If you’ve
never worked with microservices, your initial thought might be payments. The
payments service would make a call to the products API to fetch the product
info, then, after successfully executing the transaction locally, would create a
new policy by making a call to the policies API.

That would technically work, but then our payments service ends up with logic
that isn’t directly related to payments. A better solution is to have a separate
service specifically built to orchestrate requests. This is one of the first
problems I was tasked to solve when I joined the Spraoi team.

After doing some research, I was ultimately convinced that there weren’t any
tools out there that met our specific needs. As such, I happily started writing
a custom solution from scratch. After a few days of hacking together some
Javascript and throwing it on AWS Lambda, we had the original version of what we
now call the “request composer”.

Each new endpoint exposed to our UIs has to declare a “request sequence” that
determines how that request should be orchestrated. This sequence is defined in
a domain specific language (DSL) that only the request composer can parse, and
its syntax was conjured by yours truly. This boils down to the fact that, if you
need to create a public endpoint at Spraoi, you have to write, or at least
copy/paste, this DSL.

To make it possible for others to write these sequences, I’ve written tests and
documentation, and attempted to keep both up-to-date. However, when things go
wrong, or another requirement comes through, I’m still on the hook. The kicker?
It’s possible that the majority of this work could have been avoided.

Well after the creation of the request composer, I stumbled across another AWS
service: Step Functions. The first paragraph on their homepage states:

> AWS Step Functions makes it easy to coordinate the components of distributed
> applications and microservices using visual workflows.

It was almost painful to read. Is that not exactly the purpose of our request
composer? Also, “visual workflows” sounds user friendly&hellip; Hindsight is
20/20, but I feel pretty silly for not finding this and trying it out before
attempting to build an in-house solution.

A chef probably prefers a self-prepared meal over takeout. Similarly, writing
code to solve a tasty problem is where most developers feel at home. More code,
though, means more time spent testing, debugging and documenting code.

This might have been a long-winded way of saying “reinventing the wheel is
expensive”. However, keeping this story in mind when you’re tasked with solving
a “new” problem could be the difference between coming three feet from gold and
striking it.
