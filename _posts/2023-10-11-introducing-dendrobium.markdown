---
layout: post
title:  "Introducing Dendrobium!"
subtitle: "A hobbyist gardener's companion for plant care"
date: 2023-10-11 01:15:24 -0800
author: oliver_puffer
categories: Devlogs
tags: Dendrobium Devlog Django

banner:
  loop: true
  volume: 0.8
  start_at: 8.5
  image: /assets/images/banners/rock-garden.jpg
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
---

## Introduction

# For those of you who don't know me, I am a fairly avid gardener. 

I collect rare and unique plants (sustainably sourced, of course) for an indoor plant collection that I have been contributing to and maintaining since my time in college. Recently, my partner has begun attending medical school at  [The University of California Davis School of Medicine][UCDSOM] in Sacramento, CA. There is far more outdoor space here in Sacramento, as opposed to in our tiny apartment in the Bay Area, which means I have been able to expand my plant collection to include a variety of northern california native plants as well. 

We also had a neighbor, upon hearing that we were moving, very generously offer us a variety of cuttings and house plants as well, to kickstart my expanded collection.

As a result, my collection of plants has rapidly expanded to fill the newly available space, and for the first time I have begun to encountered difficulty keeping track of the requirements and watering schedules for each of them. 

Annnd then I got fungus gnats from a plant I bought online. And then the fungus gnats went for the cuttings I was attempting to propagate. 

With the new location, the new watering and light requirements, and the pests, my system for maintaining my collection began to break down. I needed to apply pest treatments 1 once a week, reacclimate all my plants to the new weather, water once every 7-10 days, re-pot plants that were overdue, fertilize plants, put shade cloth over plants that were getting sunburned, and generally manage a lot of added complexity.

So being who I am, I began to search for technology that could assist me in maintaining my plant collection. Not just for watering, but also in regards to maintenance, pruning, weatherproofing, and [neem-oil]/[fungicide]/[beneficial-nematode] application.[^1] 

Unfortunately, the apps currently on the market left a lot to be desired, they were either too rigid (you must water once a week on the dot), too feature limited (no support for fertilizer/pesticide application), or really expensive (\\$8-\\$20 a month). [^2]

Which is why, I am going to build one. I have named this project [Dendrobium].

## What's in a Name?

# I wanted to pick a good name for this project. 

Which is easier said than done, it takes seconds to register a domain name, which means most of the good ones were taken hundreds of millions of seconds ago. After several days of trying different combinations, I  settled on [Dendrobium] for a number of reasons.

1. The word Dendrobium refers to a genus of epiphytic[^3] (and occasionally lithophytic[^4]) Orchids
2. People often struggle to care for orchids, in large part because the way they are presented for sale often contradicts their needs. This is an issue my app will seek to mitigate. 
  * Orchids are often presented like any other plant, in a pot, with their roots covered in growth media.
  * Orchids are Epiphytic, which means instead of from potting mixes, they want to receive their water an nutrients in the form of humidity and moisture collecting on the side of a tree
  * The most natural way to grow orchids is mounted on a piece of wood in high humidity environment such that the roots dry quickly after getting wet. While potted orchids can be grown with success, it requires very different, well draining "soil".
3. Dendrobium includes the greek root word "Dendron" meaning tree/plant and "Bios" meaning life. While this is in reference to how they grow in the wild. These concepts are also relevant to the project.
4. [Dendrobium.io][Dendrobium] was an unclaimed domain

## First Steps

# I already a splash page successfully hosted at [Dendrobium.io][Dendrobium] including a nonfunctional preview of what the site layout might look like.

I am not 100% satisfied with how it looks, but I think it is good enough for now. Currently it is a static site hosted with github pages, but this app will need a functioning backend in order to deliver the experience I am targeting. I have decided to develop the core application with the Django framework, because of its versatility, built in admin panel, security focus, and support of Python programming language. With this decision out of the way, it is time to setup our development environment and pipeline. 

The steps are:
1. Create Dev Environment
2. Implement boilerplate and serve a splash page to verify functionality.
4. Select and Setup Database (PostgreSQL is my favorite, but is it the best match for this application?)
3. Determine Where and How to Deploy (I will probably start with Heroku because I already have underutilized eco dynamos provisioned for other projects)
4. Setup Continuous Integration Automated Testing with Github Actions
5. Setup Terraform (Probably Overkill for now, but if I start defining infrastructure from the start, it will greatly help with scaling if I decide to add a CDN, Multiple DBs, or more microservices)
6. Setup Continuous Deployment to Heroku Test Branch

Once I get all this out of the way, I can begin to implement features. I will begin with the basics; Exposing DB actions through a REST API, handling User Authentication, deciding how to define the relationship between different models, allowing users to add/edit/remove plants from their "garden" and input care notes. Once all of this is in place, I can setup the backend to process these notes and develop a care schedule.

For now though, it is the boilerplate. Stay tuned for updates!

# Footnotes:
[^1]: By the way, Beneficial Nematodes were actually super effective at solving the fungus gnat issue. 10/10 would recommend

[^2]: Don't worry, my plants all turned out ok. There is one Rhaphidophora tetrasperma that was already unhealthy that I am propagating because it did not move well, but otherwise all plants are doing much better now, and my uncommon propagations have been successfully transferred to soil.
[^3]: Epiphyte - A plant that grows off of another plant, as opposed to in soil.
[^4]: lithophyte - A plant that grows on rock faces

[UCDSOM]: https://health.ucdavis.edu/medical-school/
[neem-oil]:https://www.amazon.com/Bonide-BND022-Pesticide-Organic-Gardening/dp/B007CRG4CW/ref=asc_df_B007CRG4CW/?tag=hyprod-20&linkCode=df0&hvadid=416810682178&hvpos=&hvnetw=g&hvrand=5982816398489676476&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9032529&hvtargid=pla-902339632319&psc=1&tag=&ref=&adgrpid=99569067251&hvpone=&hvptwo=&hvadid=416810682178&hvpos=&hvnetw=g&hvrand=5982816398489676476&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9032529&hvtargid=pla-902339632319
[fungicide]: https://www.amazon.com/Bonide-Copper-Fungicide-Rtu-Natural/dp/B000UJVDXY/ref=sr_1_4?crid=3RO7F3I1YZIL3&keywords=copper+fungicide+spray&qid=1697046944&s=lawn-garden&sprefix=copper+fu%2Clawngarden%2C144&sr=1-4
[beneficial-nematode]: https://www.amazon.com/NaturesGoodGuys-Live-Beneficial-Nematodes-Million_Nematodes/dp/B07DQT735W/ref=sr_1_5?crid=L2SSGZ3LDEZQ&keywords=beneficial+nematodes&qid=1697046964&s=lawn-garden&sprefix=benef%2Clawngarden%2C140&sr=1-5
[Dendrobium]:https://dendrobium.io