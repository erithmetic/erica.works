---
layout: post
title:  "A Pattern for Javascript Events"
date: 2011-06-10 12:00:00 -0500
categories: 
---

While working on <a href="http://hootroot.com">Hootroot</a> and <a href="http://careplane.org">Careplane</a>, I found myself getting frustrated with the way I was having handling events. Over time, however, I stopped fighting the language and learned a pattern that I believe is easiest to test and read.

I&#8217;ll work with a simple example to show you my thought process.

Initially, I started handling my events with standard closures:

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
<span class='line-number'>39</span>
<span class='line-number'>40</span>
<span class='line-number'>41</span>
<span class='line-number'>42</span>
<span class='line-number'>43</span>
<span class='line-number'>44</span>
<span class='line-number'>45</span>
<span class='line-number'>46</span>
<span class='line-number'>47</span>
<span class='line-number'>48</span>
<span class='line-number'>49</span>
<span class='line-number'>50</span>
<span class='line-number'>51</span>
<span class='line-number'>52</span>
<span class='line-number'>53</span>
<span class='line-number'>54</span>
<span class='line-number'>55</span>
<span class='line-number'>56</span>
<span class='line-number'>57</span>
</pre></td><td class='code'><pre><code class='javascript'><span class='line'><span class="c1">//Rocket.js</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span> <span class="o">=</span> <span class="kd">function</span><span class="p">(</span><span class="nx">location</span><span class="p">)</span> <span class="p">{</span>
</span><span class='line'>  <span class="k">this</span><span class="p">.</span><span class="nx">location</span> <span class="o">=</span> <span class="nx">location</span><span class="p">;</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">ignite</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="cm">/* ... */</span> <span class="p">};</span>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">scrub</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="cm">/* ... */</span> <span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">launch</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="kd">var</span> <span class="nx">rocket</span> <span class="o">=</span> <span class="k">this</span><span class="p">;</span>
</span><span class='line'>  <span class="nx">$</span><span class="p">.</span><span class="nx">ajax</span><span class="p">(</span><span class="s1">&#39;/launch_code&#39;</span><span class="p">,</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">data</span><span class="o">:</span> <span class="p">{</span> <span class="nx">location</span><span class="o">:</span> <span class="k">this</span><span class="p">.</span><span class="nx">location</span> <span class="p">},</span>
</span><span class='line'>    <span class="nx">success</span><span class="o">:</span> <span class="kd">function</span><span class="p">(</span><span class="nx">data</span><span class="p">)</span> <span class="p">{</span>
</span><span class='line'>      <span class="k">if</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">isReady</span><span class="p">())</span> <span class="p">{</span>
</span><span class='line'>        <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Launching from &#39;</span> <span class="o">+</span> <span class="nx">rocket</span><span class="p">.</span><span class="nx">location</span><span class="p">);</span>
</span><span class='line'>        <span class="nx">rocket</span><span class="p">.</span><span class="nx">ignite</span><span class="p">(</span><span class="nx">data</span><span class="p">.</span><span class="nx">launchCode</span><span class="p">);</span>
</span><span class='line'>      <span class="p">}</span> <span class="k">else</span> <span class="p">{</span>
</span><span class='line'>        <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Not ready to launch!&#39;</span><span class="p">);</span>
</span><span class='line'>      <span class="p">}</span>
</span><span class='line'>    <span class="p">},</span>
</span><span class='line'>    <span class="nx">error</span><span class="o">:</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Failed to get launch code for &#39;</span> <span class="o">+</span> <span class="nx">rocket</span><span class="p">.</span><span class="nx">location</span><span class="p">);</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">scrub</span><span class="p">();</span>
</span><span class='line'>    <span class="p">}</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="c1">//RocketSpec.js</span>
</span><span class='line'>
</span><span class='line'><span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;Rocket&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;#launch&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>    <span class="kd">var</span> <span class="nx">rocket</span><span class="p">;</span>
</span><span class='line'>    <span class="nx">beforeEach</span><span class="p">(</span><span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">rocket</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">Rocket</span><span class="p">(</span><span class="s1">&#39;Cape Canaveral, FL&#39;</span><span class="p">);</span>
</span><span class='line'>      <span class="nx">fakeAjax</span><span class="p">({</span><span class="nx">urls</span><span class="o">:</span> <span class="p">{</span><span class="s1">&#39;/launch_code&#39;</span><span class="o">:</span> <span class="p">{</span><span class="nx">successData</span><span class="o">:</span> <span class="s1">&#39;{ &quot;launchCode&quot;: 12345 }&#39;</span><span class="p">}}})</span>
</span><span class='line'>      <span class="nx">spyOn</span><span class="p">(</span><span class="nx">rocket</span><span class="p">,</span> <span class="s1">&#39;ignite&#39;</span><span class="p">);</span>
</span><span class='line'>      <span class="nx">spyOn</span><span class="p">(</span><span class="nx">rocket</span><span class="p">,</span> <span class="s1">&#39;scrub&#39;</span><span class="p">);</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>
</span><span class='line'>    <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;ignites if ready&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">isReady</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="k">return</span> <span class="kc">true</span><span class="p">;</span> <span class="p">};</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">launch</span><span class="p">();</span>
</span><span class='line'>      <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">ignite</span><span class="p">).</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>    <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;waits if not ready&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">isReady</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="k">return</span> <span class="kc">false</span><span class="p">;</span> <span class="p">};</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">launch</span><span class="p">();</span>
</span><span class='line'>      <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">ignite</span><span class="p">).</span><span class="nx">not</span><span class="p">.</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>    <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;scrubs if a bad launch code is given&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">fakeAjax</span><span class="p">({</span><span class="nx">urls</span><span class="o">:</span> <span class="p">{</span><span class="s1">&#39;/launch_code&#39;</span><span class="o">:</span> <span class="p">{</span> <span class="nx">errorMessage</span><span class="o">:</span> <span class="s1">&#39;{ &quot;error&quot;: &quot;too bad&quot; }&#39;</span><span class="p">}}})</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">launch</span><span class="p">();</span>
</span><span class='line'>      <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">scrub</span><span class="p">).</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'><span class="p">});</span>
</span></code></pre></td></tr></table></div></figure>


Because of the way <a href="http://www.digital-web.com/articles/scope_in_javascript/">this</a> works in JavaScript, I had to assign the <code>this</code> that referred to the current instance of Rocket to a temporary variable that is referenced in the event handlers. This seemed kludgy to me, and I soon discovered the $.proxy() method that jQuery (and other frameworks similarly) provide:

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
<span class='line-number'>39</span>
<span class='line-number'>40</span>
<span class='line-number'>41</span>
<span class='line-number'>42</span>
<span class='line-number'>43</span>
<span class='line-number'>44</span>
<span class='line-number'>45</span>
<span class='line-number'>46</span>
<span class='line-number'>47</span>
<span class='line-number'>48</span>
<span class='line-number'>49</span>
<span class='line-number'>50</span>
<span class='line-number'>51</span>
<span class='line-number'>52</span>
<span class='line-number'>53</span>
<span class='line-number'>54</span>
<span class='line-number'>55</span>
<span class='line-number'>56</span>
</pre></td><td class='code'><pre><code class='javascript'><span class='line'><span class="c1">//Rocket.js</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span> <span class="o">=</span> <span class="kd">function</span><span class="p">(</span><span class="nx">location</span><span class="p">)</span> <span class="p">{</span>
</span><span class='line'>  <span class="k">this</span><span class="p">.</span><span class="nx">location</span> <span class="o">=</span> <span class="nx">location</span><span class="p">;</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">ignite</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="cm">/* ... */</span> <span class="p">};</span>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">scrub</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="cm">/* ... */</span> <span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">launch</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="nx">$</span><span class="p">.</span><span class="nx">ajax</span><span class="p">(</span><span class="s1">&#39;/launch_code&#39;</span><span class="p">,</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">data</span><span class="o">:</span> <span class="p">{</span> <span class="nx">location</span><span class="o">:</span> <span class="k">this</span><span class="p">.</span><span class="nx">location</span> <span class="p">},</span>
</span><span class='line'>    <span class="nx">success</span><span class="o">:</span> <span class="nx">$</span><span class="p">.</span><span class="nx">proxy</span><span class="p">(</span><span class="kd">function</span><span class="p">(</span><span class="nx">data</span><span class="p">)</span> <span class="p">{</span>
</span><span class='line'>      <span class="k">if</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">isReady</span><span class="p">())</span> <span class="p">{</span>
</span><span class='line'>        <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Launching from &#39;</span> <span class="o">+</span> <span class="k">this</span><span class="p">.</span><span class="nx">location</span><span class="p">);</span>
</span><span class='line'>        <span class="k">this</span><span class="p">.</span><span class="nx">ignite</span><span class="p">(</span><span class="nx">data</span><span class="p">.</span><span class="nx">launchCode</span><span class="p">)</span>
</span><span class='line'>      <span class="p">}</span> <span class="k">else</span> <span class="p">{</span>
</span><span class='line'>        <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Not ready to launch!&#39;</span><span class="p">);</span>
</span><span class='line'>      <span class="p">}</span>
</span><span class='line'>    <span class="p">},</span> <span class="k">this</span><span class="p">),</span>
</span><span class='line'>    <span class="nx">error</span><span class="o">:</span> <span class="nx">$</span><span class="p">.</span><span class="nx">proxy</span><span class="p">(</span><span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Failed to get launch code for &#39;</span> <span class="o">+</span> <span class="k">this</span><span class="p">.</span><span class="nx">location</span><span class="p">);</span>
</span><span class='line'>      <span class="k">this</span><span class="p">.</span><span class="nx">scrub</span><span class="p">();</span>
</span><span class='line'>    <span class="p">},</span> <span class="k">this</span><span class="p">)</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="c1">//RocketSpec.js</span>
</span><span class='line'>
</span><span class='line'><span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;Rocket&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;#launch&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>    <span class="kd">var</span> <span class="nx">rocket</span><span class="p">;</span>
</span><span class='line'>    <span class="nx">beforeEach</span><span class="p">(</span><span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">rocket</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">Rocket</span><span class="p">(</span><span class="s1">&#39;Cape Canaveral, FL&#39;</span><span class="p">);</span>
</span><span class='line'>      <span class="nx">fakeAjax</span><span class="p">({</span><span class="nx">urls</span><span class="o">:</span> <span class="p">{</span><span class="s1">&#39;/launch_code&#39;</span><span class="o">:</span> <span class="p">{</span><span class="nx">successData</span><span class="o">:</span> <span class="s1">&#39;{ &quot;launchCode&quot;: 12345 }&#39;</span><span class="p">}}})</span>
</span><span class='line'>      <span class="nx">spyOn</span><span class="p">(</span><span class="nx">rocket</span><span class="p">,</span> <span class="s1">&#39;ignite&#39;</span><span class="p">);</span>
</span><span class='line'>      <span class="nx">spyOn</span><span class="p">(</span><span class="nx">rocket</span><span class="p">,</span> <span class="s1">&#39;scrub&#39;</span><span class="p">);</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>
</span><span class='line'>    <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;ignites if ready&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">isReady</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="k">return</span> <span class="kc">true</span><span class="p">;</span> <span class="p">};</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">launch</span><span class="p">();</span>
</span><span class='line'>      <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">ignite</span><span class="p">).</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>    <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;waits if not ready&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">isReady</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="k">return</span> <span class="kc">false</span><span class="p">;</span> <span class="p">};</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">launch</span><span class="p">();</span>
</span><span class='line'>      <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">ignite</span><span class="p">).</span><span class="nx">not</span><span class="p">.</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>    <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;scrubs if a bad launch code is given&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">fakeAjax</span><span class="p">({</span><span class="nx">urls</span><span class="o">:</span> <span class="p">{</span><span class="s1">&#39;/launch_code&#39;</span><span class="o">:</span> <span class="p">{</span> <span class="nx">errorMessage</span><span class="o">:</span> <span class="s1">&#39;{ &quot;error&quot;: &quot;too bad&quot; }&#39;</span><span class="p">}}})</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">launch</span><span class="p">();</span>
</span><span class='line'>      <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">scrub</span><span class="p">).</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'><span class="p">});</span>
</span></code></pre></td></tr></table></div></figure>


The problem now is there are all sorts of functions hanging around within <code>Rocket#launch()</code> that are a bit difficult to test in a straightforward manner. Solution: create some functions on Rocket that act as event handlers.

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
<span class='line-number'>39</span>
<span class='line-number'>40</span>
<span class='line-number'>41</span>
<span class='line-number'>42</span>
<span class='line-number'>43</span>
<span class='line-number'>44</span>
<span class='line-number'>45</span>
<span class='line-number'>46</span>
<span class='line-number'>47</span>
<span class='line-number'>48</span>
<span class='line-number'>49</span>
<span class='line-number'>50</span>
<span class='line-number'>51</span>
<span class='line-number'>52</span>
<span class='line-number'>53</span>
<span class='line-number'>54</span>
<span class='line-number'>55</span>
<span class='line-number'>56</span>
<span class='line-number'>57</span>
<span class='line-number'>58</span>
<span class='line-number'>59</span>
<span class='line-number'>60</span>
<span class='line-number'>61</span>
</pre></td><td class='code'><pre><code class='javascript'><span class='line'><span class="c1">// Rocket.js</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="k">this</span><span class="p">.</span><span class="nx">location</span> <span class="o">=</span> <span class="s1">&#39;Cape Canaveral, FL&#39;</span><span class="p">;</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">igniteWhenReady</span> <span class="o">=</span> <span class="kd">function</span><span class="p">(</span><span class="nx">data</span><span class="p">)</span> <span class="p">{</span>
</span><span class='line'>  <span class="k">if</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">isReady</span><span class="p">())</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Launching from &#39;</span> <span class="o">+</span> <span class="k">this</span><span class="p">.</span><span class="nx">location</span><span class="p">);</span>
</span><span class='line'>    <span class="k">this</span><span class="p">.</span><span class="nx">ignite</span><span class="p">(</span><span class="nx">data</span><span class="p">.</span><span class="nx">launchCode</span><span class="p">);</span>
</span><span class='line'>  <span class="p">}</span> <span class="k">else</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Not ready to launch!&#39;</span><span class="p">);</span>
</span><span class='line'>  <span class="p">}</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">invalidLaunchCode</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Failed to get launch code for &#39;</span> <span class="o">+</span> <span class="k">this</span><span class="p">.</span><span class="nx">location</span><span class="p">);</span>
</span><span class='line'>  <span class="k">this</span><span class="p">.</span><span class="nx">scrub</span><span class="p">();</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">launch</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="nx">$</span><span class="p">.</span><span class="nx">ajax</span><span class="p">(</span><span class="s1">&#39;/launch_code&#39;</span><span class="p">,</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">data</span><span class="o">:</span> <span class="p">{</span> <span class="nx">location</span><span class="o">:</span> <span class="k">this</span><span class="p">.</span><span class="nx">location</span> <span class="p">},</span>
</span><span class='line'>    <span class="nx">success</span><span class="o">:</span> <span class="nx">$</span><span class="p">.</span><span class="nx">proxy</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">igniteWhenReady</span><span class="p">,</span> <span class="k">this</span><span class="p">),</span>
</span><span class='line'>    <span class="nx">error</span><span class="o">:</span> <span class="nx">$</span><span class="p">.</span><span class="nx">proxy</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">invalidLaunchCode</span><span class="p">,</span> <span class="k">this</span><span class="p">)</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="c1">//RocketSpec.js</span>
</span><span class='line'>
</span><span class='line'><span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;Rocket&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="kd">var</span> <span class="nx">rocket</span><span class="p">;</span>
</span><span class='line'>  <span class="nx">beforeEach</span><span class="p">(</span><span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">rocket</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">Rocket</span><span class="p">(</span><span class="s1">&#39;Cape Canaveral, FL&#39;</span><span class="p">);</span>
</span><span class='line'>    <span class="nx">spyOn</span><span class="p">(</span><span class="nx">rocket</span><span class="p">,</span> <span class="s1">&#39;ignite&#39;</span><span class="p">);</span>
</span><span class='line'>    <span class="nx">spyOn</span><span class="p">(</span><span class="nx">rocket</span><span class="p">,</span> <span class="s1">&#39;scrub&#39;</span><span class="p">);</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'>
</span><span class='line'>  <span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;#launch&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>    <span class="c1">// we don&#39;t need to test launch() because we&#39;d really just be testing $.ajax</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'>
</span><span class='line'>  <span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;#igniteWhenReady&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;ignites if ready&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">isReady</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="k">return</span> <span class="kc">true</span><span class="p">;</span> <span class="p">};</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">igniteWhenReady</span><span class="p">({</span> <span class="nx">launchCode</span><span class="o">:</span> <span class="mi">12345</span> <span class="p">});</span>
</span><span class='line'>      <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">ignite</span><span class="p">).</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>    <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;does not ignite if not ready&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">isReady</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="k">return</span> <span class="kc">false</span><span class="p">;</span> <span class="p">};</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">igniteWhenReady</span><span class="p">();</span>
</span><span class='line'>      <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">ignite</span><span class="p">).</span><span class="nx">not</span><span class="p">.</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'>  <span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;#invalidLaunchCode&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;scrubs if a bad launch code is given&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">invalidLaunchCode</span><span class="p">();</span>
</span><span class='line'>      <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">scrub</span><span class="p">).</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'><span class="p">});</span>
</span></code></pre></td></tr></table></div></figure>


This is much cleaner and easier to test, but those lingering <code>$.proxy()</code> calls were bugging me. They also made debugging a bit more tedious when having to step through the calls to $.proxy.

My solution: stop fighting with <code>this</code> and create my own event proxy pattern. Testing is now much cleaner.

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
<span class='line-number'>39</span>
<span class='line-number'>40</span>
<span class='line-number'>41</span>
<span class='line-number'>42</span>
<span class='line-number'>43</span>
<span class='line-number'>44</span>
<span class='line-number'>45</span>
<span class='line-number'>46</span>
<span class='line-number'>47</span>
<span class='line-number'>48</span>
<span class='line-number'>49</span>
<span class='line-number'>50</span>
<span class='line-number'>51</span>
<span class='line-number'>52</span>
<span class='line-number'>53</span>
<span class='line-number'>54</span>
<span class='line-number'>55</span>
<span class='line-number'>56</span>
<span class='line-number'>57</span>
<span class='line-number'>58</span>
<span class='line-number'>59</span>
<span class='line-number'>60</span>
<span class='line-number'>61</span>
<span class='line-number'>62</span>
<span class='line-number'>63</span>
<span class='line-number'>64</span>
<span class='line-number'>65</span>
<span class='line-number'>66</span>
<span class='line-number'>67</span>
<span class='line-number'>68</span>
<span class='line-number'>69</span>
<span class='line-number'>70</span>
</pre></td><td class='code'><pre><code class='javascript'><span class='line'><span class="c1">//Rocket.js</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span> <span class="o">=</span> <span class="kd">function</span><span class="p">(</span><span class="nx">location</span><span class="p">)</span> <span class="p">{</span>
</span><span class='line'>  <span class="k">this</span><span class="p">.</span><span class="nx">location</span> <span class="o">=</span> <span class="nx">location</span><span class="p">;</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">ignite</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="cm">/* ... */</span> <span class="p">};</span>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">scrub</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="cm">/* ... */</span> <span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">events</span> <span class="o">=</span> <span class="p">{</span>
</span><span class='line'>  <span class="nx">igniteWhenReady</span><span class="o">:</span> <span class="kd">function</span><span class="p">(</span><span class="nx">rocket</span><span class="p">)</span> <span class="p">{</span>
</span><span class='line'>    <span class="k">return</span> <span class="kd">function</span><span class="p">(</span><span class="nx">data</span><span class="p">)</span>
</span><span class='line'>      <span class="k">if</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">isReady</span><span class="p">())</span> <span class="p">{</span>
</span><span class='line'>        <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Launching from &#39;</span> <span class="o">+</span> <span class="nx">rocket</span><span class="p">.</span><span class="nx">location</span><span class="p">);</span>
</span><span class='line'>        <span class="nx">rocket</span><span class="p">.</span><span class="nx">ignite</span><span class="p">(</span><span class="nx">data</span><span class="p">.</span><span class="nx">launchCode</span><span class="p">);</span>
</span><span class='line'>      <span class="p">}</span> <span class="k">else</span> <span class="p">{</span>
</span><span class='line'>        <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Not ready to launch!&#39;</span><span class="p">);</span>
</span><span class='line'>      <span class="p">}</span>
</span><span class='line'>    <span class="p">};</span>
</span><span class='line'>  <span class="p">},</span>
</span><span class='line'>
</span><span class='line'>  <span class="nx">invalidLaunchCode</span><span class="o">:</span> <span class="kd">function</span><span class="p">(</span><span class="nx">rocket</span><span class="p">)</span> <span class="p">{</span>
</span><span class='line'>    <span class="k">return</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">alert</span><span class="p">(</span><span class="s1">&#39;Failed to get launch code for &#39;</span> <span class="o">+</span> <span class="nx">rocket</span><span class="p">.</span><span class="nx">location</span><span class="p">);</span>
</span><span class='line'>      <span class="nx">rocket</span><span class="p">.</span><span class="nx">scrub</span><span class="p">();</span>
</span><span class='line'>    <span class="p">};</span>
</span><span class='line'>  <span class="p">}</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="nx">Rocket</span><span class="p">.</span><span class="nx">prototype</span><span class="p">.</span><span class="nx">launch</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="nx">$</span><span class="p">.</span><span class="nx">ajax</span><span class="p">(</span><span class="s1">&#39;/launch_code&#39;</span><span class="p">,</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">data</span><span class="o">:</span> <span class="p">{</span> <span class="nx">location</span><span class="o">:</span> <span class="k">this</span><span class="p">.</span><span class="nx">location</span> <span class="p">},</span>
</span><span class='line'>    <span class="nx">success</span><span class="o">:</span> <span class="nx">Rocket</span><span class="p">.</span><span class="nx">events</span><span class="p">.</span><span class="nx">igniteWhenReady</span><span class="p">(</span><span class="k">this</span><span class="p">),</span>
</span><span class='line'>    <span class="nx">error</span><span class="o">:</span> <span class="nx">Rocket</span><span class="p">.</span><span class="nx">events</span><span class="p">.</span><span class="nx">invalidLaunchCode</span><span class="p">(</span><span class="k">this</span><span class="p">)</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'><span class="p">};</span>
</span><span class='line'>
</span><span class='line'><span class="c1">//RocketSpec.js</span>
</span><span class='line'>
</span><span class='line'><span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;Rocket&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>  <span class="kd">var</span> <span class="nx">rocket</span><span class="p">,</span> <span class="nx">igniteWhenReady</span><span class="p">,</span> <span class="nx">invalidLaunchCode</span><span class="p">;</span>
</span><span class='line'>  <span class="nx">beforeEach</span><span class="p">(</span><span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">rocket</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">Rocket</span><span class="p">(</span><span class="s1">&#39;Cape Canaveral, FL&#39;</span><span class="p">);</span>
</span><span class='line'>    <span class="nx">spyOn</span><span class="p">(</span><span class="nx">rocket</span><span class="p">,</span> <span class="s1">&#39;ignite&#39;</span><span class="p">);</span>
</span><span class='line'>    <span class="nx">spyOn</span><span class="p">(</span><span class="nx">rocket</span><span class="p">,</span> <span class="s1">&#39;scrub&#39;</span><span class="p">);</span>
</span><span class='line'>    <span class="nx">igniteWhenReady</span> <span class="o">=</span> <span class="nx">Rocket</span><span class="p">.</span><span class="nx">events</span><span class="p">.</span><span class="nx">igniteWhenReady</span><span class="p">(</span><span class="nx">rocket</span><span class="p">);</span>
</span><span class='line'>    <span class="nx">invalidLaunchCode</span> <span class="o">=</span> <span class="nx">Rocket</span><span class="p">.</span><span class="nx">events</span><span class="p">.</span><span class="nx">invalidLaunchCode</span><span class="p">(</span><span class="nx">rocket</span><span class="p">);</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'>
</span><span class='line'>  <span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;.events&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>    <span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;.igniteWhenReady&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;ignites if ready&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>        <span class="nx">rocket</span><span class="p">.</span><span class="nx">isReady</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="k">return</span> <span class="kc">true</span><span class="p">;</span> <span class="p">};</span>
</span><span class='line'>        <span class="nx">igniteWhenReady</span><span class="p">({</span> <span class="nx">launchCode</span><span class="o">:</span> <span class="mi">12345</span> <span class="p">});</span>
</span><span class='line'>        <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">ignite</span><span class="p">).</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>      <span class="p">});</span>
</span><span class='line'>      <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;does not ignite if not ready&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>        <span class="nx">rocket</span><span class="p">.</span><span class="nx">isReady</span> <span class="o">=</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span> <span class="k">return</span> <span class="kc">false</span><span class="p">;</span> <span class="p">};</span>
</span><span class='line'>        <span class="nx">igniteWhenReady</span><span class="p">();</span>
</span><span class='line'>        <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">ignite</span><span class="p">).</span><span class="nx">not</span><span class="p">.</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>      <span class="p">});</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>    <span class="nx">describe</span><span class="p">(</span><span class="s1">&#39;.invalidLaunchCode&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>      <span class="nx">it</span><span class="p">(</span><span class="s1">&#39;scrubs if a bad launch code is given&#39;</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
</span><span class='line'>        <span class="nx">invalidLaunchCode</span><span class="p">();</span>
</span><span class='line'>        <span class="nx">expect</span><span class="p">(</span><span class="nx">rocket</span><span class="p">.</span><span class="nx">scrub</span><span class="p">).</span><span class="nx">toHaveBeenCalled</span><span class="p">();</span>
</span><span class='line'>      <span class="p">});</span>
</span><span class='line'>    <span class="p">});</span>
</span><span class='line'>  <span class="p">});</span>
</span><span class='line'><span class="p">});</span>
</span></code></pre></td></tr></table></div></figure>


The result is much more readable code, easier debugging (when absolutely necessary), and simpler testing without all those nested closures and AJAX stubs. As an added bonus, you get to keep the <code>this</code> in your event handlers that refers to the event itself.

This experience has led me to believe that a lot of the problems CoffeeScript tries to solve (like <a href="http://jashkenas.github.com/coffee-script/#fat_arrow">function binding</a>) can really just be solved using good, simple JavaScript coding practices. I&#8217;m happy to hear from anyone who has a better pattern or has had similar experiences.


