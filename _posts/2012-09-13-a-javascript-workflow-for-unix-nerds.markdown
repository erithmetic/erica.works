---
layout: post
title:  "Back to the Browser - a JavaScript Workflow for UNIX Nerds"
date: 2012-09-13 12:00:00 -0500
categories: 
---

When Apple announced Mac OS X Lion, their tagline was &#8220;Back to the Mac&#8221; as they were bringing some features from iOS into the desktop-oriented Mac OS. In the JavaScript world, a similar thing has happened: innovations in the <a href="http://nodejs.org/">Node.js</a> space can be brought back to the browser. These innovations have made JavaScript development faster and cleaner with command-line tools and the npm packaging system.

As I began writing serious JavaScript libraries and apps, I wanted the same kind of workflow I enjoy when writing Ruby code. I wanted to write my code in vi, run tests in the command line, organize my code into classes and modules, and use versioned packages similar to Ruby gems. At the time, the standard way to write JavaScript was to manage separate files by hand and concatenate them into a single file. One of the only testing frameworks in town was Jasmine, which required you to run tests in the browser. Since then, there has been an explosion of command-line code packaging and testing frameworks in the Node.js community that have lent themselves well to client side development. What follows is the approach I find to be the most productive.

Here&#8217;s a list of the tools that correspond to their Ruby world counterparts:

<table>
<thead>
<tr>
<th></th>
<th> Application          </th>
<th> Ruby                            </th>
<th> Javascript                                               </th>
</tr>
</thead>
<tbody>
<tr>
<td></td>
<td> Testing              </td>
<td> <a href="http://rspec.info">RSpec</a>      </td>
<td> <a href="http://vowsjs.org">vows</a>, <a href="http://busterjs.org">buster</a> |</td>
</tr>
<tr>
<td></td>
<td> Package management   </td>
<td> <a href="http://rubygems.org">rubygems</a>, <a href="http://gembundler.com">bundler</a> </td>
<td> <a href="http://npmjs.org">npm</a>, <a href="http://github.com/substack/node-browserify">browserify</a> |</td>
</tr>
<tr>
<td></td>
<td> Code organization </td>
<td> <code>require</code>                         </td>
<td> <a href="http://commonjs.org">CommonJS</a> |</td>
</tr>
<tr>
<td></td>
<td> Build tools  </td>
<td> jeweler, rubygems </td>
<td> <a href="http://github.com/substack/node-browserify">browserify</a> |</td>
</tr>
</tbody>
</table>


By installing Node.js, you have access to a command-line JavaScript runtime, testing, package management, and application building. Running tests from the command-line allows you to more easily use tools like <a href="http://rubydoc.info/gems/guard/frames">guard</a>, run focused unit tests, and easily set up continuous integration.

<h3>Testing</h3>

Many JavaScripters run Jasmine in the browser for testing. While it does the job, its syntax is extremely verbose and it breaks the command-line-only workflow. There is a Node.js package for running Jasmine from the command line, but I have found it to be buggy and not as feature rich as a typical command line testing tool. Instead I prefer vows or buster.js. Each supports a simpler &#8220;hash&#8221; based syntax, as opposed to Jasmine&#8217;s verbose syntax:

<div>
  <pre>
    <code class='javascript'>// Jasmine

describe('MyClass', function() {
  describe('#myMethod', function() {
    before(function() {
      this.instance = new MyClass('foo');
    });
  
    it('returns true by default', function() {
      expect(this.instance.myMethod()).toBeTruthy();
    });
    it('returns false sometimes', function() {
      expect(this.instance.myMethod(1)).toBeFalsy();
    });
  });
});</code>
  </pre>
</div>




<div>
  <pre>
    <code class='javascript'>// Vows

vows.describe('MyClass').addBatch({
  '#myMethod': {
    topic: new MyClass('foo'),

    'returns true by default': function(instance) {
      assert(instance.myMethod());
    },
    'returns false sometimes': function(instance) {
      refute(instance.myMethod(1));
    }
  }
}).export(module);</code>
  </pre>
</div>


Vows and buster can be used just like <code>rspec</code> to run tests from the command line:

<pre><code>&gt; vows test/my-class-test.js
................
OK &gt;&gt; 22 honored
</code></pre>

