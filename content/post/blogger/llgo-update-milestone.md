+++
title = "llgo update, milestone"
date = 2012-09-09T18:26:00Z
updated = 2012-09-09T18:26:54Z
blogimport = true 
aliases = [

  "/2012/09/llgo-update-milestone.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

In between gallivanting in <a href="http://en.wikipedia.org/wiki/Sydney">Sydney</a>, working, and organising to have a new house built, I've squeezed in a little bit of work on llgo. If you've been following along on Github, you'll have seen that things have progressed a bit since last time I wrote.<br /><br />Aside from a slew of bug fixes and trivialities, llgo now implements:<br /><br /><ul><li>Slice operations (make, append, slice expressions). I've only implemented single-element appends so far, i.e. No <i>append(s, a, b, c, ...)</i> or <i>(s, abc...)</i> yet.</li><li>Named results in functions.</li><li>Maps - creation, indexing, assignment, and deletion. The underlying implementation is just a dumb linked-list at this point in time. I'll implement it as a hash map in the future, when there aren't more important things to implement.</li><li>Range statements for arrays, slices and maps. I haven't done strings yet, simply because it requires a bit more thought into iterating through strings runes-at-a-time. I don't expect it'll be too much work.</li><li>Branch statements, except for goto. You can now break, continue, and fallthrough.</li><li>String indexing, and slicing.</li><li>Function literals. Once upon a time these were working, but they haven't been for a while. Now they are again. Note that this does not include support for closures at this stage, so usefulness is pretty limited.</li></ul><br />Early on in the development of llgo, I decided that rather than implementing the compiler by going through the specification one item at a time, I'd drive the development by attempting to compile a real program. For this, I chose <a href="https://code.google.com/p/go/source/browse/src/pkg/unicode/maketables.go">maketables</a>, a program from the <i>unicode</i>&nbsp;standard library package. As of today, llgo can successfully compile the program. That is, it compiles that specific&nbsp;<i>file,</i> maketables.go. It doesn't yet compile all of its dependencies, and it certainly doesn't link or produce a usable program.<br /><br />So now I'll be working towards getting all of the dependencies compiling, then linking. In the interest of seeing usable progress, I think I might now take a bottom-up approach and start focusing on the core libraries, like <i>runtime</i> and <i>syscall</i>. I'll report back when I have something interesting to say.<br /><br />
