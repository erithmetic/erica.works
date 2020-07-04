---
layout: post
title:  "Headless Chrome"
date: 2017-04-21 14:09:27 -0500
categories: 
---

Google Chrome version 59 [will ship with the headless
option](https://www.chromestatus.com/features/5678767817097216). This means you
can test your web applications using chrome without needing a running window
manager or xvfb.

There is one problem: the latest chromedriver (version 2.29) does not support
versions of Chrome higher than 58.

The solution is to build the latest chromedriver that does support the latest
chrome/chromium. Unfortunately, Google does not make nightly builds of
chromedriver public. You have to download the chromium source and build
chromedriver yourself.

**UPDATE**: It turns out that chromedriver [still relies on XOrg for keycode
decoding](https://bugs.chromium.org/p/chromedriver/issues/detail?id=1772) when
handling `sendKeys` commands from selenium. Thus, you still need xvfb for real
testing to work.

## How all the pieces work together

In my case, I am running the ruby version of cucumber. Cucumber uses the
capybara gem to send commands to selenium-webdriver. Selenium-webdriver in turn
starts up your local copy of chromedriver, which then starts up chrome and
controls the browser through a special debug port.

This means our system has to have a copy of the latest chromium installed, along
with our custom chromedriver build.

To get the latest chromium, I used
[a tool on github](https://github.com/scheib/chromium-latest-linux) that
downloads the latest snapshot compiled for linux.

To get the latest chromedriver, I followed the
[build instructions](https://chromium.googlesource.com/chromium/src/+/master/docs/linux_build_instructions.md)
but ran it all in a docker container in order to maintain a clean building environment. I then
copied the built chromedriver and its shared libraries out of the container.

Once I had the built chrome and chromedriver, I was ready to run
selenium/capybara tests against it.

## Configuring the tests

When configuring capybara, you need to tell selenium-webdriver the path to your
custom chromium binary and send the `--headless` flag, along with other flags
you'll likely need in a CI build node environment.

For running in docker:

```ruby
Capybara.register_driver :headless_chromium do |app|
  caps = Selenium::WebDriver::Remote::Capabilities.chrome(
    "chromeOptions" => {
      'binary' => "/chromium-latest-linux/466395/chrome-linux/chrome",
      'args' => %w{headless no-sandbox disable-gpu}
    }
  )
  driver = Capybara::Selenium::Driver.new(
    app,
    browser: :chrome,
    desired_capabilities: caps
  )
end

Capybara.default_driver = :headless_chromium
```

Now, capybara will drive a headless chromium!

I've [created a repo](https://github.com/dkastner/headless-chromium-selenium)
that lets you test this all out.