One advantage that buster has over vows is that it can run its tests both from the command line and from a browser in case you want to run some integration tests in a real browser environment.

For mocks and stubs, you can use the excellent <a href="http://sinonjs.org">sinon</a> library, which is included by default with buster.js.

<h4>Integration testing</h4>

In addition to unit testing, it&#8217;s always good run a full integration test. Since every browser has its own quirks, it&#8217;s best to run integration tests in each browser. I write <a href="http://cukes.info">cucumber</a> tests using <a href="http://jnicklas.github.com/capybara/">capybara</a> to automatically drive either a &#8220;headless&#8221; (in-memory) webkit browser with <a href="https://github.com/thoughtbot/capybara-webkit">capybara-webkit</a> and/or GUI browsers like Firefox and Chrome with <a href="http://seleniumhq.org/">selenium</a>.

In <code>features/support/env.rb</code> you can define which type of browser is used to run the tests by defining custom drivers

<div>
  <pre>
    <code class='ruby'>require 'selenium-webdriver'

    Capybara.register_driver :selenium_chrome do |app|
      Capybara::Selenium::Driver.new app, :browser =&gt; :chrome
    end

    Capybara.register_driver :selenium_firefox do |app|
      Capybara::Selenium::Driver.new app, :browser =&gt; :firefox
    end

    if ENV['BROWSER'] == 'chrome'
      Capybara.current_driver = :selenium_chrome
    elsif ENV['BROWSER'] == 'firefox'
      Capybara.current_driver = :selenium_firefox
    else
      require 'capybara-webkit'
      Capybara.default_driver = :webkit
    end</code>
  </pre>
</div>


Now you can choose your browser with an environment variable: <code>BROWSER=firefox cucumber features</code>

If you are testing an app apart from a framework like Sinatra or Rails, you can use Rack to serve a static page that includes your built app in a <code>&lt;script&gt;</code> tag. For example, you could have an html directory with an <code>index.html</code> file in it:

<div>
  <pre>
    <code class='html'>&lt;html&gt;
  &lt;head&gt;
    &lt;title&gt;Test App&lt;/title&gt;
    &lt;script type=&quot;text/javascript&quot; src=&quot;application.js&quot;&gt;&lt;/script&gt;
  &lt;/head&gt;
  &lt;body&gt;&lt;div id=&quot;app&quot;&gt;&lt;/div&gt;&lt;/body&gt;
&lt;/html&gt;</code>
  </pre>
</div>


When you&#8217;re ready to run an integration test, compile your code into <code>application.js</code> using browserify:

<pre><code>&gt; browserify -e lib/main.js -o html/application.js
</code></pre>

Then tell cucumber to load your test file as the web app to test:

<div>
  <pre>
    <code class='ruby'># features/support/env.rb
    
    require 'rack'
    require 'rack/directory'

    Capybara.app = Rack::Builder.new do
      run Rack::Directory.new(File.expand_path('../../../html/', __FILE__))
    end</code>
  </pre>
</div>


Once cucumber is set up, you can start writing integration tests just as you would with Rails:

<pre><code># features/logging_in.feature

Feature: Logging in

Scenario: Successful in-log
  Given I am on the home page
  When I log in as Erica
  Then I should see a welcome message
</code></pre>

<div>
  <pre>
    <code class='ruby'># features/step_definitions/log_in_steps.rb

    Given %r{I am on the home page} do
      visit '/index.html'
    end
    
    When %r{I log in as Erica} do
      click '#login'
      fill_in 'username', :with =&gt; 'Erica'
      fill_in 'password', :with =&gt; 'secret'
      click 'input[type=submit]'
    end
    
    Then %r{I should see a welcome message} do
      page.should =~ /Welcome, Erica!/
    end</code>
  </pre>
</div>


<h3>Package management</h3>

One of the joys of Ruby is its package manager, rubygems. With a simple <code>gem install</code> you can add a library to your app. There has been an explosion of JavaScript package managers lately. Each one adds the basic ability to gather all of your libraries and application code, resolve the dependencies, and concatenate them into a single application file. I prefer <a href="http://github.com/substack/node-browserify">browserify</a> over all the others for two reasons. First, you can use any Node.js package, which opens you up to many more utilities and libraries than other managers. Second, it uses Node.js&#8217; CommonJS module system, which is a very simple and elegant module system.

