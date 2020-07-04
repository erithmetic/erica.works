---
layout: post
title:  "Bundler to the Max"
date: 2010-07-28 12:00:00 -0500
categories: 
---

I have been spending the past few weeks creating and refactoring our <a href="http://carbon.brighterplanet.com/science">carbon model gems</a>, with the goal of making them easy to enhance, fix, and test by climate scientists and Ruby developers. I wanted to make contributing a simple process and <a href="http://rubybundler.com">bundler</a> fit the bill quite well.

A not-so-widely-known feature of the <a href="http://rubygems.org">Rubygems</a> API is the ability to declare a gem&#8217;s development dependencies, along with its runtime dependencies. If one planned on making changes to one of the emitter gems and testing it, she could run <code>gem install &lt;emitter_gem&gt; --development</code> and have any needed testing gems installed for the emitter gem.

This is all fine and good, but I chose to use bundler to manage our dependencies, as it adds a few extras that have been a tremendous help to us. To contribute to any of our gems, a developer can follow a simple process:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
</pre></td><td class='code'><pre><code class='bash'><span class='line'><span class="nv">$ </span>git clone git://github.com/brighterplanet/&lt;gem&gt;.git
</span><span class='line'><span class="nv">$ </span><span class="nb">cd</span> &lt;gem&gt;
</span><span class='line'><span class="nv">$ </span>gem install bundler --pre  <span class="c"># this is needed until bundler 1.0 is released</span>
</span><span class='line'><span class="nv">$ </span>bundle install
</span><span class='line'><span class="nv">$ </span>rake
</span></code></pre></td></tr></table></div></figure>


And Bob&#8217;s your uncle!

<h3>Bundler + Gemspecs</h3>

The first goodie that bundler provides is the ability to use the gem&#8217;s own gemspec to define the dependencies needed for development. For instance, our flight gem has a gemspec with dependencies:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
<span class='line-number'>8</span>
<span class='line-number'>9</span>
<span class='line-number'>10</span>
<span class='line-number'>11</span>
<span class='line-number'>12</span>
<span class='line-number'>13</span>
<span class='line-number'>14</span>
<span class='line-number'>15</span>
<span class='line-number'>16</span>
<span class='line-number'>17</span>
<span class='line-number'>18</span>
<span class='line-number'>19</span>
<span class='line-number'>20</span>
<span class='line-number'>21</span>
</pre></td><td class='code'><pre><code class='ruby'><span class='line'><span class="no">Gem</span><span class="o">::</span><span class="no">Specification</span><span class="o">.</span><span class="n">new</span> <span class="k">do</span> <span class="o">|</span><span class="n">s</span><span class="o">|</span>
</span><span class='line'>  <span class="c1"># ...</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_development_dependency</span><span class="p">(</span><span class="sx">%q&lt;activerecord&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 3.0.0.beta4&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_development_dependency</span><span class="p">(</span><span class="sx">%q&lt;bundler&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;&gt;= 1.0.0.beta.2&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_development_dependency</span><span class="p">(</span><span class="sx">%q&lt;cucumber&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 0.8.3&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_development_dependency</span><span class="p">(</span><span class="sx">%q&lt;jeweler&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 1.4.0&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_development_dependency</span><span class="p">(</span><span class="sx">%q&lt;rake&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;&gt;= 0&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_development_dependency</span><span class="p">(</span><span class="sx">%q&lt;rdoc&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;&gt;= 0&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_development_dependency</span><span class="p">(</span><span class="sx">%q&lt;rspec&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 2.0.0.beta.17&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_development_dependency</span><span class="p">(</span><span class="sx">%q&lt;sniff&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 0.0.10&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_runtime_dependency</span><span class="p">(</span><span class="sx">%q&lt;characterizable&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 0.0.12&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_runtime_dependency</span><span class="p">(</span><span class="sx">%q&lt;data_miner&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 0.5.2&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_runtime_dependency</span><span class="p">(</span><span class="sx">%q&lt;earth&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 0.0.7&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_runtime_dependency</span><span class="p">(</span><span class="sx">%q&lt;falls_back_on&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 0.0.2&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_runtime_dependency</span><span class="p">(</span><span class="sx">%q&lt;fast_timestamp&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 0.0.4&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_runtime_dependency</span><span class="p">(</span><span class="sx">%q&lt;leap&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 0.4.1&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_runtime_dependency</span><span class="p">(</span><span class="sx">%q&lt;summary_judgement&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 1.3.8&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_runtime_dependency</span><span class="p">(</span><span class="sx">%q&lt;timeframe&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 0.0.8&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="n">s</span><span class="o">.</span><span class="n">add_runtime_dependency</span><span class="p">(</span><span class="sx">%q&lt;weighted_average&gt;</span><span class="p">,</span> <span class="o">[</span><span class="s2">&quot;= 0.0.4&quot;</span><span class="o">]</span><span class="p">)</span>
</span><span class='line'>  <span class="c1"># ...</span>
</span><span class='line'><span class="k">end</span>
</span></code></pre></td></tr></table></div></figure>


