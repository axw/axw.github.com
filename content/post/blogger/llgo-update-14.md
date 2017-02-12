+++
title = "llgo update #14"
date = 2013-08-16T22:55:00Z
updated = 2013-08-16T22:55:16Z
blogimport = true 
aliases = [

  "/2013/08/llgo-update-14.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

Ahoy there, mateys!<br /><br />It's been three months since our last correspondence. Apologies for the negligence. I've been busy, as usual, but it's more self-inflicted than usual. I've taken up a new role at Canonical, working on <a href="http://juju.ubuntu.com/">Juju</a>.&nbsp;I'm really excited about Juju (both the concept and realisation), and the fact that it's written in Go is icing on the cake. Working remotely is taking some getting used to, but so far it's been pretty swell. Anyway, you didn't come here to read about that, did you?<br /><br />I'm still working on <a href="http://github.com/axw/llgo">llgo</a> in the background, quietly prodding it along towards the <a href="https://github.com/axw/llgo/issues?milestone=1&amp;state=open">0.1 milestone</a>. There's just one big ticket item left, and that's partially done now: channels. I've just finished porting the basics of channels from gc's standard library to llgo's runtime. That doesn't include select, which is entirely missing. When that's done, I'll be content to release 0.1.<br /><br />So what's new since last time?<br /><br /><ul><li>There's a new llgo-build tool, which takes the pain out of building packages and programs with llgo and the LLVM toolchain. Just run "llgo-build &lt;package&gt;", and you'll either build and install a package, or build a program in the working directory. There's no freshness checking, so you're currently required to manually build all dependencies before building a program.</li><li>Simplified building against PNaCl: llgo-dist now accepts a "-pepper" option, which points to a NaCl SDK.</li><li>Implemented support for map literals.</li><li>Implemented complex number arithmetic.</li><li>Implemented channels (apart from anything select-related)</li><li>Numerous bug fixes.</li></ul><div>In my previous post I talked about having implemented panic/recover, and having implemented them in terms of DWARF exception handling. Well, it looks like PNaCl isn't going to support that, at least initially, so a setjmp/longjmp version is likely inevitable now.</div><div><br /></div><div>I also said I would be working on a temporary for of cmd/go. I gave up on that, after hitting a few stumbling blocks. I figured it was more important to actually get the compiler and runtime working than get bogged down in the tooling, hence the simpler llgo-build tool.</div><div><br /></div><div>That's about it! "Feature complete" is getting closer, though lots of things still don't work very nicely. Still no garbage collection, no proper escape analysis, etc. Those will come in time.</div><div><br /></div><div>For now, though... I think I might go catch up on some sleep.</div>
