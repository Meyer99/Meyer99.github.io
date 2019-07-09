---
title: A Brief Guide on Setting up Blog Using Jekyll
subtitle: The overall process of setting up my blog
layout: post
mathjax: false
author: Meyer
catalog: true
header-img: "img/polygons-1920x1080-network-blue-hd-11843.jpg"
tags: 
    - Blog
---

After several-day efforts, I finally set up my blog successfully!🌟 Although the overall process is not complicated, it is not simple yet. Therefore, I will record the whole process of setting up my blog in my first post. Many other tutorials have explained some common issues in detail, so I won't repeat those but focus on my own problems during the construction process. I will include the links to those easy-to-follow tutorials, which helped me a lot in setting up my blog.

![](/img/in-post/20190709-2.png)

## Preparation
* **Learn about [Github Pages](https://pages.github.com/) and [Jekyll](https://jekyllrb.com/)**  
  [Videos](https://www.youtube.com/channel/UCbOO7d0vVo0kIrkd7m32irg) on YouTube may give you an intuitive understanding on how to use GitHub and create a GitHub page, and use Jekyll to generate a static website. Watch a couple of these to gain some basic knowledge of using GitHub Pages and Jekyll.
* **Install Jekyll**  
  Installing Jekyll on your computer enables you to preview your website before pushing it to GitHub. Follow the [guide](https://jekyllrb.com/docs/installation/) to install it. If you are using Windows, I strongly recommend you to install the "Windows subsystem for Linux" and install Jekyll using it. 
* **Select a Jekyll theme**  
  There are many amazing themes available on GitHub, which is developed carefully for different purposes of use. Search "Jekyll theme" on GitHub and choose your favourite.  
![](/img/in-post/20190709-1.png)

## Quick Start
Once you choose your favourite theme, you may follow the setup guide provided by the theme developer. The file structures will vary slightly from themes to themes. You may get more familiar with that later and know how to customize your blog by editing the corresponding file.  
For now, clone or fork the theme, and usually just by modifying the `_config.yml` file, in which you change the original properties to your personal ones, complete most of the work!  
<br>
I cloned the [Hux Blog](https://github.com/huxpro/huxpro.github.io/), and followed the guide in README to get started. A more detailed setup guide(Chinese) is available [here](https://www.jianshu.com/p/e68fba58f75c).

## Modification
After editing the `_config.yml` file, you can either run `jekyll serve` or commit to your GitHub repository to view your website. If you are satisfied with the current result, then you may begin to write posts using Markdown blissfully.   
For me, after cloning the theme, I found a minor bug and made some modifications to meet my requirements:  
* Change some expressions
* Change the background pictures
* Remove the Portfolio module
* Add a [search module](https://github.com/androiddevelop/jekyll-search)
* Fix a minor bug that leads to undesired link
* Enable my website to be indexed by Google

You may need to understand the file structure, know the function of each file, and have basic knowledge of programming to make these modifications. Read the [Jekyll documentation](https://jekyllrb.com/docs/) for more information and try to customize it in your way.  
![](/img/in-post/20190709-3.png)  
To change the expressions, you need to locate where they are defined. To change the background pictures is much easier just by simply uploading your favoured picture and modifying the path. If you want your website to be searched by others on Google, Google it to know how to enable that!

## Summary
If you don't pay much attention to some details, then congrats and you successfully build your blog easily by following the instructions provided along with the theme. But if you want to customize it and make further modifications according to your requirements, then you have to make an effort to figure out how to achieve that if you are not familiar with Web development.  
<br>
*P.S.* I had stuck with modifying some details and struggled to change them accordingly for several hours since I have little experience in Web development.😂 And at the end knowing how the files are organized made the modifications more clear and easy to implement. Good luck!  
<br>
*P.P.S.* Please forgive my poor English writing skills. I had tried my best to express in a precise way. If you find any grammar mistakes or unclear meaning, do let me know!🙋 Your suggestion is welcomed!