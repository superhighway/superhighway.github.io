---
title: Middleman or Jekyll?
date: 2013-03-05
tags: comparison, web, open source
---

I have used [Jekyll](https://github.com/mojombo/jekyll) with [Octopress](https://github.com/imathis/octopress) before. When I saw [Middleman](http://middlemanapp.com/), I immediately fell in love and left Jekyll.

[Jekyll](https://github.com/mojombo/jekyll) has awesomely organized folder stucture and has lots of plugins. You can host Jekyll site as it is in GitHub, without any deploy script. It has a few themes you can pick, especially if you use Octopress. However, I like Jekyll better when the documentation has short getting started guide. It took me a while just to make things work initially. I have ever stuck just because I didn't install Python source code in my computer (I needed syntax highlighting). Also, when I first started using Jekyll, I had to read a lot of information from the documentation or Googling just because of a random problem (either related to syntax highlighting or Liquid).

[Middleman](http://middlemanapp.com/) is amazingly lightweight and front-end friendly. The initially generated template is so simple that it works immediately, and anyone can customize it quickly. It comes with [asset pipeline](http://middlemanapp.com/asset-pipeline/) by default, so it is just a few configs and a command line away to [minify assets, compress image](http://middlemanapp.com/advanced/file-size-optimization/), and even [minify HTML](http://middlemanapp.com/minify-html/). The documentation is pretty short as well. Unfortunately, Middleman doesn't have as many plugins as Jekyll. There is no deploy script available by default, and GitHub does not support hosting site organized with Middleman as it is. So, if you want to deploy Middleman-generated site, you need [middleman-deploy](https://github.com/tvaughan/middleman-deploy). Also, unfortunately, Middleman's folder structure can be more disorganized than Jekyll.

Both [Jekyll](https://github.com/mojombo/jekyll) and [Middleman](http://middlemanapp.com/) are really really cool. You should try both and pick what you like.
