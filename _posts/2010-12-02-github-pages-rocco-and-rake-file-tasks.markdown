---
layout: post
title:  "Github Pages, Rocco, and Rake File Tasks"
date: 2010-12-02 12:00:00 -0600
categories: 
---

Recently, we spiffed up some of our emitters with enhanced documentation using <a href="http://rubygems.org/gems/rocco">Rocco</a>, a Ruby port of Docco.  We combined this with github&#8217;s ability to set up <a href="http://pages.github.com/">static html pages for a repo</a>.  By setting up a branch called gh-pages, Github will serve any html files in that branch.  We use this feature to display Rocco-generated documentation of our carbon models (check out the <a href="http://brighterplanet.github.com/flight/carbon_model.html">flight emitter</a> for an example).

This was great, but we were missing a way to automate the process by which Rocco will generate its documentation from the code in the master branch and place them in the gh-pages branch.  Ryan Tomayko came to the rescue and <a href="http://github.com/rtomayko/rocco/blob/cffe49a813bbc083c695997c5d2a7da7f3cf99a1/Rakefile">wrote a rake task</a> that creates a docs directory within the project and initializes a new git repository within the directory, which points to the project&#8217;s gh-pages branch.  When documentation is generated, it is copied to the docs folder and pushed to gh-pages.

What intrigued me about the rake task was its use of file tasks.  It&#8217;s a feature of rake I had never noticed before, but it&#8217;s pretty slick.  A file task says, &#8220;if the specified path does not exist, execute the following code.&#8221;  Since many unix tools use files for configuration, this feature plays well with many utilities, such as git, your favorite editor, etc.

For example, you could define a rake task that will create a .rvmrc for your project using your current <a href="http://rvm.beginrescueend.com/">RVM</a>-installed ruby:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
<span class='line-number'>6</span>
<span class='line-number'>7</span>
</pre></td><td class='code'><pre><code class='ruby'><span class='line'><span class="c1"># Rakefile</span>
</span><span class='line'>
</span><span class='line'><span class="n">file</span> <span class="s1">&#39;.rvmrc&#39;</span> <span class="k">do</span> <span class="o">|</span><span class="n">f</span><span class="o">|</span>
</span><span class='line'>  <span class="no">File</span><span class="o">.</span><span class="n">open</span><span class="p">(</span><span class="n">f</span><span class="o">.</span><span class="n">name</span><span class="p">,</span> <span class="s1">&#39;w&#39;</span><span class="p">)</span> <span class="k">do</span> <span class="o">|</span><span class="n">rvmrc</span><span class="o">|</span>
</span><span class='line'>    <span class="n">rvmrc</span><span class="o">.</span><span class="n">puts</span> <span class="s2">&quot;rvm </span><span class="si">#{</span><span class="no">ENV</span><span class="o">[</span><span class="s1">&#39;rvm_ruby_string&#39;</span><span class="o">]</span><span class="si">}</span><span class="s2">&quot;</span>
</span><span class='line'>  <span class="k">end</span>
</span><span class='line'><span class="k">end</span>
</span></code></pre></td></tr></table></div></figure>


When you run <code>rake .rvmrc</code>, your .rvmrc will be generated.  Try it out!

There is all kinds of magic you can work with a file task.  A novel way in which Ryan&#8217;s Rocco tasks use file tasks is when deciding whether to create a git remote based on whether there is a file referencing the remote in the .git configuration directory:

<figure class='code'><figcaption><span></span></figcaption><div class="highlight"><table><tr><td class="gutter"><pre class="line-numbers"><span class='line-number'>1</span>
<span class='line-number'>2</span>
<span class='line-number'>3</span>
<span class='line-number'>4</span>
<span class='line-number'>5</span>
</pre></td><td class='code'><pre><code class='ruby'><span class='line'><span class="c1"># Rakefile</span>
</span><span class='line'>
</span><span class='line'><span class="n">file</span> <span class="s1">&#39;.git/refs/heads/gh-pages&#39;</span> <span class="o">=&gt;</span> <span class="s1">&#39;docs/&#39;</span> <span class="k">do</span> <span class="o">|</span><span class="n">f</span><span class="o">|</span>
</span><span class='line'>  <span class="sb">`cd docs &amp;&amp; git branch gh-pages --track origin/gh-pages`</span>
</span><span class='line'><span class="k">end</span>
</span></code></pre></td></tr></table></div></figure>


Happy raking!
