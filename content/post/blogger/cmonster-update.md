+++
title = "cmonster update"
date = 2011-09-25T14:30:00Z
updated = 2011-09-25T14:39:16Z
blogimport = true 
aliases = [

  "/2011/09/cmonster-update.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

<br />I've been quietly beavering away at <a href="http://github.com/axw/cmonster">cmonster</a>, and thought I should share an update.<br /><br /><b>Note: </b>the changes I describe below are not yet part of a cmonster release. I'll release something once I've stabilised the API and tested it more thoroughly. In the mean time you can pull the source from github.<br /><br />In the last month or so I have been adding C/C++ parsing capabilities to cmonster, by exposing the underlying Clang parser API. I've been wanting a simple, scriptable framework for analysing and manipulating C++ source for a few years now. The reason for wanting such a thing is so that I, and others, can more rapidly develop tools for writing better C++, and eliminate some of the drudgery. I've only just made a start, but cmonster now provides an API for parsing a C/C++ source file, returning a Python object to inspect the result.<br /><br />So what's cmonster looking like now? We now have a preprocessor and a parser interface, the former now being incorporated into the latter. The parser interface will parse a single source file, and return its Abstract Syntax Tree (AST). As is typical with parsers, there are many classes involved to describe each declaration, statement, type, etc. So I've added <a href="http://cython.org/">Cython </a>into the mix to speed up the process of defining Python equivalents for each of these classes.<br /><br />Unfortunately Cython does not yet support <a href="http://www.python.org/dev/peps/pep-0384/">PEP 384</a> (Py_LIMITED_API), so at the moment cmonster is back to requiring the full Python API, and thus must be rebuilt for each new release. I've had a tinker with Cython to get its output to compile with Py_LIMITED_API, and hope to provide a patch in the near future.<br /><br />What's next? Once I get the AST classes mapped out, I intend to introduce a source-to-source translation layer. I'm not entirely sure how this'll work yet, but I think ideally you'd just modify the AST and call some function to rewrite the main source file. Elements of the AST outside of the main source file would be immutable. That's the hope, but it may end up being something a little more crude, using Clang's "Rewriter" interface directly to replace source ranges with some arbitrary string. I expect this will be a ways off yet, though.
