---
title: "Web Scraper - Band Tour Dates Nearby"
description: "Python web scraper which scrapes band web pages to identify nearby upcoming concerts"
date: "2022-03-13"
categories:
tags:
  - "webscraper"
  - "python"
  - "music"
  - "blog"
---

## Intro

I've heard people talk about building web scrapers for years. Projects ranging from gathering research data to building machine learning bots to win at online games. Building one of these has been on my TODO list for a long time, and this week I finally made it happen.

### What is a "Web Scraper"?

A web scraper is an application that retrieves one-or-more web pages, analyses their contents, and then extracts specific pieces of data. A concrete example of a web scraper would be a web scraper that searches major news websites for mentions of a specific topic like cryptocurrency, and then aggregates the results.

### What does my Web Scraper Do?

The web scraper I decided to build would search a list of websites for band's I want to see live, scrape their upcoming tour dates/locations, and then identify which bands had shows in a specific state. I did this because I find it tedious to keep checking a bunch of band websites by hand to look for tours, and I also hate subscribing to email update lists because I get enough email as-is. So, a web scraper that does all this work for me in a matter of seconds, whenever I want, would be super awesome.

The band that I chose for this initial version of the project is [Jinjer](https://www.youtube.com/watch?v=SQNtGoM3FVU). Jinjer is a Ukrainian metal band which combines a wide array of sounds/styles with metal in order to create a unique sound. I highly recommend checking them out.

![jinjer_band](/images/jinjer_band.jpg)

## Building the Web Scraper

The code for my web scraper can be found at: https://github.com/daltonmblack/tour-scraper#tour-scraper

### Initial Steps

Since I had never built a web scraper before, I started by Googling "how to build a web scraper in Python?" based on the fact I knew Python has powerful HTML parsing libraries like [Beautiful Soup](https://beautiful-soup-4.readthedocs.io/en/latest/).

It was pretty simple to build a quick dummy application which downloaded a web page and retrieved a specific section.

### Parsing Jinjer's 'Upcoming Tour' Page

At this point I was ready to move onto scraping a real web page. I pointed my scraper at Jinjer's [upcoming tour page](http://jinjer-metal.com/tour). My strategy for figuring out how to extract the details I wanted (tour dates + locations) was to manually inspect the HTML of the web page. Within this HTML I was looking for either CSS id's or classes.

Here's an example snippet from the Jinjer tour page:

```html
<div class="bit-event">
  <a class="bit-details" ...>
    <div>
      <span class="bit-date">Sun, MAR 13</span>
    </div>
    <div class="bit-titleWrapper">
      <div class="bit-venue">The Factory</div>
    </div>
    <div class="bit-location">Chesterfield, MO</div>
  </a>
  <div class="bit-details bit-event-buttons">...</div>
</div>
```

In my code, I told `Beautiful Soup` to look for elements with the classes `bit-date` and `bit-location`. The library couldn't find any instances. This confused me for a while, and I tried to identify what I was doing wrong in my code. After about 10 minutes, I was examining the full scraped HTML and realized that the missing elements I was looking for were probably retrieved via Javascript that runs on the client browser when the page is loaded. It only took a minute to verify that this indeed was the case.

Now I had to figure out how to run this Javascript code during web scraping. I investigated if `Beautiful Soup` could do the job, and it didn't seem like it. After searching for the right tool to do this, I chose [Selenium](https://www.selenium.dev/). `Selenium` is a tool often used for automating web UI/UX testing. Using Selenium I was able to retrieve web pages, run their JavaScript, and scrape the resulting rendered web page. I was able to successfully extract elements I was looking for: location and date.

### Implementing the Business Logic

Now that I could scrape, the remaining work to be done was using this information to answer the question of "are there any upcoming concerts near me?". For simplicity's sake, I made a CLI application that accepts a state as a parameter (e.g., "WA") and prints out all upcoming concerts in that state from any of the bands built-into the application.

Implementing storage and categorization of the scraped data was simple for my single band use-case, but likely will require some additional thought as I add support for additional bands into the application.

The final point of interest was my choice to try out the Python CLI library [click](https://click.palletsprojects.com/en/8.0.x/). It is an alternative to the widely-used [optparse](https://docs.python.org/3/library/optparse.html) and some of its stated benefits are:
* Arbitrary nesting of commands
* Automatic help page generation
* Supports lazy loading of subcommands at runtime

This use-case of click was pretty basic, and I plan on trying it out in more complex situations in future projects.

## Conclusion

It was satisfying to get my first web scraper working. I expect this project specifically will be a good jumping-off point for future projects since there is so much information on the Internet that isn't set up to be consumed programmatically.

I also plan on expanding the scope of this project since I actually would use the product myself. Being able to search all of my favorite bands for upcoming concerts in my state would be so helpful.

## Appendix/Resources

* Project GitHub repo: [tour-scraper](https://github.com/daltonmblack/tour-scraper#tour-scraper)
