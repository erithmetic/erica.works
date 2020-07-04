---
layout: post
title:  "Some Very Strange Markup"
date: 2007-11-07 12:00:00 -0600
categories: 
---

I was viewing the source of the Facebook iPhone webapp to see how they did their fancy transitions between pages, when I found some very strange HTML:

```
<body home="home.php">
<div class="toolbar1 tops chrome">
<u href="home.php"><img src="images/facebook.png"/></u>
<u class="button plain leftButton" href="#status">Status</u>
<u class="button plain" href="#search" radio="true">Search</u>
</div>
```

How can a &lt;body&gt; tag have a &#8220;home&#8221; attribute or a &lt;u&gt; tag have an &#8220;href&#8221; attribute? I scoured the W3C spec and looked for a possible mobile HTML standard supporting it, but couldn&#8217;t find anything.Well, it turns out they&#8217;re doing some non-standard trickery by adding custom attributes to their elements. They&#8217;re then using JavaScript to get the values of those attributes and use them in their &#8220;onclick&#8221; handlers for each element. Sneaky!
