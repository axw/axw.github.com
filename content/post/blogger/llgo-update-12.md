+++
title = "llgo update #12"
date = 2013-03-01T21:50:00Z
updated = 2013-03-01T21:50:31Z
blogimport = true 
aliases = [

  "/2013/03/llgo-update-12.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

Oh my, it's been a while.<br /><br />In my <a href="http://blog.awilkins.id.au/2012/12/go-in-browser-llgo-does-pnacl.html">previous post</a>&nbsp;I wrote about llgo and PNaCl. I haven't had much time to play with PNaCl recently, but I have been prodding llgo along. In February, my wife gave birth to our son, Jeremy, so naturally I've been busy. But anyway, let's talk about what has been happening in llgo. Quick, while he's sleeping!<br /><br />Feature-wise, there's nothing terribly exciting going on. Without getting too boring, what's new is:<br /><br /><ul><li>A new "go1.1" branch in the Git repository. The go1.1 branch aims to make llgo compatible with the Go tip, and will replace the master branch when Go 1.1 is released.</li><li>Removed llgo/types (a fork of the old exp/types package), and moved to go/types.</li><li>Updated runtime type representations to match those from gc's tip (thanks to minux for initiating this effort).</li><li>Updated to use architecture-specific size for "int" (same as uintptr).</li><li><a href="https://github.com/axw/llgo/commit/e26ff1f4a7ea661ff77b12a4cb7273f770d5a928">Changed function representation to be a pair of pointers</a>, to avoid trampolines/runtime code generation for closures. The rationale is the same as for rsc's proposal for Go 1.1; using runtime code generation limits the environments that Go can run in (e.g. PNaCl).</li><li>A slew of bug fixes and minor enhancements.</li></ul><div>The go/types change in particular was not a small one, but llgo came out much better at the end. As of the most recent go/types commits, llgo now passes all of its tests in the go1.1 branch. Now I can get back to implementing features again.</div><div><br /></div><div>That's about all there is to report. It has been <a href="https://github.com/axw/llgo/issues/26#issuecomment-13347764">suggested</a>&nbsp;that I set up some milestones in the GitHub project; I will spend a bit of time coming up with what I think are the bare essentials for a 0.1 release, and what would constitute future releases and so on.</div><div><br /></div><div>One last thing: there's a new(ish) <a href="https://groups.google.com/forum/?fromgroups#!forum/llgo-dev">llgo-dev</a> mailing list. If you want to get involved, or just lurk, come and join the party.</div><div><br /></div><div>Until next time.</div>
