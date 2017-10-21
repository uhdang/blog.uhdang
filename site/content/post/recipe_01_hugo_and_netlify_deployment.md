---
title: "Recipe #1. Victor Hugo and Netlify deployment"
date: 2017-10-16T06:46:07+02:00
draft: false
---

The first recipe is for creating this very website.
For a simple static website creation, a combination of **Hugo** and **Netlify** is quick and simple.
Instructions are well written in a blog post from **Netlify**.
I will post a link and list some useful commands.
<br><br>

## A Step-by-Step Guide: Victor-Hugo on Netlify

https://www.netlify.com/blog/2016/09/21/a-step-by-step-guide-victor-hugo-on-netlify/
<br><br>

## Useful Commands and Notes

Command for creating a new page. 
Starting from the base directory, you are making `first.md` file in site/content/post directory

```
$ cd site
$ hugo new post/first.md
```

Something to remember when updating theme.
Unlike theme updating in regular _Hugo_ quickstart tutorial, base directory for updating theme has to be '`/site`' , not '`/`'
<br><br>

## Font Change in a Hugo Website

https://stoned.io/web-development/hugo/How-To-Dynamically-Use-Google-Fonts-In-A-Hugo-Website/

* This technique will be useful when you have a customized theme of your own. 
