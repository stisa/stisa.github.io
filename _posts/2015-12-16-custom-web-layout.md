---
date: "2015-12-16T13:51:04+01:00"
draft: false
title: "Snow/Luxe: Custom Web Layout"
layout: post
tags: ["snow", "luxe", "example", "layout"]
---

Hi everyone, this is just a quick post on a simple thing I found out today.
When building for the web target with `luxe`, the default layout is just a black background with the game window in the middle.
While this is okay while developing, I wanted to show a demo of the game on my website, so I wanted its layout to fit with the rest of the site.  

After a bit of digging (all right, a lot of digging, but only because I thought the layout was in `flow`, as it is the one responsible of building)
I found the default layout inside `snow` lib folder, `snow\flow\web\index.html`, where `snow` is the path in which you installed `snow`.
The format suggests that it uses [handlebarjs](http://handlebarsjs.com/) or a similar templating system.  

![Default layout](/media/custom-web-layout/before.png)
![Custom layout](/media/custom-web-layout/after.png)

There probably is a better method of doing this on a per-project basis, maybe by defining a custom template in the flow file somewhere or by using
 a hook that builds your custom layout, but this is a quick way of fitting a large number of projects with a consistent layout.

 Thanks for reading, if you find errors or if I got something wrong, please [open an issue on github](https://github.com/stisa/stisa.github.io)