---
layout: post
title:  "CurbFu - Now With Killer Testing"
date: 2009-05-20 12:00:00 -0500
categories: 
---

<a href="http://twitter.com/hypomodern">Matt Wilson</a> and <a href="http://twitter.com/dkastner">I</a> have been making some awesome new updates to <a href="http://github.com/gdi/curb-fu/tree/master">CurbFu</a> - our convenient wrapper for Ruby&#8217;s <a href="http://github.com/taf2/curb/tree/master">curb</a> gem - itself an wrapper around <a href="http://curl.haxx.se/">libcurl</a>.

Our latest improvement revolves around integration testing.  At <a href="http://greenviewdata.com/">Greenview Data</a>, we have several Rack apps that talk to each other, including a Rails app.  While all of code for the individual apps was unit tested, we still lacked a good integration test to make sure everything played well together.

In the past I had tried to set up a VM instance that was controlled by a series of RSpec Story Runner tests.  Getting the test server instances updated and running was a pain, managing all of the running processes was a pain (especially testing a system with down processes), and writing tests to verify data became quite contrived.

With the introduction of Bryan Helmkamp&#8217;s <a href="http://github.com/brynary/rack-test/tree/master">Rack::Test</a> and Rails 2.3&#8217;s support for <a href="http://github.com/chneukirchen/rack/tree/master">Rack</a>, I dreamed of having these apps running in memory, able to send HTTP requests to each other without having to set up a separate integration testing system.  Now that all of our apps use CurbFu to send HTTP requests to each other, we seized the opportunity to create a testing module that override&#8217;s CurbFu&#8217;s get/post/put/delete operations.  Instead of sending a real HTTP request over the internet, CurbFu will now call any rack apps you specify directly using Rack::Test.

Essentially, this system is similar to <a href="http://github.com/chrisk/fakeweb/tree/master">FakeWeb</a> and <a href="http://github.com/brynary/webrat/tree/master">Webrat&#8217;s</a> newly added Rack support, but the difference is that if you use CurbFu for all of your HTTP communication, you can quickly and easily set up an integration test environment or set up smart mocks of HTTP services.

How does it work?

<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">require &#8216;curb-fu&#8217;</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">CurbFu.stubs = {</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">&#8216;a.example.com&#8217; =&gt; RackApp1.new,</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">&#8216;b.example.com&#8217; =&gt; RackApp2.new</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">}</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">class RackApp1</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">def call(env)</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">CurbFu.get(&#8216;http://b.example.com&#8217;)</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">end</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">end</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">class RackApp2</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">def call(env)</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">puts &#8220;WOW!&#8221;</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">end</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">end</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">CurbFu.get(&#8216;a.example.com&#8217;)</div>


<div id="_mcePaste" style="position:absolute;left:-10000px;top:345px;width:1px;height:1px;">#=&gt; WOW!</div>


<code>

<pre>require 'curb-fu'

CurbFu.stubs = {
  'a.example.com' =&gt; RackApp1.new,
  'b.example.com' =&gt; RackApp2.new
}

class RackApp1
  def call(env)
    response = Rack::Response.new
    response.status, response.body = [200, '']
    CurbFu.get('http://b.example.com')
    response.finish
  end
end

class RackApp2
  def call(env)
    response = Rack::Response.new
    response.status, response.body = [200, "WOW!"]
    response.finish
  end
end

puts CurbFu.get('a.example.com').body
#=&gt; WOW!</pre>


</code>

Loading a Rails app is a bit more involved:

<code> </code>

<code>

<pre>require 'curb-fu'
require '/path/to/rails/app/config/environment'
require 'dispatch'

CurbFu.stubs = {
  'my.cool.railsa.pp' =&gt; ActionController::Dispatcher.new
}</pre>


</code>

We&#8217;ve set up a series of <a href="http://github.com/aslakhellesoy/cucumber/tree/master">Cucumber</a> stories to test the interplay between our various rack apps.  By having the rails app in memory, we&#8217;re able to access ActiveRecord objects from our step definitions, as if we&#8217;re within the rails app. We&#8217;re also able to stub specific methods on objects within our rack apps and we throw up quick little Rack apps that can pretend to be things like a Solr server or a site hosting an RSS feed.  The sky is the limit!

Once caveat we&#8217;ve run into is the issue of namespacing.  It may be a good idea to namespace your Rails model classes as they may conflict with other loaded Rack apps.

Have fun testing!

Links:
<a href="http://github.com/gdi/curb-fu/tree/master"> CurbFu</a>
