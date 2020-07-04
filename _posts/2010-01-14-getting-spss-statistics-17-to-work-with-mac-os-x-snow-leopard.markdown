---
layout: post
title:  "Getting SPSS Statistics 17 to Work With Mac OS X Snow Leopard"
date: 2010-01-14 12:00:00 -0600
categories: 
---

My wife is a grad student who does quantitative studies from time to time. Her advisor recommended that she use <a href="http://www.spss.com/">SPSS</a> to help her generate statistics from her research data. Of course, SPSS is a gigantic, bloated Java app developed by IBM. Being an IBM product targeted toward large institutions, it also costs an arm and a leg.  I really wish <a href="http://www.r-project.org/">R</a> was more well known and widely used because it is free software.

My wife recently found that after I upgraded her laptop to Snow Leopard, it rendered SPSS unusable. Mac OS reported that the application is incompatible with Snow Leopard. For a brief time, SPSS offered a free patch that allows you to use SPSS with Snow Leopard, but it expired December 31st, 2009 and you are now told to buy an upgrade for $100.  A poster mentioned in a help forum that the only reason SPSS 17 didn&#8217;t work with Snow Leopard was that SPSS 17 was designed for Java 1.5 and Snow Leopard only comes with Java 1.6 installed.  My blood boiled as I realized that SPSS was attempting to charge me $100 for not having an older version of Java installed. I was also suspicious of the fact that Mac OS warned of an incompatibility before the application even attempted to launch. How was it so sure?

I found some <a href="http://rhizomicomm.blogspot.com/2009/09/tips-tricks-fixing-spss-mac-v17-in-snow.html">tutorials</a> for getting SPSS 17 to work with Snow Leopard, but they involved magic and cargo culting.  Therefore, I give you the simplest instructions for installing it:

<ol>
    <li><a href="http://redirectingat.com/?id=292X457&amp;url=http%3A%2F%2Fwww.cs.washington.edu%2Fhomes%2Fisdal%2Fsnow_leopard_workaround%2Fjava.1.5.0-leopard.tar.gz">Download a Java 1.5 package</a> that was designed for Leopard, but will work with Snow Leopard, too.</li>
    <li>Extract the package by double-clicking the downloaded file.  A &#8220;1.5.0&#8221; folder will appear.</li>
    <li>Drag the 1.5.0 folder into Macintosh HD/Library/System/Frameworks/JavaVM.framework/versions. Say yes you want to overwrite and authenticate with your user name and password.</li>
    <li>In spotlight (click the magnifying glass in the top right) type &#8220;Java Preferences&#8221; and click the top hit.</li>
    <li>In the bottom-most box, drag Java 1.5 32-bit to the top of the box, ahead of Java 1.6.  This tells OS X to try and use Java 1.5 to open your apps before it tries the newer 1.6 version.</li>
    <li>Download and install the <a href="http://support.spss.com/ProductsExt/Statistics/Patches/17.0.2/Client/Mac/17.0.2_Readme.html">SPSS 17.0.2 patch</a></li>
    <li>This is the ridiculous part. Snow Leopard ships with a <a href="http://support.apple.com/kb/HT3258">black list of applications that it &#8220;knows&#8221; are incompatible</a>. Apple is trying to protect us, here. The only way it knows you&#8217;re trying to run an incompatible application is that it checks the name of the application you try to run against its blacklist. Simply rename your SPSS program by going to Applications/SPSSInc/Statistics17, highlight SPSSStatistics17.0, press return, then rename it to something like SPSS. Now Mac OS will not recognize that you&#8217;re running SPSS 17.</li>
</ol>


Happy statisticing, or whatever it is you do&#8230;
