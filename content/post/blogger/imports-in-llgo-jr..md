+++
title = "Imports in llgo, jr."
date = 2012-02-19T13:50:00Z
updated = 2012-02-19T13:50:46Z
tags = ["go", "llgo", "gollvm", "llvm"]
blogimport = true 
aliases = [

  "/2012/02/imports-in-llgo-jr.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

So I realised I'm a doofus the other day, when I started getting closer to completion on producing export metadata in llgo. Rolling my own import mechanism is unnecessary for now. Instead, I can just lean on the import mechanism that exists in the standard library (well, <a href="http://weekly.golang.org/doc/go1.html#exp">until Go 1</a> at least): go/types/GcImporter.<br /><br />I've modified llgo to use go/ast/NewPackage, rather than the old code I had that was using go/parser/ParseFiles. The NewPackage function takes an optional "importer" object which will be used for inter-package dependency resolution, whereas ParseFiles does no resolution. The standard GcImporter type may be used to identify exports by interrogating the object and archive files in $GOROOT. The AST that's generated is filled in with external declarations, so it's then up to llgo to convert those into LLVM external declarations. Easy peasy.<br /><br />Now it's time to come up with a symbol naming scheme. Without having thought about it too hard, I'm going to start off with the assumption that the absolute name of the symbol (package+name), with slashes converted to dots, will do the trick. Once I've implemented that, I'll need to start work on the runtime in earnest. It's also high time I put some automated tests in place, since things are starting to get a little stabler.<br /><br />In the long term I'll probably want to continue on with my original plan, which is to generate module-level metadata in the LLVM bitcode, and then extract this in a custom importer. It should be quite straightforward.&nbsp;Earlier this week I wrapped up some updates to gollvm to add an API to make generating <a href="http://llvm.org/docs/SourceLevelDebugging.html">source-level debugging metadata</a> simpler. This will be useful not only for describing exports, but also for what it's intended: generating <a href="http://dwarfstd.org/">DWARF</a>&nbsp;debug information.<br /><br />In other news: my wife just ordered the 1-4a box set of&nbsp;<a href="http://www-cs-staff.stanford.edu/~uno/taocp.html">The Art of Computer Programming</a>&nbsp;for me. At the moment I am slowly making way through&nbsp;<a href="http://en.wikipedia.org/wiki/G%C3%B6del,_Escher,_Bach">Gödel, Escher, Bach: an Eternal Golden Braid</a>, and so far, so good. Looking forward to more light reading for the bus/train!
