+++
title = "llgo on Go 1.1"
date = 2013-05-18T21:28:00Z
updated = 2013-05-19T23:07:17Z
blogimport = true 
aliases = [

  "/2013/05/llgo-on-go-11.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

Hi folks,<br /><br />(For those of you coming from HN/Twitter/elsewhere, this is a post about <a href="https://github.com/axw/llgo">llgo</a>. llgo is an LLVM frontend for the Go programming language).<br /><br />In my <a href="http://blog.awilkins.id.au/2013/03/llgo-update-12.html">last post</a> I mentioned that work had began on moving to Go 1.1 compatibility; this has been my primary focus since then. Since Go 1.1 is now released (woohoo!), I've gone ahead and pulled all the changes back into the master branch on GitHub. If you want to play around, you can do the following:<br /><br /><ol><li><a href="http://golang.org/doc/install">Get Go 1.1</a>.</li><li><a href="http://llvm.org/releases/">Get Clang and LLVM</a> (I've tested with 3.2, Ubuntu x86-64). Make sure llvm-config is in your $PATH.</li><li>Run "go get github.com/axw/llgo/cmd/llgo-dist"</li><li>Run "llgo-dist". This will install llgo into $GOBIN, and build the runtime.</li></ol><div><br /></div><br />The biggest new feature would have to be: defer, panic and recover (I'm lumping them together as they're closely related). I've implemented them on top of <a href="http://llvm.org/docs/ExceptionHandling.html">LLVM's exception handling support</a>. The panic and recover functions are currently tied to DWARF exception handling, though it's simple enough that it should be feasible to use setjmp/longjmp on platforms where DWARF exception handling isn't viable.<br /><br />Aside from that, there's some new bits and bobs:<br /><br /><ul><li>Method sets are handled properly now (or at least not completely wrong like before). This means you can use a embedded types' methods to satisfy an interface.</li><li>"return" requirements are now checked by go/types</li><li>cap() is now implemented for slices.</li><li>llgo-dist now builds against the LLVM static libraries (if available) by default now, with an option for building against the shared libraries.</li></ul><div><br /></div><div>I'll be working on a temporary fork of cmd/go to build programs with llgo, while a long-term solution is figured out. I'd also like to get PNaCl integration working again, given that its release is nigh.</div><div><br /></div><div>That's all for now.</div><div><br /></div>