In your project&#8217;s root, place a <code>package.json</code> file that defines the project&#8217;s dependencies:

<div>
  <pre>
    <code class='javascript'>{
      &quot;dependencies&quot;: {
        &quot;JSONPath&quot;: &quot;0.4.2&quot;,
        &quot;underscore&quot;: &quot;*&quot;,
        &quot;jquery&quot;: &quot;1.8.1&quot;
      },
      &quot;devDependencies&quot;: {
        &quot;browserify&quot;: &quot;*&quot;,
        &quot;vows&quot;: &quot;*&quot;
      }
    }</code>
  </pre>
</div>


Run <code>npm install</code> and all of your project&#8217;s dependencies will be installed into the <code>node_modules</code> directory. In your project you can then make use of these packages:

<div>
  <pre>
    <code class='javascript'>var _ = require('underscore'),
        jsonpath = require('JSONPath'),
        myJson = &quot;...&quot;;

    _.each(jsonpath(myJson, '$.books'), function(book) {
      console.log(book);
    });</code>
  </pre>
</div>


If you&#8217;re looking for packages available for certain tasks, simply run <code>npm search &lt;whatever&gt;</code> to find pacakges related to your search terms. Some packages are tagged with &#8220;browser&#8221; if they are specifically meant for client side apps, so you can include &#8220;browser&#8221; as one of your search terms to limit your results accordingly. Many of the old standbys, like jquery, backbone, spine, and handlebars are there.

<h3>Code organization</h3>

As JavaScript applications get more complex, it becomes prudent to split your code into separate modules, usually placed in separate files. In the Ruby world, this was easily done by <code>require</code>-ing each file. Node.js introduced many people (including me) to the CommonJS module system. It&#8217;s a simple and elegant way to modularize your code and allows you to separate each module into its own file. Browserify allows you to write your code in the CommonJS style and it will roll all of your code up into a single file appropriate for the browser.

<h4>Ruby structure</h4>

For example, my Ruby project may look like:

<pre><code>~lib/
 -my_library.rb
 -my_library/
   -book.rb
-my_library.gemspec
-spec/
 -my_library/
   -book_spec.rb
</code></pre>

Where <code>lib/my_library.rb</code> looks like:

<div>
  <pre>
    <code class='ruby'>require 'my_library/book'

    class MyLibrary
      def initialize(foo)
        @book = Book.parse(foo)
      end
    end</code>
  </pre>
</div>


And <code>lib/my_library/book.rb</code> looks like:

<div>
  <pre>
    <code class='ruby'>require 'jsonpath'

class MyLibrary
  class Book
    def self.parse(foo)
      JSONPath.eval(foo, '$.store.book\[0\]')
    end
  end
end</code>
  </pre>
</div>


And <code>spec/my_library/book_spec.rb</code> looks like:

<div>
  <pre>
    <code class='ruby'>require 'json'
    require 'helper'
    require 'my_library/book'

    describe MyLibrary::Book do
      describe '.parse' do
        it 'parses a book object' do
          json = File.read('support/book.json')
          book = Book.parse(JSON.parse(json))
          book.title.should == &quot;Breakfast at Tiffany's&quot;
        end
      end
    end</code>
  </pre>
</div>


<h4>JavaScript structure</h4>

A javascript project would look similar:

<pre><code>~lib/
 -my-library.js
 -my-library/
   -book.js
-package.json
-test/
 -my-library/
   -book-test.js
</code></pre>

Where <code>lib/my-library.js</code> looks like:

<div>
  <pre>
    <code class='javascript'>var Book = require('./my-library/book');

var MyLibrary = function(foo) {
  this.book = new Book(foo);
};

module.exports = MyLibrary;</code>
  </pre>
</div>


And <code>lib/my-library/book.js</code> looks like:

<div>
  <pre>
    <code class='javascript'>var jsonpath = require('jsonpath');

var Book = {
  parse: function(foo) {
    return jsonpath(foo, '$.store.book\[0\]');
  }
};

module.exports = Book;</code>
  </pre>
</div>


And <code>test/my-library/book-test.js</code> looks like:

<div>
  <pre>
    <code class='javascript'>var fs = require('fs');