Instead of defining these dependencies in both flight.gemspec and in Gemfile, we can instead give the following directive in our Gemfile:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
</pre></td><td class='code'><pre><code class='ruby'><span class='line'><span class="n">gemspec</span> <span class="ss">:path</span> <span class="o">=&gt;</span> <span class="s1">&#39;.&#39;</span>
</span></code></pre></td></tr></table></div></figure>


<h3>Bundler + Paths</h3>

We have a chain of gem dependencies, where an emitter gem depends on the sniff gem for development, which in turn depends on the earth gem for data models. In the olden days (like, 4 months ago) if I made a change to sniff, I would have to rebuild the gem and reinstall it. With bundler, I can simply tell my emitter gem to use a path to my local sniff repo as the gem source:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
</pre></td><td class='code'><pre><code class='ruby'><span class='line'><span class="n">gem</span> <span class="s1">&#39;sniff&#39;</span><span class="p">,</span> <span class="ss">:path</span> <span class="o">=&gt;</span> <span class="s1">&#39;../sniff&#39;</span>
</span></code></pre></td></tr></table></div></figure>


Now, any changes I make to sniff instantly appear in the emitter gem!

I had to add some special logic (a hack, if you will) to my gemspec definition for this to work, because the above gem statement in my Gemfile would conflict with the dependency listed in my gemspec (remember, I&#8217;m using my gemspec to tell bundler what gems I need). To get around this, I added an if clause to my gemspec definition that checks for an environment variable. If this variable exists, the gemspec will not request the gem and bundler will instead use my custom gem requirement that uses a local path:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
</pre></td><td class='code'><pre><code class='ruby'><span class='line'><span class="c1"># Rakefile  (we use jeweler to generate our gemspecs)</span>
</span><span class='line'><span class="no">Jeweler</span><span class="o">::</span><span class="no">Tasks</span><span class="o">.</span><span class="n">new</span> <span class="k">do</span> <span class="o">|</span><span class="n">gem</span><span class="o">|</span>
</span><span class='line'>  <span class="c1"># ...</span>
</span><span class='line'>  <span class="n">gem</span><span class="o">.</span><span class="n">add_development_dependency</span> <span class="s1">&#39;sniff&#39;</span><span class="p">,</span> <span class="s1">&#39;=0.0.10&#39;</span> <span class="k">unless</span> <span class="no">ENV</span><span class="o">[</span><span class="s1">&#39;LOCAL_SNIFF&#39;</span><span class="o">]</span>
</span><span class='line'>  <span class="c1"># ...</span>
</span><span class='line'><span class="k">end</span>
</span></code></pre></td></tr></table></div></figure>




<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
</pre></td><td class='code'><pre><code class='ruby'><span class='line'><span class="c1"># Gemfile</span>
</span><span class='line'><span class="n">gem</span> <span class="s1">&#39;sniff&#39;</span><span class="p">,</span> <span class="ss">:path</span> <span class="o">=&gt;</span> <span class="no">ENV</span><span class="o">[</span><span class="s1">&#39;LOCAL_SNIFF&#39;</span><span class="o">]</span> <span class="k">if</span> <span class="no">ENV</span><span class="o">[</span><span class="s1">&#39;LOCAL_SNIFF&#39;</span><span class="o">]</span>
</span></code></pre></td></tr></table></div></figure>


So now, if I want to make some changes to the sniff gem and test them out in my emitter, I do:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
<span class='line-number'>8</span>
<span class='line-number'>9</span>
</pre></td><td class='code'><pre><code class='bash'><span class='line'><span class="nv">$ </span><span class="nb">cd </span>sniff
</span><span class='line'>  <span class="c"># work work work</span>
</span><span class='line'><span class="nv">$ </span><span class="nb">cd</span> ../<span class="o">[</span>emitter<span class="o">]</span>
</span><span class='line'><span class="nv">$ </span><span class="nb">export </span><span class="nv">LOCAL_SNIFF</span><span class="o">=</span>~/sniff
</span><span class='line'><span class="nv">$ </span>rake gemspec
</span><span class='line'><span class="nv">$ </span>bundle update
</span><span class='line'>  <span class="c"># ...</span>
</span><span class='line'>sniff <span class="o">(</span>0.0.13<span class="o">)</span> using path /Users/dkastner/sniff
</span><span class='line'>  <span class="c"># ...</span>
</span></code></pre></td></tr></table></div></figure>


And then Bob is my uncle.

<h3>Bundler + Rakefile</h3>

This next idea has some drawbacks in terms of code cleanliness, but I think it offers a good way to point contributers in the right direction. One thing that frustrated me about Jeweler was that if I wanted to contribute to a gem, my typical work flow went like:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
<span class='line-number'>8</span>
</pre></td><td class='code'><pre><code class='bash'><span class='line'><span class="nv">$ </span><span class="nb">cd</span> <span class="o">[</span>project<span class="o">]</span>
</span><span class='line'>  <span class="c"># work work work</span>
</span><span class='line'><span class="nv">$ </span>rake <span class="nb">test</span>
</span><span class='line'>LoadError: No such file: <span class="s1">&#39;jeweler&#39;</span>
</span><span class='line'><span class="nv">$ </span>gem install jeweler
</span><span class='line'><span class="nv">$ </span>rake <span class="nb">test</span>
</span><span class='line'>LoadError: No such file: <span class="s1">&#39;shoulda&#39;</span>
</span><span class='line'>  <span class="c"># etc etc</span>
</span></code></pre></td></tr></table></div></figure>


I attempted to simplify this process, so a new developer who doesn&#8217;t read the README should be able to just do:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
<span class='line-number'>8</span>
</pre></td><td class='code'><pre><code class='bash'><span class='line'><span class="nv">$ </span><span class="nb">cd</span> <span class="o">[</span>emitter<span class="o">]</span>
</span><span class='line'>  <span class="c"># work work work</span>
</span><span class='line'><span class="nv">$ </span>rake <span class="nb">test</span>
</span><span class='line'>You need to <span class="sb">`</span>gem install bundler<span class="sb">`</span> and <span class="k">then </span>run <span class="sb">`</span>bundle install<span class="sb">`</span> to run rake tasks
</span><span class='line'><span class="nv">$ </span>gem install bundler
</span><span class='line'><span class="nv">$ </span>bundle install
</span><span class='line'><span class="nv">$ </span>rake <span class="nb">test</span>
</span><span class='line'>All tests pass!
</span></code></pre></td></tr></table></div></figure>


I achieved this by adding the following code to the top of the Rakefile:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
</pre></td><td class='code'><pre><code class='ruby'><span class='line'><span class="nb">require</span> <span class="s1">&#39;rubygems&#39;</span>
</span><span class='line'><span class="k">begin</span>
</span><span class='line'>  <span class="nb">require</span> <span class="s1">&#39;bundler&#39;</span>
</span><span class='line'>  <span class="no">Bundler</span><span class="o">.</span><span class="n">setup</span>
</span><span class='line'><span class="k">rescue</span> <span class="no">LoadError</span>
</span><span class='line'>  <span class="nb">puts</span> <span class="s1">&#39;You must `gem install bundler` and `bundle install` to run rake tasks&#39;</span>
</span><span class='line'><span class="k">end</span>
</span></code></pre></td></tr></table></div></figure>


This was convenient, but it created a chicken and egg problem: in order to generate a gemspec for the first time, bundler needed to know which dependencies it needed, which meant that it needed the gemspec, which is generated by the Rakefile, which requires bundler, which requires the gemspec, etc. etc. I overcame this problem by allowing an override:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
<span class='line-number'>8</span>
<span class='line-number'>9</span>
</pre></td><td class='code'><pre><code class='ruby'><span class='line'><span class="nb">require</span> <span class="s1">&#39;rubygems&#39;</span>
</span><span class='line'><span class="k">unless</span> <span class="no">ENV</span><span class="o">[</span><span class="s1">&#39;NOBUNDLE&#39;</span><span class="o">]</span>
</span><span class='line'>  <span class="k">begin</span>
</span><span class='line'>    <span class="nb">require</span> <span class="s1">&#39;bundler&#39;</span>
</span><span class='line'>    <span class="no">Bundler</span><span class="o">.</span><span class="n">setup</span>
</span><span class='line'>  <span class="k">rescue</span> <span class="no">LoadError</span>
</span><span class='line'>    <span class="nb">puts</span> <span class="s1">&#39;You must `gem install bundler` and `bundle install` to run rake tasks&#39;</span>
</span><span class='line'>  <span class="k">end</span>
</span><span class='line'><span class="k">end</span>
</span></code></pre></td></tr></table></div></figure>


So, if you&#8217;re really desparate, you can run <code>rake test NOBUNDLE=true</code>

<h3>More on Local Gems</h3>

Now that I had a way to easily tell bundler to use an actual gem or a local repo holding the gem, I wanted a way to quickly &#8220;flip the switch.&#8221; I wrote up a quick function in my ~/.bash_profile:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
<span class='line-number'>8</span>
<span class='line-number'>9</span>
<span class='line-number'>10</span>
<span class='line-number'>11</span>
<span class='line-number'>12</span>
<span class='line-number'>13</span>
<span class='line-number'>14</span>
<span class='line-number'>15</span>
<span class='line-number'>16</span>
<span class='line-number'>17</span>
<span class='line-number'>18</span>
<span class='line-number'>19</span>
<span class='line-number'>20</span>
<span class='line-number'>21</span>
<span class='line-number'>22</span>
<span class='line-number'>23</span>
<span class='line-number'>24</span>
<span class='line-number'>25</span>
<span class='line-number'>26</span>
<span class='line-number'>27</span>
<span class='line-number'>28</span>
<span class='line-number'>29</span>
<span class='line-number'>30</span>
<span class='line-number'>31</span>
<span class='line-number'>32</span>
<span class='line-number'>33</span>
<span class='line-number'>34</span>
<span class='line-number'>35</span>
<span class='line-number'>36</span>
<span class='line-number'>37</span>
<span class='line-number'>38</span>
</pre></td><td class='code'><pre><code class='bash'><span class='line'><span class="k">function </span>mod_devgem<span class="o">()</span> <span class="o">{</span>
</span><span class='line'>  <span class="nv">var</span><span class="o">=</span><span class="s2">&quot;LOCAL_`echo $2 | tr &#39;a-z&#39; &#39;A-Z&#39;`&quot;</span>
</span><span class='line'>
</span><span class='line'>  <span class="k">if</span> <span class="o">[</span> <span class="s2">&quot;$1&quot;</span> <span class="o">==</span> <span class="s2">&quot;disable&quot;</span> <span class="o">]</span>
</span><span class='line'>  <span class="k">then</span>
</span><span class='line'><span class="k">    </span><span class="nb">echo</span> <span class="s2">&quot;unset $var&quot;</span>
</span><span class='line'>    <span class="nb">unset</span> <span class="nv">$var</span>
</span><span class='line'>  <span class="k">else</span>
</span><span class='line'><span class="k">    </span><span class="nv">dir</span><span class="o">=</span><span class="k">${</span><span class="nv">3</span><span class="k">:-</span><span class="s2">&quot;~/$2&quot;</span><span class="k">}</span>
</span><span class='line'>    <span class="nb">echo</span> <span class="s2">&quot;export $var=$dir&quot;</span>
</span><span class='line'>    <span class="nb">export</span> <span class="nv">$var</span><span class="o">=</span><span class="nv">$dir</span>
</span><span class='line'>  <span class="k">fi</span>
</span><span class='line'><span class="o">}</span>
</span><span class='line'>
</span><span class='line'><span class="k">function </span>devgems <span class="o">()</span> <span class="o">{</span>
</span><span class='line'>  <span class="c"># Usage: devgems [enable|disable] [gemname]</span>
</span><span class='line'>  <span class="nv">cmd</span><span class="o">=</span><span class="k">${</span><span class="nv">1</span><span class="k">:-</span><span class="s2">&quot;enable&quot;</span><span class="k">}</span>
</span><span class='line'>
</span><span class='line'>  <span class="k">if</span> <span class="o">[</span> <span class="s2">&quot;$1&quot;</span> <span class="o">==</span> <span class="s2">&quot;list&quot;</span> <span class="o">]</span>
</span><span class='line'>  <span class="k">then</span>
</span><span class='line'><span class="k">    </span>env | grep LOCAL
</span><span class='line'>    <span class="k">return</span>
</span><span class='line'><span class="k">  fi</span>
</span><span class='line'>
</span><span class='line'><span class="k">  if</span> <span class="o">[</span> -z <span class="nv">$2</span> <span class="o">]</span>
</span><span class='line'>  <span class="k">then</span>
</span><span class='line'><span class="k">    </span>mod_devgem <span class="nv">$cmd</span> characterizable
</span><span class='line'>    mod_devgem <span class="nv">$cmd</span> cohort_scope
</span><span class='line'>    mod_devgem <span class="nv">$cmd</span> falls_back_on
</span><span class='line'>    mod_devgem <span class="nv">$cmd</span> leap
</span><span class='line'>    mod_devgem <span class="nv">$cmd</span> loose_tight_dictionary
</span><span class='line'>    mod_devgem <span class="nv">$cmd</span> sniff
</span><span class='line'>    mod_devgem <span class="nv">$cmd</span> data_miner
</span><span class='line'>    mod_devgem <span class="nv">$cmd</span> earth
</span><span class='line'>  <span class="k">else</span>
</span><span class='line'><span class="k">    </span>mod_devgem <span class="nv">$cmd</span> <span class="nv">$2</span>
</span><span class='line'>  <span class="k">fi</span>
</span><span class='line'><span class="o">}</span>
</span></code></pre></td></tr></table></div></figure>


This gives me a few commands:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
</pre></td><td class='code'><pre><code class='bash'><span class='line'><span class="nv">$ </span>devgems <span class="nb">enable </span>sniff
</span><span class='line'>  <span class="c"># sets LOCAL_SNIFF=~/sniff</span>
</span><span class='line'><span class="nv">$ </span>devgems disable sniff
</span><span class='line'>  <span class="c"># clears LOCAL_SNIFF</span>
</span><span class='line'><span class="nv">$ </span>devgems list
</span><span class='line'>  <span class="c"># lists each LOCAL_ environment variable</span>
</span></code></pre></td></tr></table></div></figure>


I now have a well-oiled gem development machine!

Overall, after a few frustrations with bundler, I&#8217;m now quite happy with it, especially the power and convenience it gives me in developing gems.

I&#8217;m really interested to hear any of your thoughts on this. Drop me a line at <a href="http://twitter.com/dkastner">@dkastner</a>.
