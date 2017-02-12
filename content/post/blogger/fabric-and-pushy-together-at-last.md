+++
title = "Fabric and Pushy, together at last"
date = 2011-03-09T21:47:00Z
updated = 2011-03-09T22:06:46Z
blogimport = true 
aliases = [

  "/2011/03/fabric-and-pushy-together-at-last.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

A workmate of mine recently brought <a href="http://fabfile.org/">Fabric </a>to my attention. If you're not familiar with Fabric, it is a command-line utility for doing sys-admin type things over SSH. There's an obvious overlap with <a href="http://awilkins.id.au/pushy/">Pushy</a> there, so I immediately thought, "Could Pushy be used to back Fabric? What are the benefits, what are the downsides?"<div><br /></div><div>A couple of fairly significant benefits:<br /><div><ul><li>Pushy does more than just SSH, so Fabric could conceivably be made to support additional transports by using Pushy (which uses Paramiko), rather than using Paramiko directly.</li><li>Pushy provides access to the entire Python standard library, which is largely platform independent. So you could do things like determine the operating system name without using "uname", which is a *NIX thing. That's a trivialisation, but you get the idea I'm sure.</li></ul>One big fat con:<br /><ul><li>Pushy absolutely requires Python on the remote system (as well as SSH, of course, but Fabric requires that anyway.) So requiring Pushy would mean that Fabric would be restricted to working only with remote machines that have Python installed. Probably a safe bet in general, but not ideal.</li></ul><div>How about using Pushy if Python is available, and just failing gracefully if it doesn't? This turns out to be really easy to do, since Fabric and Pushy both use Paramiko. So I wrote a Fabric "<a href="http://docs.fabfile.org/en/1.0.0/api/core/operations.html">operation</a>" to import a remote Python module. Under the covers, this piggy-backs a Pushy connection over the existing Paramiko connection created by Fabric. I'll bring this to the attention of the Fabric developers, but I thought I'd just paste it here for now.</div></div></div><br /><div>First an example of how one might use the "remote_import" operation. Pass it a module name, and you'll get back a reference to the remote module. You can then use the module as you would use the module as if you had done a plain old "import <module>".<br /></div><br /><div><h3>fabfile.py</h3><pre class="brush: python">from fabric_pushy import remote_import<br /><br />def get_platform():<br />    platform = remote_import("platform")<br />    print platform.platform()<br /></pre></div><br /><div>You just execute your fabfile as per usual, and the "remote_import" operation will create a Pushy connection to each host, import the remote Python interpreter's standard <a href="http://docs.python.org/library/platform.html">platform</a> module, and call its platform method to determine its platform name. Easy like Sunday morning...<br /><pre>    $ fab -H localhost get_platform<br />    [localhost] Executing task 'get_platform'<br />    Linux-2.6.35-27-generic-i686-with-Ubuntu-10.10-maverick<br /><br />    Done.<br />    Disconnecting from localhost... done.<br /></pre></div><br /><div><h3>fabric_pushy.py</h3><pre class="brush: python">from fabric.state import env, default_channel<br />from fabric.network import needs_host<br /><br />import pushy<br />import pushy.transport<br />from pushy.transport.ssh import WrappedChannelFile<br /><br />class FabricPopen(pushy.transport.BaseTransport):<br />    """<br />    Pushy transport for Fabric, piggy-backing the Paramiko SSH connection<br />    managed by Fabric.<br />    """<br /><br />    def __init__(self, command, address):<br />        pushy.transport.BaseTransport.__init__(self, address)<br /><br />        # Join arguments into a string<br />        args = command<br />        for i in range(len(args)):<br />            if " " in args[i]:<br />                args[i] = "'%s'" % args[i]<br />        command = " ".join(args)<br /><br />        self.__channel = default_channel()<br />        self.__channel.exec_command(command)<br />        self.stdin  = WrappedChannelFile(self.__channel.makefile("wb"), 1)<br />        self.stdout = WrappedChannelFile(self.__channel.makefile("rb"), 0)<br />        self.stderr = self.__channel.makefile_stderr("rb")<br /><br />    def __del__(self):<br />        self.close()<br /><br />    def close(self):<br />        if hasattr(self, "stdin"):<br />            self.stdin.close()<br />            self.stdout.close()<br />            self.stderr.close()<br />        self.__channel.close()<br /><br /># Add a "fabric" transport", which piggy-backs the existing SSH connection, but<br /># otherwise operates the same way as the built-in Paramiko transport.<br />class pseudo_module:<br />    Popen = FabricPopen<br />pushy.transports["fabric"] = pseudo_module<br /><br />###############################################################################<br /><br /># Pushy connection cache<br />connections = {}<br /><br />@needs_host<br />def remote_import(name, python="python"):<br />    """<br />    A Fabric operation for importing and returning a reference to a remote<br />    Python package.<br />    """<br /><br />    if (env.host_string, python) in connections:<br />        conn = connections[(env.host_string, python)]<br />    else:<br />        conn = pushy.connect("fabric:", python=python)<br />        connections[(env.host_string, python)] = conn<br />    m = getattr(conn.modules, name)<br />   if "." in name:<br />        for p in name.split(".")[1:]:<br />            m = getattr(m, p)<br />    return m<br /></pre></div>