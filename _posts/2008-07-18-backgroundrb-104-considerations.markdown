---
layout: post
title:  "BackgrounDRb 1.0.4 Considerations"
date: 2008-07-18 12:00:00 -0500
categories: 
---

Today I gleefully updated <a href="http://groups.google.com/group/rubyonrails-talk/browse_thread/thread/913db7da586c09cf">BackgrounDRb to 1.0.4</a> after I learned that a new release was out that supported clustered BDRb servers.  I quickly learned that <strong>regiester_status</strong> has been removed.  That&#8217;s right.  All that code that sends data from your workers to your app need to be refactored to put data into cache[job_key].  This approach is more thread-safe, however.  Perhaps you could even write your own wrapper that puts data into cache on a call to register_status.

All in all, however, there are some really cool new features related to clustering.  I was starting to think for a while that BDRb had been abandoned and that Bj was the new way to go.  This release clearly puts BackgrounDRb on top for spawning asynchronous tasks.
