+++
title = "cmonster 0.2 released"
date = 2012-01-07T21:20:00Z
updated = 2012-01-07T21:20:12Z
blogimport = true 
aliases = [

  "/2012/01/cmonster-02-released.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

Last week I announced a new version of cmonster (now version 0.2) <a href="http://lists.cs.uiuc.edu/pipermail/cfe-dev/2012-January/019362.html">on the Clang mailing list</a>.&nbsp;I've finally updated the <a href="https://github.com/axw/cmonster/">Github page for cmonster</a> with some basic examples, and installation instructions.<br /><br />I&nbsp;asked on the Clang mailing list for some feedback, but so far all I'm hearing is crickets. I'm surprised that nobody's interested enough to reply, but I'll freely admit that I'm not particularly good at marketing. If you do check it out, let me know what I can do to make it useful for you.<br /><br />In other news: I haven't spent much time on llgo recently, what with Real Life happening all the time. I sent a patch into LLVM to add improved support for named metadata, which was accepted. I also made a bunch of fixes to gollvm so that it builds and works with LLVM 3.0 (and some additional changes to work with trunk).<br /><br />There's been some changes to LLVM that mean I can no longer abuse the metadata system by attaching metadata to values. Metadata can now be specified only on instructions. This means that I can no longer attach type information to values using metadata, nor identify function receivers in a similar way. So llgo will need to maintain type and receiver (amongst other) information outside of LLVM's values. This was always going to be necessary, I'd just been putting it off to get something that worked.<br /><br />I hope to get back to making inroad with llgo soon. I feel like I made pretty good progress on implementing my crazy ideas in 2011. Here's hoping 2012 works out as well.
