---
layout: post
title:  "Graphite - Beyond the Basics"
date: 2012-08-27 12:00:00 -0500
categories: 
---

The <a href="http://graphite.wikidot.com/">Graphite</a> and <a href="http://github.com/etsy/statsd">statsd</a> systems have been <a href="http://codeascraft.etsy.com/2011/02/15/measure-anything-measure-everything/">popular</a> choices lately for recording system statistics, but there isn&#8217;t much written beyond how to get the basic system set up. Here are a few tips that will make your life easier.

<!-- more start -->


<h3>Graphite + statsd - the rundown</h3>

The graphite and statsd <a href="http://www.aosabook.org/en/graphite.html">system</a> consists of three main applications

<ul>
<li>carbon: a service that receives and stores statistics</li>
<li>statsd: a node server that provides an easier and more performant, UDP-based protocol for receiving stats which are passed off to carbon</li>
<li>graphite: a web app that creates graphs out of the statistics recorded by carbon</li>
</ul>


<h3>Use graphiti</h3>

<a href="http://graphite.readthedocs.org/en/latest/tools.html">Several alternative front-ends</a> to graphite have been written. I chose to use graphiti because it had the most customizable graphs. Note that graphiti is just a facade on top of graphite - you still need the graphite web app running for it to work. Graphiti makes it easy to quickly create graphs. I&#8217;ll cover this later.

The flow looks like:

<pre><code>|App| ==[UDP]==&gt; |statsd| ==&gt; |carbon| ==&gt; |.wsp file|

|.wsp file| ==&gt; |graphite| ==&gt; |graphiti| ==&gt; |pretty graphs on your dashboard|
</code></pre>

<h3>Use chef-solo to install it</h3>

If you&#8217;re familiar with <a href="http://wiki.opscode.com/display/chef/Home">chef</a>, you can use the cookboos that the community has already developed for installing graphite and friends. If not, this would be a good opportunity to learn. You can use chef-solo to easily deploy graphite to a single server. I plan to write a &#8220;getting started with chef-solo&#8221; post soon, so stay tuned!

Chef saved me a ton of time setting up python, virtualenv, graphite, carbon, whisper, statsd, and many other tools since there are no OS-specific packages for some of these.

<h3>Use sensible storage schemas</h3>

The default chef setup of graphite stores all stats with the following storage schema rule:

<pre><code>[catchall]
priority = 0
pattern = ^.*
retentions = 60:100800,900:63000
</code></pre>

The retentions setting is the most important. It&#8217;s a comma-delimited list of data resolutions and amounts.
* The number before the colon is the size of the bucket that holds data in seconds. A value of 60 means that 60 seconds worth of data is grouped together in the bucket. A larger number means the data is less granular, but more space efficient.
* The number after the colon is the number of data buckets to store at that granularity. 100800 will cover (100800 * 60) = 70 days of data. That&#8217;s (100800 * 12) = 1.2MiB of space for those 70 days. A bigger number means more disk space and longer seek times.

Alternatively, you can specify retentions using time format shortcuts. For example, 1m:7d means &#8220;store 7 days worth of 1-minute granular data.&#8221;

<h3>Use a good stats client</h3>

In the ruby world, there are two popular client libraries: <a href="http://rubygems.org/gems/fozzie">fozzie</a> and <a href="http://rubygems.org/gems/statsd-ruby">statsd-ruby</a>. Both provide the standard operations like counting events, timing, and gauging values.

Fozzie differs in that it integrates with Rails or rack apps by adding a rack middleware that automatically tracks timing statistics for every path in your web app. This can save time, but it also has the downside of sending too much noise to your statsd server and can cause excessive disk space consumption unless you implement tight storage schema rules. It also adds a deep hierarchy of namespaces based on the client machine name, app name, and current environment. This can be an issue on heroku web apps where the machine name changes frequently.

If you want more control over your namespacing, statsd-ruby is the way to go. Otherwise, fozzie may be worth using for its added conveniences.

<h3>Make sure you don&#8217;t run out of disk space</h3>

Seriously, if you do run out of disk, the graphite (whisper) data files can become corrupted and force you to delete them and start over. I learned this the hard way :) Make sure your storage schemas are strict enough because each separate stat requires its own file that can be several megabytes in size.

<h3>Use graphiti for building graphs and dashboards</h3>

