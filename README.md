#Nova Blog

###[View Nova Blog &rarr;](https://zlxy9892.github.io)




#### 1 Get Started

You can easily get started by modifying `_config.yml`:

```
# Site settings
title: Nova Blog             # title of your website
SEOTitle: Nova Blog			 # check out docs for more detail
description: "Welcome to Nova Blog"    # ...

# SNS settings      
github_username: zlxy9892     # modify this account to yours
weibo_username: jerrywinning  # the footer woule be auto-updated.

# Build settings
# paginate: 10              # nums of posts in one page
```


#### 2 Write Posts

Feel free to checkout Markdown files in the `_posts/`, you will quickly realized how to post your articles with magical markdown plus this nice theme.

The **front-matter** of a post looks like that:

```
---
layout:     post
title:      "Hello 2017"
subtitle:   "Hello World, Hello Blog"
date:       2017-08-08 08:08:08
author:     "Nova"
header-img: "img/post-bg-1.jpg"
tags:
    - Life
---
```

#### 3 SideBar

![](/img/NovaBlog-Screenshot.jpg)

Seeing more information may be necessary for you to display, from V1.1, a clean, gorgeous **SideBar** is added for you, which provide more area for displaying possible modules. You can enable *(it is default enable)* this feature by simply config:

```
# Sidebar settings
sidebar: true
sidebar-about-description: "your description here"
sidebar-avatar: /img/head/head1.jpg     # use absolute URL.
```

#### 4 Featured Tags

Considering the Featured-Tags feature in [Medium](http://medium.com) is pretty cool, so I add it in my blog theme also.   
This module is independent of sidebar from V1.4, so it can definitely live without enable sidebar, which would be displayed in the bottom when `sidebar` set to false, and it is not only displayed in home page but also every post page bottom.


```
# Featured Tags
featured-tags: true  
featured-condition-size: 1     # A tag will be featured if the size of it is more than this condition value
```

The only one thing need to be paid attention to is the `featured-condition-size`: A tag will be featured if the size of it is more than this condition value.  
Internally, a condition template `{% if tag[1].size > {{site.featured-condition-size}} %}` is used to do the filter.

#### 5 Friends

Friends is a very common feature of a blog seeing the SEO, so I add it in V1.1 release to help that.   
Friends can also live without enable sidebar, also be displayed in the bottom when sidebar unable, and be displayed in every post page bottom.


You can just add your friends information in `_config.yml` with a familiar JSON syntax and everything is done, very easy:

```
# Friends
friends: [
    {
        title: "Google",
        href: "http://www.google.com/"
    },
    {
        title: "Baidu",
        href: "http://www.baidu.com/"
    }
]
```


#### 6 Comment

This theme support both [Disqus](http://disqus.com) and [Duoshuo](http://duoshuo.com) as the third party discussion system.

First, you need to sign up and get your own account. (I have set Trusted Domains) It is deathly simple to sign up and you will get the full power of management system. Please give it a try!

Second, from V1.5, you can easily complete your comment configuration by just adding your **short name** into `_config.yml`:

```
duoshuo_username: _your_duoshuo_short_name_
# OR
disqus_username: _your_disqus_short_name_
```

**To the old version user**, it's better that you pull the new version, otherwise you have to replace code in `post.html`, `keynote.html` and `about.html` by yourselves.

Furthermore, Duoshuo support Sharing. if you only wanna use Duoshuo comment without sharing, you can set `duoshuo_share: false`. You can use Duoshuo Sharing and Disqus Comments together also.

#### 7 Analytics

We support Google Analytics and Baidu Tongji officially with a deathly simple config:

```
# Baidu Analytics
ba_track_id: fceba9290cfb6b04ecbea282358d8bd6

# Google Analytics
ga_track_id: '[]'            
ga_domain: []
```

Just checkout the code offered by Google/Baidu, and copy paste here, all the rest is already done for you.

(Google might ask for meta tag "google-site-verification")


#### 8 Customization

If you wanna do more customization and change code yourself, a [Grunt](gruntjs.com) environment is also included. (Thanks to Clean Blog.)

There are a number of tasks it performs like minification of the JavaScript, compiling of the LESS files, adding banners to keep the Apache 2.0 license intact, and watching for changes. Run the grunt default task by entering `grunt ` into your command line which will build the files. You can use `grunt watch` if you are working on the JavaScript or the LESS.

**Try to understand code in `_include/` and `_layouts/`, then you can modify Jekyll [Liquid](https://github.com/Shopify/liquid/wiki) template directly to do more creative customization.**



## License

Apache License 2.0.
Copyright (c) 2017-2018 Nova

NovaBlog is derived from [Clean Blog Jekyll Theme (MIT License)](https://github.com/BlackrockDigital/startbootstrap-clean-blog-jekyll/)
Copyright (c) 2013-2016 Blackrock Digital LLC.
