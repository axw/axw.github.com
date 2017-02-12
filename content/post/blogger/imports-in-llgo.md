+++
title = "Imports in llgo"
date = 2011-12-03T12:26:00Z
updated = 2012-01-07T20:23:56Z
blogimport = true 
aliases = [

  "/2011/12/imports-in-llgo.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

It's been a while. I've implemented bits and pieces of the Go language: constants (though not properly handling arbitrary precision numbers at this stage), structs, functions (with and without receivers, as declarations and as literals), and goroutines. Much of the implementation is simplistic, not covering all bases. I want to get something vaguely useful working before I go down the path to completeness.<br /><br />I intended to wait until I had something interesting to show off, but unfortunately I've hit a snag with LLVM.<br /><br />Debug information might sound luxurious, but it's how I'm intending to encode information about exported symbols, so I can implement imports. The <a href="http://golang.org/cmd/gc/">gc compiler</a>&nbsp;creates a header in archives that lists all of this information. LLVM provides a standard set of <a href="http://llvm.org/docs/SourceLevelDebugging.html">metadata for describing debug information</a>, using DWARF descriptors.<br /><br />So I figured I could build on the LLVM metadata, extending it to describe Go-specific information where necessary. The benefit here, aside from reusing an existing well-defined format, is that I'd get DWARF debugging information in my output binaries, something I'd eventually want anyway. Unfortunately it appears that there's no C API for generating named metadata.<br /><br />I'll have a look at extending the LLVM C API now. Down the rabbit hole...<br /><br /><b>Update - 7 January 2012</b><br />I submitted a <span id="goog_2124873712"></span>patch to LLVM to add support for adding named metadata to a module, <a href="http://lists.cs.uiuc.edu/pipermail/llvm-commits/Week-of-Mon-20111219/133770.html">which has been accepted</a><span id="goog_2124873713"></span>. This will, presumably, be part of LLVM 3.1.<br /><br />
