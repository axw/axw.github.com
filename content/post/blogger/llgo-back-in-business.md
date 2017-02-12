+++
title = "llgo: back in business"
date = 2012-02-11T21:05:00Z
updated = 2012-02-11T21:05:01Z
tags = ["go", "llgo", "llvm", "pushy"]
blogimport = true 
aliases = [

  "/2012/02/llgo-back-in-business.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

I've been hacking away on llgo, on evenings and weekends when I've had the chance. It's now roughly equivalent in functionality to where it was before I upgraded LLVM to trunk (3.1) and broke everything. There's a couple of added bonuses too, like proper arbitrary precision constants, and partial support for untyped constants.<br /><br />Now that the basics are working again, I'll get back to working on the import/export mechanism. I expect this will expose more design flaws, and will take a while. I still plan to make use of debug metadata, which I am not altogether familiar with. I'll also need to decide how the linker and the runtime library are going to work.<br /><br />In other news, I've moved <a href="http://github.com/axw/pushy">Pushy</a>&nbsp;to GitHub. I'm not actively developing it at the moment, but I wanted to consolidate the services I'm consuming. I do have an addition to the Java API in the works: a sort of remote classloader, that will communicate over a Pushy connection to fetch classes/resources. The idea is to make it really quick and easy to run a bit of Java code on a remote machine, without having to deploy the application remotely. I'll hopefully get around to pushing this change within the coming few weeks.
