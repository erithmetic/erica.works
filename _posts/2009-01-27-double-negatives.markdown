---
layout: post
title:  "Double Negatives"
date: 2009-01-27 12:00:00 -0600
categories: 
---

Recently I was working on a project adding new functionality that would queue a long-running task to a background job. The original code looked something like this:

<pre><code>receive_data
forward_data
</code></pre>


My task was to run <em>enqueue_data</em> instead of <em>forward_data</em> since the forwarding was taking too long. I wanted to retain the ability to run <em>forward_data</em> if a specific parameter was passed in.  Since I was creating a parameter that would override a default feature, my first intuition was to name this parameter &#8220;noenqueue&#8221;:

<pre><code>receive_data
if params['noenqueue']
  forward_data
else
  enqueue_data
end
</code></pre>


I then smacked myself in the forehead when I realized I had &#8220;pulled a Microsoft.&#8221;  I had created a setting that is named such that setting it to true would disable a feature, while setting it to false would enable the feature.  You see this all the time in Microsoft products.

<img src="http://img.skitch.com/20090128-1xk7jkp9sw8fu1jnkgtjet1mja.jpg" alt="" />

It irritates me because you have to think in double-negative terms when setting the parameter.  Instead, it is much more intuitive to create settings that are positive - that setting them to true will enable something.

Lesson learned: I renamed the parameter to &#8220;forward_immediately&#8221;:

<pre><code>receive_data
if params['forward_immediately']
  forward_data
else
  enqueue_data
end
</code></pre>


One good syntactic feature of Ruby is the <em>unless</em> keyword. I prefer to use &#8220;unless variable.nil?&#8221; instead of &#8220;if variable&#8221; if I want to only operate on non-nil objects.  I find it a bit more intuitive.

<pre><code>unless document.nil?
  document.write
end</code></pre>


However, you can still get into trouble when you need to branch:

<pre><code>unless document.nil?
  document.write
else
  document.new(str).write
end</pre>


</code>

An <em>else</em> branch on an <em>unless</em> is, in my opinion, the least readable or intuitive way to write branches.
