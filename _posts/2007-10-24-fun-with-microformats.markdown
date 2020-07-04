---
layout: post
title:  "Fun with Microformats"
date: 2007-10-24 12:00:00 -0500
categories: 
---

I just updated my <a href="http://eatlansing.wordpress.com">food blog</a> by encoding all of the restaurants&#8217; contact information in <a href="http://microformats.org">microformats</a>.  It&#8217;s basically a way to encode vCard/vCal data using HTML.  It&#8217;s part of the latest movement to bring <a href="http://en.wikipedia.org/wiki/Semantic_web">semantics</a> (meaningful data that can be interpreted by computer programs) to web content. For instance, with this technology you could easily write a program to find all the phone numbers and addresses listed on the internet and generate a worldwide phone book. Or you could track who has lived in a given house over the past X years. Or you could generate statistics on how many CEOs live next to golf courses. Or you could figure out, according to published calendars, when most people will be away from home and thus not watching the latest sitcom. And that&#8217;s just the beginning.

One thing that&#8217;s cool about the hCard specification is its flexibility. According to the spec, they recommend the following format:

<pre>&lt;div class="vcard"&gt;
  &lt;span class="fn n"&gt;
     &lt;a class="url" href="http://t37.net"&gt;
       &lt;span class="given-name"&gt;Fréderic&lt;/span&gt;
       &lt;span class="family-name"&gt;de Villamil&lt;/span&gt;
     &lt;/a&gt;
  &lt;/span&gt;
  &lt;span class="nickname"&gt;neuro&lt;/span&gt;
  &lt;a class="email" href="mailto:neuroNOSPAM@t37.net"&gt;
     &lt;span class="type"&gt;pref&lt;/span&gt;&lt;span&gt;erred email&lt;/span&gt;
  &lt;/a&gt;
  &lt;span class="org"&gt;Omatis&lt;/span&gt;
  &lt;span class="adr"&gt;
     &lt;abbr class="type" title="dom"&gt;France&lt;/abbr&gt;
     &lt;span class="type"&gt;home&lt;/span&gt; address
     &lt;abbr class="type" title="postal"&gt;mail&lt;/abbr&gt; and
     &lt;abbr class="type" title="parcel"&gt;shipments&lt;/abbr&gt;:
     &lt;span class="street-address"&gt;12 rue Danton&lt;/span&gt;
     &lt;span class="locality"&gt;Le Kremlin-Bicetre&lt;/span&gt;
     &lt;span class="postal-code"&gt;94270&lt;/span&gt;
     &lt;span class="country-name"&gt;France&lt;/span&gt;
  &lt;/span&gt;
&lt;/div&gt;</pre>

But I was able to easily use different elements in place of &lt;div&gt; and &lt;span&gt;:

<pre>&lt;p class="vcard"&gt;
  &lt;span class="fn n"&gt;
     &lt;a class="url" href="http://t37.net"&gt;
       &lt;span class="given-name"&gt;Fréderic&lt;/span&gt;
       &lt;span class="family-name"&gt;de Villamil&lt;/span&gt;
     &lt;/a&gt;
  &lt;/span&gt;
  &lt;span class="nickname"&gt;neuro&lt;/span&gt;
  &lt;a class="email" href="mailto:neuroNOSPAM@t37.net"&gt;
     &lt;span class="type"&gt;pref&lt;/span&gt;&lt;span&gt;erred email&lt;/span&gt;
  &lt;/a&gt;
  &lt;span class="org"&gt;Omatis&lt;/span&gt;
  &lt;a class="adr" href="http://somegooglemapslink"&gt;
     &lt;abbr class="type" title="dom"&gt;France&lt;/abbr&gt;
     &lt;span class="type"&gt;home&lt;/span&gt; address
     &lt;abbr class="type" title="postal"&gt;mail&lt;/abbr&gt; and
     &lt;abbr class="type" title="parcel"&gt;shipments&lt;/abbr&gt;:
     &lt;span class="street-address"&gt;12 rue Danton&lt;/span&gt;
     &lt;span class="locality"&gt;Le Kremlin-Bicetre&lt;/span&gt;
     &lt;span class="postal-code"&gt;94270&lt;/span&gt;
     &lt;span class="country-name"&gt;France&lt;/span&gt;
  &lt;/a&gt;
&lt;/p&gt;</pre>

This was useful because wordpress does not allow &lt;div&gt; in your blog posts.  It automatically converts them to &lt;p&gt; :p