Graphiti has a great interface for building graphs. You can even fork it and deploy your own custom version that fits your company&#8217;s needs and/or style. It&#8217;s a small rack app that uses redis to store graph and dashboard settings. There&#8217;s even a chef cookbook for it!

When setting up graphiti, remember to set up a cron job to run <code>rake graphiti:metrics</code> periodically so that you can search for metric namespaces from graphiti.

<h3>Use graphite&#8217;s built-in functions for summarizing and calculating data</h3>

Graphite provides a wealth of <a href="http://graphite.readthedocs.org/en/1.0/functions.html">functions</a> that run aggregate operations on data before it is graphed.

For example, let&#8217;s say we&#8217;re tracking hit counts on our app&#8217;s home page. We&#8217;re using several web servers for load balancing and our stats data is namespaced by server under <code>stats.my_app.server-a.production.home-page.hits</code> and <code>stats.my_app.server-b.production.home-page.hits</code>. If we told graphite to graph results for <code>stats.my_app.*.production.home-page.hits</code> we would get two graph lines &#8211; one for server-a and one for server-b. To combine them into a single measurement, use the <code>sumSeries</code> function. You can then use the alias function to give it a friendlier display name like &#8220;Home page.&#8221;

Graphiti has a peculiar way of specifying which function to use. In a normal series list, you have the following structure:

<pre><code>"targets": [
  [
    "stats.my_app.*.production.home-page.hits",
    {}
  ]
]
</code></pre>

The <code>{}</code> is an object used to specify the list of functions to apply, in order, on the series specified in the parent array. Each graphite function is specified as a key and its parameters as the value. A <code>true</code> value indicates the function needs no parameters and an array is provided if the function requires multiple parameters.

You&#8217;ll notice in the function documentation that each function usually takes two initial arguments, a context and a series name. In graphiti, you won&#8217;t need to specify those first two arguments.

Here&#8217;s an example of sumSeries and alias used together. Note that the order matters!

<pre><code>"targets": [
  [
    "stats.my_app.*.production.home-page.hits",
    {
      "sumSeries": true,
      "alias": "Homepage hits"
    }
  ]
]
</code></pre>

<h3>Different graph areaMode for different applications</h3>

While not well documented, graphite has a few options for displaying graph lines. By default, the &#8220;stacked&#8221; area mode stacks each measurement on top of each other into an area chart that combines multiple measurements that are uniquely shaded. This can be good for seeing grand totals. The blank option plots each measurement as a line on a line chart. This is preferable for comparing measurements.

<h3>Different metrics for different events</h3>

Each stats recording method provided by statsd-ruby and fozzie has different behavior, which isn&#8217;t well documented anywhere.

<ul>
<li><code>Stats.count</code> is the base method for sending a count of some event for a given instance. It&#8217;s rarely used alone.</li>
<li><code>Stats.increment</code> and <code>Stats.decrement</code> will adjust a count of an event. It&#8217;s useful for counting things like number of hits on a page, number of times an activity occurs, etc. It will be graphed as &#8220;average number of events per second&#8221;. So if your web app runs <code>Stats.increment 'hits'</code> 8 times over a 1 second period, the graph will draw a value of 8 for that second. Sometimes you will see fractional numbers charted. This is because graphite may average the data over a time period based on your schema storage settings and charting resolution.</li>
<li><code>Stats.timing</code> will take a block and store the amount of time the code in the block took to execute. It also keeps track of average, min, and max times, as well as standard deviation and total number of occurrences.</li>
<li><code>Stats.gauge</code> tracks absolute values over time. This is useful for tracking measurements like CPU, memory, and disk usage.</li>
<li>Fozzie provides <code>Stats.event 'party'</code> to track when an event happens. This is useful for tracking things like deploys or restarts. Equivalent functionality can be obtained in statsd-ruby by running <code>Stats.count 'party', Time.now.to_i</code>.</li>
</ul>


<h3>Bonus tip: Graphs on your Mac dashboard</h3>

If you&#8217;re using a mac, you can add your favorite graphs to your dashboard. Create a graph in graphiti, then view it on the graphiti dashboard with Safari. Click File->Open in Dashboard&#8230; and select the graph image with the select box. Now, you can quickly see important graphs at the press of a button!

Overall, statsd is a great tool and can add great visibility into your applications.
