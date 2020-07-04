---
layout: post
title:  "Technical Difficulties - Movable Type 3.2 and SELinux"
date: 2005-09-21 12:00:00 -0500
categories: 
---

Ever since I set up Movable Type on my Fedora Core 4 server, I&#8217;ve been having issues with MT not being able to perform basic tasks, like trackback pings, Typekey authentication, Technorati updates, and some plugins that fetch data over HTTP (such as RPC calls and RSS).

I had an inkling that SELinux was to blame, because it puts some pretty strict restrictions on CGI scripts and the like.  To prove it, I put SELinux in permissive mode - and lo and behold!  I now get MT news on my admin page and Typekey authentication works.  There are some issues with some plugins I have, but they are due to coding errors.

Most sites recommend disabling SELinux completely to get around problems that people are having with PHP, Apache, CGIs, etc.  For me, that seems like the worst way to fix a problem.  SELinux serves a purpose - it keeps hackers/viruses from wiping out your entire server using a simple Apache/CGI exploit.  That&#8217;s powerful stuff!  Through my Google searches for technical documentation, I&#8217;ve seen plenty of Movable Type blogs that have been compromised and wiped out.  SELinux is beneficial!

So, my goal is to hammer out all of the SELinux incompatibilities with Movable
Type.  It should just be a matter of assigning the correct classes to each
script.  *Hopefully!!*
