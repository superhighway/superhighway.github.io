---
title: Playing with PageSpeed
date: 2013-03-14
tags: design, web, performance, tools
---

How is design related to [PageSpeed score](https://developers.google.com/speed/pagespeed/)? After playing around with [a bunch of side projects I built](/about.html), I discovered these 4 things.

1. More images can mean lower PageSpeed score, especially if the images are not optimized. So, if your site has images, be sure that they are optimized.
1. [Flat design](http://fltdsgn.com/) uses color combination, but very few texture background, gradients, and other kinds of images. This can lead to high score. Of course, if images are used, number 1 above has to be fulfilled.
1. Although you use CDN for your Javascript or CSS, the score will be a lot lower if they're not gzipped.<br/>I had 200kb scripts hosted on somebody else's CDN. Then, I gzipped it and hosted those on GitHub. The final size became 20-30kb, but I thought DNS lookup could be a lot slower. Anyway, just by gzipping those scripts, Google PageSpeed gave my site score higher by 30.
1. Look at "Critical Path Explorer" to check how and how long your site assets are downloaded. Though your score is so-so, maybe your site is actually quite decent.

No wonder Gmail page design looks so clean, and it is very performant as well.
