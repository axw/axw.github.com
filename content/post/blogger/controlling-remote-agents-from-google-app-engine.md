+++
title = "Controlling Remote Agents from Google App Engine"
date = 2011-07-17T12:57:00Z
updated = 2011-07-17T12:57:48Z
blogimport = true 
aliases = [

  "/2011/07/controlling-remote-agents-from-google.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

A month or so ago I was brainstorming ideas related to <a href="http://code.google.com/appengine/">Google App Engine</a> (GAE), as I had been wanting a reason to play with it for a while. One idea that stuck was connecting a remote Python process to GAE via <a href="http://awilkins.id.au/pushy">Pushy</a>, so we could either control GAE or GAE could control the remote process. I'm still working on the C/Python Preprocessor thingy, but I took a break from that this weekend to look into the possibility of a GAE Pushy transport.<br /><br />So yesterday morning I signed up for an account, and started tinkering. I had been reading the docs already, and I figured there were a few possible approaches:<br /><br /><ul><li>The obvious: use HTTP. This has one major drawback in that it is inherently synchronous and wholly driven by the client. Moreover, GAE only allows request handlers around 30s to complete, so no kind of fancy long-polling is possible here.</li><li>Channel API. This sounds like the right kind of thing to use, but it's aimed at interacting with Javascript in a webpage.</li><li>XMPP. Huh? Isn't that for instant messaging? Exactly. The client (remote Python process) and server (GAE) are peers in XMPP, and either one can initiate sending messages to the other. Let's look into this a bit more...</li></ul><div>So I did a quick search for Python XMPP libraries, and a few came up. I settled on <a href="http://xmpppy.sourceforge.net/">xmpppy</a>, but to be honest I didn't find any of them particularly compelling. The APIs are a bit clunky. Anyway, the approach I took was to have an XMPP handler in my GAE application create a persistent Pushy connection object associated with a pair of read/write files that wrapped the XMPP API. When an XMPP message came in, the application would extract the Pushy request from base-64 encoded body of the message, and return the result in a similar manner.</div><div><br /></div><div>And it worked, but only just. I had to make a few hacks to Pushy to get all of this work. There were some oddities I had to work around, such as the "eval" built-in in GAE's Python not taking any keyword arguments. Unfortunately, I don't think this particular transport is very useful in the flaky state it's in at the moment. Also, it's not terribly valuable to build a transport to control a GAE application, since other APIs&nbsp;exist for that purpose&nbsp;(<a href="http://code.google.com/appengine/docs/python/tools/protorpc/">ProtoRPC</a>, <a href="http://code.google.com/appengine/articles/remote_api.html">remote_api</a>). More useful would be to have the GAE application control the remote process without the need for polling. I'll be looking into this further.</div>
