---
title: 4 Tips to Build Fast Websites
date: 2013-07-09
tags: performance, tools, web
---

Sometimes, you need to build websites for showcasing company information, team details, product information, etc. Once you've done your beautiful sites, you wonder why the page loads slowly. There is very little database calls and small amount of back-end web app work.

Take a deep breath.

I'm gonna explain 4 common tips to help you build fast websites, and show you the tools related. Be happy, most of the tools are free. Sit back and grab some chips!

READMORE

**The 4 Tips:**

1. Sanity Check
1. Optimize Images
1. Optimize Javascript and CSS: Combine, Minify and GZip Them
1. Cache Static Assets Using HTTP Cache Headers and/or CDN

## Sanity Check

Are you sure the problem is in the front-end and not on the hosting server? Check it out just to be sure! You will need to examine the network from your website via a development tool in a web browser of your choice.

- Google Chrome: [Use DevTools](https://developers.google.com/chrome-developer-tools/)
- Firefox: [Use Firebug](http://getfirebug.com/html)
- Internet Explorer >= 9: [Use the Network Inspection Tab](http://www.sitepoint.com/debug-faster-with-f12-developer-tools-in-internet-explorer-9/#one)
- Safari: [Enable the Develop Menu in Safari’s Advanced Preferences](https://developer.apple.com/technologies/safari/developer-tools.html)

Reload your page and see which requests take the most time. Out of the slow requests, there are at least three categories below which can be improved.

- The request roundtrip time to your site's page (most probably the HTML markup)
- The request roundtrip time to get images on your site
- The request roundtrip time to get Javascript/CSS files needed by your site

In addition to the development tool, use [Google PageSpeed](https://developers.google.com/speed/pagespeed/) to get to know your page score. A good score is at least close to 90. If you've already done the next three sections I'm gonna explain, you can get around 70-90, which is very close! If you haven't done any optimization, your page scores can be around 30-60.

*If you already get score above 90 but the request time is slow due to slow HTML page response time, feel free to ignore this article!*

## Optimize Images

*If your sites don't have any images, feel free to skip to the next section.*

Why should we think that images can be the culprit of slowness? First of all, if you reach this point, it seems that you use some or plenty of images in your site. Second, their sizes are big if they are not optimized for the web.

Where did you get your images? Image software, your camera, or somewhere online? Chances are images from those sources contain extraneous metadata and lots of colors. Some of these metadata aren't necessary for the web, and some colors can be 'compressed' with other colors. These extraneous data can be removed from the image and save lots of bytes, without actually affecting the image quality so much.

Alright. That's complicated. Why do we even care?

Remember that the Internet speed varies among different regions and countries; some have blazing fast speed, some have moderately slow speed. If your goal is to serve lots of Internet users, you need to consider the number of bytes transferred. You might actually find out that images take lots of bytes and slow down your whole site!

Don't worry! Plenty of free tools for image optimization are available. If you already have images you showcase publicly, you will need some script to automate optimizing existing images and the new ones. The automation can take quite some effort. Nevertheless, it is so worthed when your site has a few big images or lots of small ones.

Before using any tools to optimize images, please make sure that exact image sizes are used on your site. They should not be scaled (down) at all, if possible.

Alright. These are the tools for compressing or optimizing images.

### Command Line

- [PngCrush](http://pmt.sourceforge.net/pngcrush/)
- [PNGOUT](http://advsys.net/ken/utils.htm)
- [Gifsicle](http://www.lcdf.org/gifsicle/)
- [JpegOptim](http://freecode.com/projects/jpegoptim)
- ...[and many more!](https://www.google.com.sg/search?q=image+optimization+command+line&oq=image+optimization+command+line&ie=UTF-8)

### Desktop

- [ImageOptim](http://imageoptim.com/) for Mac
- [PNGGauntlet](http://pnggauntlet.com/) for Windows
- [Trimage](http://trimage.org/) for Linux

### Online

- [Smush.it](http://www.smushit.com/ysmush.it/)
- [TinyPNG](http://tinypng.org/)

## Optimize Javascript and CSS: Combine, Minify and GZip Them

This can seem tedious at the first glance. Thankfully, there are tools that can combine and minify assets (CSS, Javascript).

But wait! Why should we do this in the first place?

### Why Combine?

Say, you have the habit of separating Javascript files into different concerns such as model, views, controllers, helpers, widget X, widget Y, etc. Or, you have the habit of separating CSS files for the base layout, sections, widgets, etc. That is great. Keep it up!

Then, in your HTML views, you reference all those files one by one like this…

```
<link href="style1.css" />
<link href="style2.css" />
<link href="style3.css" />
<link href="style4.css" />
...
```

Imagine if the browser fetches all your Javascript and CSS files one by one...Wait...how many of those files do you have? 10? 20? Whoa, the browser's gonna create a lot of requests! **One browser can only do 4-6 requests concurrently**, so please make sure you make use of that limitation effectively.

It would be great if the browser only need to fetch once for a combined Javascript file, and another one time for a combined CSS file (instead of 10, 20, or a lot). This means, effort to combine those files is necessary. Combining files is as easy as copying and pasting everything to a brand new file. You get what I mean. And of course that's really tedious to do if you do it by hand for lots of files.

You can use `cat` or other command-line tools, but you might want a more elegant way of doing this than creating custom scripts.

Continue to the next section!

### Why Minify?

Now that you saved the number of requests on your site by combining assets, you can compress the content further. In your CSS files, do you see whitespaces? In your Javascript files, do you see whitespaces and long variable/method names? We all do. Getting rid of those unnecessary whitespaces and compressing long names (while maintaining the same code behavior) can reduce the overall file size. [That is what minification does, generally.](http://en.wikipedia.org/wiki/Minification_\(programming\))

Of course, you can remove whitespaces with your custom scripts, but compressing long names need some effort. [Google Closure Compiler](https://developers.google.com/closure/compiler/) and [YUI Compressor](http://yui.github.io/yuicompressor/) are some of the most popular tools to help you do minification. Give the CSS or Javascript file name you want to compress and voila, the corresponding compressed file will be created!

While you're pretty sure that minification can save lots of bytes, sometimes you aren't quite satisfied with the result. There are a few ways to optimize your code for minification, [as mentioned in this blog post](http://www.edave.net/2009/10/18/maximising-javascript-minimisation-and-readability/).

### Why GZip?

After minification, the content can be compressed further by GZipping the file. Some browsers and servers might not support this, but the amount of bytes transferred can be reduced significantly if GZip is supported by both parties.

You can GZip on the fly using your web server (Apache, [Nginx](http://wiki.nginx.org/HttpGzipModule), etc.), or just provide the gzipped version of your assets in your web application.

### The Tools!

Now comes the fun part. There are tools which can combine, minify and gzip your CSS and JavaScript assets.

**If you use Ruby:**

- [Jammit](http://documentcloud.github.io/jammit/)
- [Sprockets](https://github.com/sstephenson/sprockets)<br/>[Rails](http://rubyonrails.org/) or [Middleman](http://middlemanapp.com) already include this!

*Most of the tools above also come with CSS preprocessors (SASS, SCSS) and a JavaScript preprocessor (CoffeeScript) too!*

**If you use ASP.NET:**

- [Cassette](http://getcassette.net/)<br/>This comes with CSS and Javascript preprocessors!
- Asset bundle<br/>This is already provided by default.

## Cache Static Assets Using HTTP Cache Headers and/or CDN

You might think that the above optimizations are enough. You already cut the number of bytes transferred from your web server to the browser. You already minimize the number of requests from the browser to the web server. What else can you save?

**The distance of bytes transfers!**

The way a byte travel from your site to the browser is not like a straight line. It looks more like a graph a connections; there are intermediate servers between your web server and the browser.

Just by distributing your assets closer to your visitors, you can speed up your website response times…at least according to those visitors. This can be done using **Content Delivery Network**.

But that can be a little bit overkill for sites with small number of visitors…

Don't start distributing just yet. There is a trick you can use. Tell browsers to cache resources so that they don't do requests unless necessary! We can tell them just by setting HTTP Cache Headers via web applications or web servers.

Let's talk about the HTTP Cache Headers first.

### HTTP Cache Headers

The fastest way to make a request faster is not to do the request at all. Obviously.

As long as your web server (like Apache, Nginx, etc.) obeys HTTP server spec, you can apply HTTP Cache Headers for your assets and web pages. Those headers can tell the gateway or browser so that those files are cached until certain time or until the file content is changed. How?

There are two types of cache headers.

1. Expiration: Tells the browsers or gateway to expire cached resources at certain time. This uses Expires or Cache-control header. This could be more suitable for dynamic HTML pages.
1. Validation: Tells the browsers or gateway to get the new resources if Last-Modified header from the client side is stale, or ETag header value is different from the actual server. This is very suited for static assets (like static HTML, Javascript, CSS, and image files)

There are lots of more headers [in the spec](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html) or [some other docs](http://www.mnot.net/cache_docs/).

You can set these headers from your web server ([Apache](http://httpd.apache.org/docs/current/mod/mod_expires.html), [Nginx](http://wiki.nginx.org/HttpHeadersModule), etc.) or your web application ([Rails](http://guides.rubyonrails.org/caching_with_rails.html#conditional-get-support), [Rack](http://rtomayko.github.io/rack-cache/), etc.). Setting the headers is trivial effort, but it impacts greatly on performance.

If you don't want to do that setup for your static assets, use [GitHub Pages](http://pages.github.com/) to host your files. It already applies HTTP Cache Headers for each of the files.

### CDN

Alright. After you apply the proper cache headers, you think that things are going well. That is true, until you see visitors from the other side of the world complaining your site is slow. Or, you see for yourself in another country that the site is slow. For this case, you might want to cut the round trip time a little bit more by hosting those static assets (CSS, Javascript, images) on Content Delivery Network (CDN).

What is that CDN thing?

CDN is just like your server (for assets), with a lot more other servers are sitting in different countries and regions. So, they are actually a network of servers. Once you upload your files to the CDN, it will distribute them to the different locations. When a request to your file come from a browser, the CDN will respond from a server closest to where the request come from. This reduces the travelling time of the actual file bytes; instead of from your server in Tokyo all the way to Berlin, the file just travels within Berlin. Well, there are some small bytes from the browser, CDN server, and the actual CDN server in Berlin. Luckily, that's pretty short and fast trip!

There are [plenty of CDNs](https://en.wikipedia.org/wiki/Content_delivery_network#Notable_content_delivery_service_providers) like [Amazon CloudFront](http://aws.amazon.com/cloudfront/) and [CloudFlare](https://www.cloudflare.com/). They usually have certain pricing. They can be really big help for speeding up your site. You can also configure HTTP Cache Headers for each file on CDN. Feel free to refer to the specific CDNs documentations to learn more.

<hr/>

Congratulations! You have reached the end of article. If you try all the above tips, your site should respond faster and have a better Google PageSpeed score than before.
