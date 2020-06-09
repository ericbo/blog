---
title:  "Homelab Update"
mathjax: true
layout: post
---

![Swiss Alps](/images/homelab/dell-1950.JPG)

## First Attempt

Back in late 2016 I had managed to find a lot of Dell PowerEdge 1950's for free and a bunch of 24 and 48 port network switches for $40.
With all of this hardware I decided to setup a homelab of my own. This being my first time ever seeing/using a commercial server,
I was not prepared for the amount of noise and heat they produced. This coupled with the large power usage, I opted to run a single
PowerEdge in my stack. I installed RangerOS with the hopes of slowly migrating all of my projects and automation tools to docker.

In the meantime I decided to do some more reasurch on commercial server, and stumbled across the the /r/homelab reddit. I found the
goto server at the time was the Dell r710. However within the first few months of me reciving it I purchased my first home. Ever
since the move I have never found the time to continue working on my homelab. I was also sharing my home with someone else, so space
was more scarse. Slowly I started selling off all my equipment and my r710 collected dust sitting in my shed alongside the lawnmower.

![Swiss Alps](/images/homelab/dell-r710.jpg)


## Second Wind

During christmas of 2019, I had taken 2-weeks off of work and had a lot more free time to myself. A good friend of mine had setup
a media server and a handful of other self hosted services. Back in 2013 I had assembled my first NAS using

## Gists

With the `jekyll-gist` plugin, which is preinstalled on Github Pages, you can embed gists simply by using the `gist` command:

<script src="https://gist.github.com/5555251.js?file=gist.md"></script>

## Images

Upload an image to the *assets* folder and embed it with `![title](/assets/name.jpg))`. Keep in mind that the path needs to be adjusted if Jekyll is run inside a subfolder.

A wrapper `div` with the class `large` can be used to increase the width of an image or iframe.

![Flower](https://user-images.githubusercontent.com/4943215/55412447-bcdb6c80-5567-11e9-8d12-b1e35fd5e50c.jpg)

[Flower](https://unsplash.com/photos/iGrsa9rL11o) by Tj Holowaychuk

## Embedded content

You can also embed a lot of stuff, for example from YouTube, using the `embed.html` include.

{% include embed.html url="https://www.youtube.com/embed/_C0A5zX-iqM" %}
