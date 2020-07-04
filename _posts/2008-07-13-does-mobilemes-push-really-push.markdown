---
layout: post
title:  "Does MobileMe's Push Really 'Push?'"
date: 2008-07-13 12:00:00 -0500
categories: 
---

When Steve Jobs first announced &#8220;push email&#8221; for the upcoming iPhone 2.0 software, I was skeptical.  The two protocols I knew of, POP3 and IMAP, operate on a protocol that is always initiated by the client.  The user has to continuously check in with the server to see if new messages have arrived.  With POP3 and IMAP, you can approximate push email by polling server several times a minute.  I figured that Exchange supported some sort of persistent connection that allowed push, but from the way it was presented at Steve&#8217;s keynote, it seemed that they were touting push email for any mail account accessed by an iPhone.  I wrote off &#8220;push email&#8221; as a marketing ploy.  Im my mind, the mail server would have to know where the client was at all times to &#8220;push&#8221; updates.

Well, it turns out that MobileMe supports Push-IMAP, a relatively new protocol.  The way it works is a client logs into the MobileMe server with a long-lived HTTPS connection.  Through this connection, the client sends any updates to the server.  The server sets up a long-lived response to send back notifications of new messages.  Data is compressed to help lower bandwidth requirements.  P-IMAP is an open protocol and a P-IMAP to IMAP bridge can be set up to allow legacy mail servers to provide push services.  In addition to email updates, P-IMAP allows other data, such as contacts and calendars to be synchronized.

What seems surprising to me is that Apple is going to have to set up a system that will have to handle millions (well, at least thousands) of simultaneous connections.  Let&#8217;s hope they can do it!

So, is MobileMe&#8217;s push really &#8220;push?&#8221;  Well, technically, no.  The client still has to initiate and maintain a connection to the server.  If the server kept track of what IP address each iPhone had and initiated connections out to each iPhone, it would be a true push system.  In reality it&#8217;s a more scalable way to handle continuous polling.

More information:

<a href="http://www.ietf.org/proceedings/04mar/slides/lemonade-1/sld1.htm">P-IMAP</a>
