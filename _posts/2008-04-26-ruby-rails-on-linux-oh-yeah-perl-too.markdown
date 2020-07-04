---
layout: post
title:  "Ruby/Rails on Linux (oh yeah, Perl too)"
date: 2008-04-26 12:00:00 -0500
categories: 
---

So I&#8217;ve spent the last four months developing Rails apps, Ruby scripts, and Perl scripts on a Fedora desktop.  I cut my teeth with Ruby/Rails on my Mac at home, so I was in on the TextMate and Growl goodness.  It&#8217;s comical to see that 90% of Rails developers are on the Mac, and one would assume that it&#8217;s because if one must use a trendy framework, they must also choose a trendy development platform.  The truth is, I just haven&#8217;t found sufficient analogues to Mac tools in the Linux environment.

The first editor I experienced (and the one I keep coming back to) is <a href="http://www.aptana.com/rails/">Eclipse/Aptana/RadRails</a>.  It is a beast of an IDE, taking several seconds to load on a capable dual core AMD 64.  Here&#8217;s some other issues I run into on a daily basis with the 1.0 release (which, to me, would imply feature complete and stable):


* RadRails &#8220;forgets&#8221; about syntax highlighting and makes everything purple.  Sometimes, typing space at the end of a line and saving will fix it, but it&#8217;s a crap shoot.
* Eclipse will occasionally slow down to a crawl for about a minute.  The GNOME task monitor says the CPU is idle, but Eclipse would make you think it was sharing CPU time with a nuclear explosion simulator.
* The &#8220;go to resource&#8221; dialog doesn&#8217;t quite load results for every file that matches your criteria.  It also takes an extra arrow key press to get through the list.
* If left open for too long, the inevitable, &#8220;There was an error, would you like to exit the workbench?&#8221; dialog appears.  You can say &#8220;no&#8221; but then you&#8217;re just living on borrowed time.  Eventually, it will all come crashing down.
* Of course, there are not nearly as many cool shortcuts and scriptability that TextMate offers.

After getting frustrated with RadRails, I turned to gedit, GNOME&#8217;s text editor.  Apparently, there are <a href="http://grigio.org/textmate_gedit_few_steps">several plugins</a> you can get to make it act almost like TextMate.  It&#8217;s also very lean and responsive.  First of all, the gedit website was down when I tried to get the plugins.  Then, it became a confusing ordeal to get the plugins installed correctly.  Gedit also liked to keep backups of each file sitting right next to the real files.  This made SVN a bit messy when I would just run an svn commit on everything.  Furthermore, the plugins, like the quick file open, didn&#8217;t even work 100%.  After some gedit crashes, I decided to ditch it and look further.

After trying the two major open source editors, I looked into ActiveState&#8217;s <a href="http://www.activestate.com/Products/komodo_ide/index.mhtml">Komodo IDE</a>.  It&#8217;s a paid app running at $300.  This IDE was the most consistent, stable, and feature-rich app out there.  However, it had major performance issues.  Simply scrolling through the list of files in a project slowed the app to a crawl.  I installed the version compiled with gcc 5, which offered a slight speed improvement, but still wasn&#8217;t snappy enough to facilitate rapid development.  It wasn&#8217;t worth $300.

So now I&#8217;m back to Eclipse.  Yes, I know there&#8217;s always vi and emacs, but I&#8217;m feeling too lazy right now to learn all the keystrokes required for things as simple as highlighting text and switching files.  I&#8217;d rather just use the mouse that God gave me.

As far as a Linux analogue of Growl, there is Mumbles.  At this point, it seems like too much of a hassle to find all of the plugins to make Mumbles work with even simple services/apps.  I love how many apps now (like Firefox 3) have Growl support built right In.

Which brings me to my point.  The Mac is sort of like a &#8220;GUI on Rails.&#8221;  Its API sets stricter conventions on how an app should work, but with those limits comes the benefits of simply getting things done and the ability to focus on the grander issues - like interoperability and bleeding edge features.  In the Linux world there is an amazing freedom of choice, but with it comes chaos and a lot more work that goes toward just getting things to talk to one another.  While the freedom is nice, sometimes it&#8217;s nicer to work within stricter guidelines and get something done quickly.