var helper = require('../helper'),
    Book = require('../../lib/my_library/book');
    // NOTE: there are ways to set up your modules 
    // to be able to use relative require()s but
    // it is beyond the scope of this article

vows.describe('Book').addBatch({
  '.parse': {
    'parses a book object': function() {
      var json = fs.readFileSync('support/book.json'),
          book = Book.parse(JSON.parse(json));
      assert.equal(book.title, &quot;Breakfast at Tiffany's&quot;);
    }
  }
}).export(module);</code>
  </pre>
</div>


<h3>Build tools</h3>

Browserify will build concatenated JavaScript files when you&#8217;re ready to deploy your code on a website or as a general-purpose library. Its usage is simple:

<pre><code>&gt; browserify -e &lt;main_application_startup_code&gt; -o &lt;path_to_built_file&gt;
</code></pre>

<h4>Building a library</h4>

If we were building the library in the section above, we could run <code>browserify -e lib/my-library.js -o build/my-library.js</code>. Then, any user of your library can use your library with the <code>require</code> function:

<div>
  <pre>
    <code class='html'>&lt;script type=&quot;text/javascript&quot; src=&quot;jquery.js&quot;&gt;&lt;/script&gt;
    &lt;script type=&quot;text/javascript&quot; src=&quot;my-library.js&quot;&gt;&lt;/script&gt;
    &lt;script type=&quot;text/javascript&quot;&gt;
      var myLibrary = require('my-library');
      $.ajax('/lib.json', function(data) {
        console.log(myLibrary(data));
      });
    &lt;/script&gt;</code>
  </pre>
</div>


You can also save the library user some time with a custom entry point for browsers:

<div>
  <pre>
    <code class='javascript'>// in /browser.js
    window.MyLibrary = require('my-library');</code>
  </pre>
</div>


Then run `browserify -e browser.js -o build/my-library.js

And the library user would use it thusly:

<div>
  <pre>
    <code class='html'>&lt;script type=&quot;text/javascript&quot; src=&quot;jquery.js&quot;&gt;&lt;/script&gt;
    &lt;script type=&quot;text/javascript&quot; src=&quot;my-library.js&quot;&gt;&lt;/script&gt;
    &lt;script type=&quot;text/javascript&quot;&gt;
      $.ajax('/lib.json', function(data) {
        console.log(MyLibrary(data));
      });
    &lt;/script&gt;</code>
  </pre>
</div>


<h4>Building a web app</h4>

A spine app might look something like:

<div>
  <pre>
    <code class='javascript'>// in app/main.js
    
    var $ = require('jquery'),
        Spine = require('spine');

    Spine.$ = $;

    var MainController = require('./controllers/main-controller');

    var ApplicationController = Spine.Controller.sub({
      init: function() {
        var main = new MainController();
        this.routes({
          '/': function() { main.active(); }
        });
      }
    });

    Spine.Route.setup({ history: true });</code>
  </pre>
</div>


It would be built with <code>browserify -e app/main.js -o build/application.js</code> and the application.js added to your website with a <code>&lt;script&gt;</code> tag.

You can extend browserify with plugins like <a href="https://github.com/mklabs/templatify#readme">templatify</a>, which precompiles HTML/Handlebar templates into your app.

Together, npm packages, command-line testing and build tools, and modular code organization help you quickly build non-trivial JavaScript libraries and applications just as easily as it was in Ruby land. I&#8217;ve developed several in-production projects using this workflow, such as our <a href="http://github.com/dkastner/CM1.js">CM1</a> JavaScript client library, our flight search <a href="http://github.com/brighterplanet/careplane">browser plugin</a>, and <a href="http://github.com/brighterplanet/hootroot1">hootroot.com</a>.

<!-- more end -->

</div>


  <footer>
    <p class="meta">
      
  


      








  


<time datetime="2012-09-13T00:00:00-05:00" pubdate data-updated="true">Sep 13<span>th</span>, 2012</time>
      

<span class="categories">
  
    <a class='category' href='/blog/categories/technology/'>technology</a>
  
</span>


    
    
      <div class="sharing">
  
  
  
</div>

    
    <p class="meta">
      
        <a class="basic-alignment left" href="/blog/2012/08/27/graphite-beyond-the-basics/" title="Previous Post: Graphite and statsd – beyond the basics">&laquo; Graphite and statsd – beyond the basics</a>
