---
layout: post
title:  "Ruby Development on Linux Part 3"
date: 2009-07-11 12:00:00 -0500
categories: 
---

I&#8217;ve <a href="http://plusrw.wordpress.com/2008/05/01/linux-editor-update/">posted</a> earlier about my attempts to find the perfect development environment on Linux, an environment that would match the elegance and ease of TextMate on OS X.  I&#8217;ve always been a fan of the freedom that you get with Linux and I&#8217;ve been willing to sacrifice prettiness as long as the environment is intuitive and easy to use.

So far, my journey has been wrought with failures, but there have been a few bright spots.

By far, my favorite platform is <a href="http://www.ubuntu.com/">Ubuntu</a>.  They&#8217;ve really nailed the desktop usability aspect as it trumped CentOS and Fedora in being able to recognize my video card and use dual monitors.  However, Ubuntu still suffers from some quirks.  With full visual effects enabled, I can only open so many instances of Firefox without strange things like page content not scrolling.  When I hit a certain number of windows open over several desktops, Gnome freaks out and merges them all into a single workspace, disabling full visual effects.

I have tried many text editors and IDEs but all come up short in some way.

I can&#8217;t stand vi&#8217;s separate edit and navigation modes.

<a href="http://www.xemacs.org/">XEmacs</a> is quick and powerful, but there are too many quirks that keep me from loving it.  The speedbar widget exists as a separate window, which makes task switching more of a pain.  Simply finding settings and saving them across sessions is cryptic and difficult.  Installing a language extension requires some elisp programming.  I just want to click!  Copy and paste are broken.  If I copy in emacs, I can&#8217;t paste into another app, and vice-versa.  The only way to do it is to use the edit drop-down menu in each app.  Aside from that, the actual act of text editing in emacs is a breeze.  However, I still miss the Textmate macros that generate things like RSpec describe and it blocks.  One redeeming quality of xemacs is that I can have multiple windows open.

<a href="http://www.netbeans.org/">Netbeans</a> works, but is a bit slow.  I also hate having to create an nbproject folder to keep project state.  The same goes with Eclipse, Aptana, JetBrains, and all the rest.  IDEs are too cumbersome, especially when most of them force you into a MDI interface.  I really like having many different windows open when editing a project.  Tabs just don&#8217;t do it for me.  In fact, that&#8217;s one area where Textmate disappoints me.

I had tried to make <a href="http://projects.gnome.org/gedit/">gedit</a> act like Textmate, but it would crash a lot.

Lately, I&#8217;ve had the best results with <a href="http://www.activestate.com/komodo_edit/">Komodo Edit</a>.  I&#8217;ve been able to customize the keyboard shortcuts so that alt acts just like the command button in Textmate/OS X.  It&#8217;s very helpful when I have to switch off our Mac onto the Linux box.  I&#8217;ve also set up a &#8220;cobalt&#8221; theme.  Working in split-screen mode with the whole project window spanned across monitors works well enough for my desire for multiple windows.  I can have the code on one side with the spec on the other.  Komodo also features the quick file open command and can show you where a method is defined.  The drop-down autocomplete is also nice.  There is no integration with rspec, et al, but I&#8217;m fine using the terminal.  In fact one annoyance I have with Textmate is that any output to STDOUT in a spec is assumed to be HTML in Textmate&#8217;s output screen.  This means I have to use HTMLEntities.new.encode when inspecting an object.  With the terminal, text is text!  Komodo also requires project files to be maintained in the root directory of your project, but I guess I can live with that.  One final complaint: Komodo tends to crash if I have too many separate windows open.  I guess I just can&#8217;t have 6 projects open at a time.

<a href="http://redcareditor.com/">Redcar</a> is a very promising project.  It&#8217;s designed to act just like Textmate and will support TM bundles.  It&#8217;s also written in Ruby!  The only thing that has kept me from using it is that the gtk-ruby libraries that it uses have C extensions that are written specifically for Ruby 1.8.  I&#8217;ve spent a good chunk of time porting them to 1.9 compatibility, but it&#8217;s such a large task I don&#8217;t have time to port it all.  I do have Ruby 1.8 installed in parallel, but trying to get everything to run proves difficult when different components of Redcar try to run /usr/local/bin/ruby (which points to 1.9 on my system).

Over all, Komodo on Ubuntu has made life manageable, but not quite the experience I get with Textmate.  I&#8217;m sure once Redcar is 1.9 compatible I&#8217;ll be switching.
