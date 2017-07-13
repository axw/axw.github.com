+++
title = "llgo on ssa"
date = 2014-01-06T22:02:00Z
updated = 2014-01-06T22:02:31Z
blogimport = true 
aliases = [

  "/2014/01/llgo-on-ssa.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

Hello there!<br /><br />I've been busy hacking on <a href="http://github.com/axw/llgo" target="_blank">llgo</a> again. In case you're new here: llgo is a <a href="http://golang.org/" target="_blank">Go</a> frontend for <a href="http://llvm.org/" target="_blank">LLVM</a> that I've been working on for the past ~2 years on and off. It's been quite a while since I last wrote; there has been a bunch of new work since, so I have some things to talk about at last.<br /><br />A few months ago, I started working on rewriting swathes of llgo's internals to base it on <a href="http://godoc.org/code.google.com/p/go.tools/ssa">go.tools/ssa</a>. LLVM uses an <a href="http://en.wikipedia.org/wiki/Static_single_assignment_form">SSA</a> representation, which made the process fairly straightforward. Basing llgo on go.tools/ssa gives me much higher confidence in the quality of the output; it also presented a good opportunity to clean up llgo's source itself, which I have begun, but certainly not finished. llgo is now able to compile all packages in the standard library, except those that require cgo (net, os/user, runtime/cgo).<br /><br />llgo now works something like this:<br /><ol><li>Go source is scanned and parsed by go/ast and go/parser, producing an AST;</li><li>The AST is fed into go/types, type-checking;</li><li>The output of go/types is passed onto go.tools/ssa, which generates the SSA form;</li><li>llgo translates the go.tools/ssa SSA form into an LLVM module,</li><li>llgo-build links the LLVM modules for a program together and translates to an executable.</li></ol><br />go.tools/ssa supports translating a whole program to SSA form, but llgo works in the traditional way: packages are translated one at a time. Whole program optimisation is enabled by linking the LLVM modules together, prior to any translation to machine code.<br /><br />There were a few bits that I stumbled on, when rewriting. Alan Donovan, the author of go.tools/ssa, was kind enough to give me some assistance along the way. Anyway, the main issues I had were:<br /><br /><ul><li>Translating Phi nodes requires a bit of finessing, to ensure processing of the Phi or the edges is not order-sensitive. This was dealt with by generating placeholder values for instructions that haven't yet been visited, and then replacing them later.</li><li>ssa.Index is emitted for indexing into arrays. If an array is in a register, then indexing it means extracting a value; in LLVM, an array element extraction requires a constant index. This is currently kludged by storing to a temporary alloca, and using the getelementptr LLVM instruction. Hopefully I'm missing something and this is easily fixed.</li><li>The Recover block is not dominated by the entry block, so it may not be valid for it to refer to the Alloc instructions for parameters and results. To deal with this, I generate a prologue block that contains the param/result Allocs; the prologue block conditionally jumps to either the recover or entry block, depending on panic/recover control flow. Alan has agreed to do something along these lines in go.tools/ssa.</li><li>The ssa.Next instruction required some assumptions to be made about block ordering and instruction placement, in order to be able to translate string-range using Phi nodes. Recent changes to go.tools/ssa exposed&nbsp;the dominator tree, making it possible to do away with the assumption now.</li></ul><br /><br />Various significant changes have been made during the course of the migration to go.tools/ssa:<br /><ul><li>Interfaces are now represented like in gc: empty interfaces with the runtime type &amp; data, non-empty interfaces with an "itab" and data. Russ Cox wrote <a href="http://research.swtch.com/interfaces">an article about the interface representation</a> back in 2009.</li><li>Panic/recover (and defer, by consequence) are now using setjmp/longjmp. I had been using exceptions, but it was rightly pointed out to me that this wouldn't work unless there were a way of doing non-call exceptions in LLVM (which has not been implemented). The setjmp/longjmp approach incurs a cost for every function that may defer or recover, but it works without modifications to LLVM. Perhaps this will be revisited in the future.</li><li>go/types/typemap is now used for mapping types.Types to runtime type descriptors and LLVM types. Runtime type descriptors are now generated more completely, and more correctly. Identical type descriptors will now be merged at link-time.</li><li>llgo no longer generates conditional branching for calls to non-global functions, when comparing structs, or in map iteration. Apart from producing better code, this makes it much simpler to work with go.tools/ssa, which has its own idea about how the SSA basic blocks relate to one another.</li></ul><div><br />There have also been miscellaneous bug fixes, and improvements, not directly related to the move to go.tools/ssa. Some highlights:</div><ul><li>A custom importer/exporter, thanks to <a href="https://github.com/quarnster">Fredrik Ehnbom</a>. The importer side is disabled at the moment, due to an apparent <a href="https://code.google.com/p/go/issues/detail?id=7028">bug in go.tools/ssa</a>.</li><li>Debugger support, thanks again to Fredrik Ehnbom. I haven't reenabled it since the move to go.tools/ssa. I'll get onto that real soon now, because debugging without it can be tiresome.</li><li>llgo-build can now take a "-test" flag that causes llgo-build to compile the test Go files, yet again thanks to Fredrik Ehnbom. This is currently reliant on the binary importer being enabled, so it won't work out of the box until that bug is fixed.</li><li>Shifts now generate correct values for shifts greater than the width of the lhs operand.</li><li>Signed integer conversions now sign-extend correctly.</li><li>bytes.Compare now works as it should (-1, 0, 1, not &lt;0, 0, &gt;0). "llgo-build -test bytes": PASS</li><li>llgo-build can now take a "-run" flag that causes llgo-build to execute and then dispose of the resulting binary.</li><li>Type strings are propagated to LLVM types, making the IR more legible, thanks to <a href="https://github.com/tmc">Travis Cline</a>.</li></ul><div><br />I <i>think</i> that's everything. I have various things I'd like to tackle now, but not enough time to do it all at once. If you're interested in helping out then there's plenty to do, including:</div><br /><ul><li>Move to using libgo. Ideally the gc runtime would be rewritten in Go already, but that's not going to happen just yet. The compiler and linker are due to be rewritten in Go soon, which is a lot of work as it is.</li><li>Finish off runtime type descriptor generation (notably, type algorithms).</li><li>Get PNaCl support working again. This should be pretty close, but requires the binary importer to be enabled.</li><li>Implement cgo support.</li><li>Implement bounds checking, nil pointer checks, etc.</li><li>Get garbage collection working. There's <a href="https://github.com/axw/llgo/pull/108">Pull Request #108</a>, but this is perpetuating the problem that is llgo's custom runtime. Since GC is fairly invasive, I don't want to go tying llgo to that runtime any more than it is currently. I expect this will have to wait until libgo is integrated.</li><li>Escape analysis. This is a must-have, but not immediately&nbsp;necessarily. The implementation should be based&nbsp;on go.tools/ssa, interfacing with the exporter/importer to record/consume information about external functions.</li><li>Make use of go.tools/ssa/ssautil/Switches. This is an optimisation, so again, not immediately necessary.</li></ul><div><br />If you want to have a play around, then grab LLVM and Go, and then:<br /><br /><ul><li>go get github.com/axw/llgo/cmd/llgo-dist &amp;&amp; llgo-dist</li><li>llgo-build &lt;some/package&gt; <i>or</i> llgo-build file1.go, file2.go, ...</li></ul><div>Let me know how you get on with that.</div><br /><br /></div><div>Here's hoping 2014 can be a productive year for llgo. Happy new year.</div><br /><br />Cheers,<br />Andrew<br /><br />