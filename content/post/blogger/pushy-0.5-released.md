+++
title = "Pushy 0.5 Released"
date = 2011-05-23T19:26:00Z
updated = 2011-05-23T20:20:40Z
blogimport = true 
aliases = [

  "/2011/05/pushy-05-released.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

After just a few short months since the 0.4 release, I am pleased to announce <a href="http://awilkins.id.au/pushy">Pushy </a>0.5. As usual, this release is mostly bug fixes, with a couple of features to make life easier. Instructions on downloading and installing can be found here:<br />&nbsp; &nbsp;&nbsp;<a href="http://awilkins.id.au/pushy/install.php">http://awilkins.id.au/pushy/install.php</a><br /><br />On top of standard bug fixes and features, I have made an effort to beautify some of the code, and speed everything up in general, based on code profiling. This will be evident if your Pushy program performs a lot of proxying: 0.5 performs up to ~2x &nbsp;faster than 0.4 in my tests.<br /><br /><br /><span class="Apple-style-span" style="font-size: large;"><b>New Features</b></span><br /><br />There are two new features in Pushy 0.5: with-statement support, and a simple "execute" interface.<br /><br /><b>With-statement support</b><br />Pushy connections now support the "with" statement. This is functionally equivalent to wrapping a Pushy connection with "contextlib.closing": it will close the connection when the with-statement exits. For example:<br /><br /><pre class="brush: python">with pushy.connect("local:") as con:<br />    ...<br /></pre><br /><b>Compile/execute interface </b><br />Previously if you wanted to execute a statement in the remote interpreter, you would have to first obtain the remote "compile" built-in function, invoke it to compile a remote code object, and then evaluate that with the "connection.eval" method. For example, to execute "print 'Hello, World!'":<br /><br /><pre class="brush: python">code_obj = con.eval("compile")("print 'Hello, World!'", "&lt;filename&gt;", "exec")<br />con.eval(code_obj, locals=..., globals=...)<br /></pre><br />This is a bit unfriendly, so I thought it would be a good idea to add a simpler interface. There are two new methods: "connection.compile" and "connection.execute". The former will compile a remote code object, and the latter executes either a string (statement) or a function. Continuing our very simple example, we get the much simpler:<br /><br /><pre class="brush: python">con.execute("print 'Hello, World!'")<br /></pre><br />Under the hood, this will do exactly what we would previously have had to do manually: remotely compile the statement and then evaluate the resultant code object. Now that suffices for a very simple use case like we have discussed above, but what if we want to execute a series of statements? Wouldn't it be nice if we could remotely execute a locally defined function? Well, now you can, using "connection.compile".<br /><br /><pre class="brush: python">def local_function(*args, **kwargs):<br />    return (args, kwargs)<br />remote_function = con.compile(local_function)<br />remote_function(...)<br /></pre><br />The "connection.compile" method will take a locally defined function and define it in the remote interpreter. It will then return a reference to the remote function, so that we can invoke it as if it were a local function. This allows the user to define complex and/or expensive logic that will be executed entirely remotely, only communicating back to the peer when proxy objects are involved, or to return the result.<br /><br /><br /><b><span class="Apple-style-span" style="font-size: large;">Bug Fixes</span></b><br /><br />The most noteworthy bugs fixed in 0.5 are:<br /><br /><b>#738216 Proxied old-style classes can't be instantiated</b><br />Old-style/classic classes (i.e. those that don't inherit from "object") previously could not be instantiated via a Pushy connection. This has been rectified by defining an additional proxy type for old-style classes.<br /><b><br /></b><br /><b>#733026 auto-importer doesn't support modules not imported by their parent</b><br />Previously, importing a remote submodule via "connection.modules" would only work if the parent of the submodule imported it. For example, "connection.modules.os.path" would work since os imports os.path. If the parent does not import the submodule, Pushy would fail to import the module. This has been fixed in 0.5; remotely imported modules will provide the same sort of automagical importing interface as "connection.modules".<br /><b><br /></b><br /><b>#734311 Proxies are strongly referenced and never deleted; __del__ isn't transmitted to delete proxied object</b><br />This is the most significant change in Pushy 0.5. Since inception, Pushy has never released proxy objects, meaning eventual memory bloating for objects that would otherwise by garbage collected. As of 0.5, Pushy will now garbage collect proxies by default. Every so often (by default, 5 seconds), Pushy will send a message to the peer to let it know which proxies have been garbage collected. This allows the peer to release the proxied objects, and reclaim resources.<br /><b><br /></b><br /><b>#773811 Daemon/socket transport is slow</b><br />Due to the many small messages sent back and forth by Pushy, the "daemon" transport was found to be quite slow in stress testing. This is due to&nbsp;<a href="http://en.wikipedia.org/wiki/Nagle's_algorithm">Nagle's Algorithm</a>, which delays sending network packets so that they can be combined. This has a considerable impact on the latency of Pushy's operations, and so it is now disabled by Pushy.<br /><br />Enjoy!