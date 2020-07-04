---
layout: post
title:  "Add node.js/CommonJS style require() to client-side JavaScript with browserify"
date: 2011-08-04 12:00:00 -0500
categories: 
---

While working on <a href="http://careplane.org">Careplane</a> recently, I ran into a problem that was made easier by <a href="http://goessner.net/articles/JsonPath/">JSONPath</a>. I was already enjoying the ability to test my JavaScript with <a href="http://nodejs.org">Node.js</a>, <a href="">Jasmine</a>, and other <a href="http://npmjs.org/">npm</a> packages. It would be nice if I could get the same package system I have with npm and use it with my client-side JavaScript.

With <a href="http://github.com/substack/node-browserify">browserify</a>, now I can!

There are three major benefits to using browserify, which I&#8217;ll detail below.

<h3>Using npm Modules in Your Client-Side Scripts</h3>

In Careplane, I wanted to use the <a href="">JSONPath npm package</a>. With browserify, I can write the following into my client-side code:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
</pre></td><td class='code'><pre><code class='javascript'><span class='line'><span class="cm">/* clientside.js */</span>
</span><span class='line'><span class="kd">var</span> <span class="nx">jsonpath</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="s1">&#39;JSONPath&#39;</span><span class="p">);</span>
</span><span class='line'><span class="kd">var</span> <span class="nx">$</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="s1">&#39;jquery-browserify&#39;</span><span class="p">);</span>
</span><span class='line'>
</span><span class='line'><span class="kd">var</span> <span class="nx">owned_data</span> <span class="o">=</span> <span class="nx">jsonpath</span><span class="p">.</span><span class="nb">eval</span><span class="p">(</span><span class="nx">myData</span><span class="p">,</span> <span class="s1">&#39;$.paths[*].to.data[?(@.owner == &quot;Erica&quot;)]&#39;</span><span class="p">);</span>
</span><span class='line'><span class="nx">$</span><span class="p">(</span><span class="s1">&#39;p.important&#39;</span><span class="p">).</span><span class="nx">html</span><span class="p">(</span><span class="nx">owned_data</span><span class="p">);</span>
</span></code></pre></td></tr></table></div></figure>


From the command-line, I can create a single JavaScript file (I&#8217;ll call it application.js) that contains my code and includes the JSONPath package. I can even bake jQuery into application.js!

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
</pre></td><td class='code'><pre><code class='bash'><span class='line'>&gt; npm install browserify
</span><span class='line'>&gt; npm install JSONPath
</span><span class='line'>&gt; npm install jquery-browserify
</span><span class='line'>&gt; ./node_modules/.bin/browserify -r JSONPath -r jquery-browserify -e clientside.js -o application.js
</span></code></pre></td></tr></table></div></figure>


Now that I have a nicely wrapped application.js, I can include it from a web page, or in this case, the browser plugin I&#8217;m developing.

Here&#8217;s an example of use from a web page:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
</pre></td><td class='code'><pre><code class='html'><span class='line'><span class="nt">&lt;script </span><span class="na">type=</span><span class="s">&quot;text/javascript&quot;</span> <span class="na">src=</span><span class="s">&quot;application.js&quot;</span><span class="nt">&gt;&lt;/script&gt;</span>
</span></code></pre></td></tr></table></div></figure>


<em>N.B. Not all npm packages will work client-side, especially ones that depend on core Node.js modules like <code>fs</code> or <code>http</code>.</em>

<h3>Organize Your Code</h3>

Careplane has a ton of JavaScript split into a separate file for each class. Previously, I was using simple concatenation and a hand-crafted file list to construct an application.js and to determine script load order. Now it can be managed automatically by browserify because each of my class files can require other class files.

For instance, the Kayak class in <code>src/drivers/Kayak.js</code> depends on the Driver class in <code>src/Driver.js</code>. I can now do this:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
</pre></td><td class='code'><pre><code class='javascript'><span class='line'><span class="cm">/* src/Driver.js */</span>
</span><span class='line'>
</span><span class='line'><span class="kd">var</span> <span class="nx">Driver</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{};</span>
</span><span class='line'>
</span><span class='line'><span class="cm">/* ... */</span>
</span><span class='line'>
</span><span class='line'><span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="nx">Driver</span><span class="p">;</span>
</span></code></pre></td></tr></table></div></figure>




<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
<span class='line-number'>8</span>
<span class='line-number'>9</span>
</pre></td><td class='code'><pre><code class='javascript'><span class='line'><span class="cm">/* src/Kayak.js */</span>
</span><span class='line'><span class="kd">var</span> <span class="nx">Driver</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="s1">&#39;../Driver&#39;</span><span class="p">)</span>
</span><span class='line'>
</span><span class='line'><span class="kd">var</span> <span class="nx">Kayak</span> <span class="o">=</span> <span class="kd">function</span><span class="p">(</span><span class="nx">foo</span><span class="p">)</span> <span class="p">{</span> <span class="k">this</span><span class="p">.</span><span class="nx">foo</span> <span class="o">=</span> <span class="nx">foo</span><span class="p">;</span> <span class="p">};</span>
</span><span class='line'><span class="nx">Kayak</span><span class="p">.</span><span class="nx">prototype</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">Driver</span><span class="p">();</span>
</span><span class='line'>
</span><span class='line'><span class="cm">/* ... */</span>
</span><span class='line'>
</span><span class='line'><span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="nx">Kayak</span><span class="p">;</span>
</span></code></pre></td></tr></table></div></figure>




<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
</pre></td><td class='code'><pre><code class='javascript'><span class='line'><span class="cm">/* src/main.js */</span>
</span><span class='line'>
</span><span class='line'><span class="kd">var</span> <span class="nx">Kayak</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="s1">&#39;./drivers/Kayak&#39;</span><span class="p">);</span> <span class="c1">// paths are relative to src, main.js&#39; cwd</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Kayak</span><span class="p">.</span><span class="nx">getThePartyStarted</span><span class="p">();</span>
</span></code></pre></td></tr></table></div></figure>




<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
</pre></td><td class='code'><pre><code class='bash'><span class='line'>&gt; ./node_modules/.bin/browserify -e src/main.js -o application.js
</span></code></pre></td></tr></table></div></figure>


Super simple!

<h3>Uniformity With Testing Environment</h3>

I like to run my <a href="http://pivotal.github.com/jasmine/">Jasmine</a> JavaScript tests from the command-line and so does our <a href="http://martinfowler.com/articles/continuousIntegration.html">Continuous Integration</a> system. With <a href="http://github.com/mhevery/jasmine-node">jasmine-node</a>, I had to maintain a list of files in my src directory that were loaded in a certain order in order to run my tests. Now, each spec file can use Node.js&#8217; CommonJS <code>require</code> statement to require files from my src directory, and all dependencies are automatically managed.

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
</pre></td><td class='code'><pre><code class='javascript'><span class='line'><span class="cm">/* spec/javascripts/drivers/KayakSpec.js */</span>
</span><span class='line'>
</span><span class='line'><span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;Kayak&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="kd">var</span> <span class="nx">Kayak</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="s1">&#39;src/drivers/Kayak&#39;</span><span class="p">);</span>
</span><span class='line'>  <span class="cm">/* ... */</span>
</span><span class='line'><span class="p">});</span>
</span></code></pre></td></tr></table></div></figure>




<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
</pre></td><td class='code'><pre><code class='bash'><span class='line'>&gt; rake examples<span class="o">[</span>KayakSpec<span class="o">]</span>
</span><span class='line'>Starting tests
</span><span class='line'>............FFF............FF
</span></code></pre></td></tr></table></div></figure>


When I run my specs from the command-line with Node.js (I have an <code>examples</code> rake task that runs node and jasmine), Node&#8217;s built-in CommonJS <code>require</code> is used and it recognizes the <code>require</code> statements I wrote into src/drivers/Kayak.js.

I can also run my Jasmine specs from the standard Jasmine web server. All I have to do is create a browserified script that is included before Jasmine runs its tests in my browser.

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
</pre></td><td class='code'><pre><code class='javascript'><span class='line'><span class="cm">/* src/jasmine.js */</span>
</span><span class='line'><span class="kd">var</span> <span class="nx">Kayak</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="s1">&#39;./drivers/Kayak&#39;</span><span class="p">);</span>  <span class="c1">// browserify needs a starting point to resolve all dependencies</span>
</span></code></pre></td></tr></table></div></figure>




<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
</pre></td><td class='code'><pre><code class='yaml'><span class='line'><span class="c1"># spec/javascripts/support/jasmine.yml</span>
</span><span class='line'><span class="l-Scalar-Plain">src_files</span><span class="p-Indicator">:</span>
</span><span class='line'>  <span class="p-Indicator">-</span> <span class="l-Scalar-Plain">spec/javascripts/support/application.js</span>
</span></code></pre></td></tr></table></div></figure>




<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
</pre></td><td class='code'><pre><code class='bash'><span class='line'>&gt; ./node_modules/.bin/browserify -e src/jasmine.js -o spec/javascripts/support/application.js
</span><span class='line'>&gt; rake jasmine:server
</span><span class='line'>your tests are here:
</span><span class='line'>  http://localhost:8888/
</span><span class='line'><span class="o">[</span>2011-08-04 16:15:56<span class="o">]</span> INFO  WEBrick 1.3.1
</span><span class='line'><span class="o">[</span>2011-08-04 16:15:56<span class="o">]</span> INFO  ruby 1.9.2 <span class="o">(</span>2011-02-18<span class="o">)</span> <span class="o">[</span>x86_64-darwin10.4.0<span class="o">]</span>
</span><span class='line'><span class="o">[</span>2011-08-04 16:15:56<span class="o">]</span> INFO  WEBrick::HTTPServer#start: <span class="nv">pid</span><span class="o">=</span>61833 <span class="nv">port</span><span class="o">=</span>8888
</span></code></pre></td></tr></table></div></figure>


When I visit http://localhost:8888 in my browser, my Jasmine tests all run!

The only drawback is that my code has to be re-browserified whenever it changes. This only adds about a second of overhead while I wait for my <a href="http://rubygems.org/gems/watchr">watchr</a> script to run the browserification.

<h3>More</h3>

If you&#8217;d like to take a look at the environment I set up for Careplane, you can check out the <a href="http://github.com/brighterplanet/careplane">source</a>.

I hope you enjoy browserify as much as I have!
