---
title: "Metal Jokes API (FastApi + Deta)"
description: "Python API written using FastApi and deployed to Deta"
date: "2022-03-28"
categories:
tags:
  - "api"
  - "python"
  - "fastapi"
  - "deta"
  - "music"
  - "blog"
---

## Intro

This week's project was originally intended to build on top of the [Web Scraper - Band Tour Dates Nearby](https://dalton.black/posts/tour-web-scraper/) by exposing it via an API. My goal was to enable users other than myself to use the web scraper I had built in such a way that they could use the API in their own applications.

As you can probably tell by the title of this post, something changed. Because of this, as well as some other unexpected obstacles, the majority of what I learned this week didn't actually have to do with building APIs. Instead, I learned about differences between Python dependency & package management tools, how to effectively leverage specific versions of one project from within another, and certain types of dependencies to avoid when deploying applications to the cloud.

As a teaser for where this project ended up (even though the title definitely gives this away) here are some [friendly faces](https://en.wikipedia.org/wiki/Dethklok) to welcome you to this post:


![dethklok](/images/dethklok.jpg)


## Building the API (and everything that came before it)

### Migrating from `pipenv` to `poetry`

![poetry](/images/poetry.jpg)

The first thing I realized when I started this process was that I wanted to keep my web scraper repository separate from the API repository. This separation would make it easier to develop each application independently, without interfering with the other.

The top Python repository I knew about was [PyPi](https://www.pypi.org). Since I had used `pipenv` to manage the dependencies for my web scraper, I also used it to set up this API project. After doing the basic `pipenv` setup for this project, I started trying to figure out if I could use `pipenv` to publish my web scraper to PyPi for my API to add as a dependency. After Googling, I learned that `pipenv` doesn't support this behavior.

I started searching for the best way to package Python packages, and came across [poetry](https://python-poetry.org/). `poetry` can handle dependency management as well as publishing Python packages to PyPi. This brings the best of both worlds, and you can see the details of the package manager conversion [in this commit](https://github.com/daltonmblack/tour-scraper/commit/aae8c8339b970137fea8f73af5fdb59911630c47).

### Writing a "Hello World" API

After migrating to `poetry`, I wrote a skeleton API using [FastApi](https://fastapi.tiangolo.com/). I chose FastApi because I've heard lots of hype about it and am also starting to interact with it at work, so it felt like a good choice to play around with. It only took a few minutes to set up, and it turned out to be super condensed as well. Here's essentially all the code that you need for a working API:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
  return {"Hello": "World"}
```

### Figuring out where to deploy the API (Deta to the rescue!)

Now that I had some skeleton API code running locally, I wanted to figure out where I was going to deploy the API. Since I wasn't building an application that I intended to make money from, I wanted the hosting to be as cheap as possible, if not free. I looked around for a while, and starting digging into AWS Lambda because I knew it was reliable, and despite the fact that my AWS account is long passed its free tier trial, it was still cheap for how much traffic my API would be getting.

I did some initial testing and published the FastApi "Hello World" example from above to Lambda. At this point I was thinking through whether this was the right choice, especially for how much work/complexity it would take to publish updates to my API whenever I made them. In reality, it's very likely not a big deal to make efficient deployment pipelines to Lambdas, but I still find AWS ramp-up pretty steep. So, I went back to searching for other hosting solutions. That's when I came across a machine learning blog post ([Deploying Your First Machine Learning API](https://towardsdatascience.com/deploying-your-first-machine-learning-api-1649236c695e)) talking about deploying to this site called **Deta**.

The blog post claimed that *"It took me one hour to learn FastAPI and five minutes to learn how to deploy it to Deta servers"*... That sounded fantastic! It also was directly relevant to my project because I was using FastApi too. I went to [deta.sh](https://deta.sh) and read up on what the product was, and why they built it. The creators describe a vision where they want to provide a space for people to host their personal/school projects without having the barrier-to-entry of providing a credit card. Deta provides: compute, database, storage, auth, and cron functionality. All for free! The way that the creators are able to pay for the hosting is that they are building a separate product at their startup, and they will be diverting some of its profits to indefinitely support Deta. I was sold at this point.

Setting up Deta for a project was super easy and legitimately only took a couple of minutes. IIRC, the only commands it took to set up (AND DEPLOY) were:

```bash
deta login
deta init
deta deploy metaljokeapi
```

At that, my API was running live and publicly accessible on Deta's site!

### Converting from a web scraper API to a metal joke API

Now that I had a working deployment setup for my API, I circled back to its original goal: exposing my web scraper API. I quickly realized this was infeasible, because the web scraper used Selenium, which required a headless browser to run. See my previous post [Web Scraper - Band Tour Dates Nearby](https://dalton.black/posts/tour-web-scraper/) for more details about that. I don't believe it is possible to run this sort of application on Deta.

So with little time remaining to work on this project, I did a last minute pivot. I'm a huge metal fan (as is probably unsurprising by me choosing a metal band for the web scraper project), so I decided to make a simple metal joke API. I compiled a small list of simple metal jokes from Reddit, and set them up to be served from my API.

If you want to test out the API for yourself, and also laugh at a cheesy metal-themed joke while you're at it, check it out here: [33szdz.deta.dev/random_joke](https://33szdz.deta.dev/random_joke)

## Conclusion

As anyone who builds personal projects (software or otherwise) knows, every project has so many hidden surprises and obstacles that need to be overcome. However, it is from these obstacles that I often feel like I learn the most. This project was the perfect example of that. I did end up learning about building a simple Python API using FastAPI, but I learned more about Python package management & publishing with `poetry`, and I also learned about the existence of the awesome project hosting site Deta. This knowledge will unavoidably help me on future projects, and I'm very glad I took the time to sit down and work through this project.

## Resources/Appendix

* [Metal Joke API](https://33szdz.deta.dev/random_joke)
* [Metal Joke API repository](https://github.com/daltonmblack/metal-joke-api)
* [Deta](https://deta.sh)
* [FastApi](https://fastapi.tiangolo.com/)
