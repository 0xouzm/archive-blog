---
title: "Share your website to twitter or facebook"
date: 2019-01-21 19:04
categories: web
tags: twitter share
---





sample code:

#### header

```html
    <meta name="twitter:title" content="{{ news.title }}"/>
    <meta name="twitter:description" content="{{ news.summary|default_if_none:"" }}"/>
    <meta name="twitter:image" content="https://cpchain.io{{ MEDIA_URL }}{{ news.banner }}"/>
#   <meta name="twitter:site" content="@cpchain.io">
#   <meta name="twitter:creator" content="@CPChain">
    <meta name="twitter:card" content="summary_large_image"/>
```

#### html

> facebook

```HTML
<a href="javascript:window.open('http://www.facebook.com/sharer.php?u='+encodeURIComponent(document.location.href)+'&t='+encodeURIComponent(document.title),'_blank','toolbar=yes, location=yes, directories=no, status=no, menubar=yes, scrollbars=yes, resizable=no, copyhistory=yes, width=600, height=450,top=100,left=350');void(0)"><i class="fa fa-facebook" aria-hidden="true"></i> Share </a>
```

> twitter

```html
<a href="javascript:window.open('http://twitter.com/home?status='+encodeURIComponent(document.title)+''+encodeURIComponent(document.location.href),'_blank','toolbar=yes, location=yes, directories=no, status=no, menubar=yes, scrollbars=yes, resizable=no, copyhistory=yes, width=600, height=450,top=100,left=350');void(0)"</a>
```



Twitter CARD validator：<https://cards-dev.twitter.com/validator>