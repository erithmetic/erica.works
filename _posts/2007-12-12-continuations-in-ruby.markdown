---
layout: post
title:  "Continuations in Ruby"
date: 2007-12-12 12:00:00 -0600
categories: 
---

A week ago, Dr. Frens spoke about continuations in Ruby at the <a href="http://gr-ruby.org/">Michigan Ruby Users Group</a>.  It was very interesting as it brought back my college days when I learned Scheme and functional programming concepts.  I always enjoyed functional programming because it presents such a different way of thinking through problems.

I was going to blog about his presentation, but it turns out he <a href="http://jdfrens.blogspot.com/2007/12/continuation-passing-style-in-ruby.html">blogged all his own notes already</a>.  But what I found interesting is the sort of hybrid nature that Ruby has.  It is both an object-oriented language and it also supports some functional programming concepts.  The drawback Dr. Frens found was that Ruby doesn&#8217;t do tail recursion optimization, so it&#8217;s not really worth using continuation passing style functions.  Interesting, nonetheless!

