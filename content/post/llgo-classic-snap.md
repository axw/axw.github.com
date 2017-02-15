+++
date = "2017-02-15T08:00:37+08:00"
title = "llgo: classic snap"
tags = ["llgo", "snapcraft"]
+++

Some time ago I created a [snap](https://snapcraft.io/) for
[llgo](https://github.com/llvm-mirror/llgo). Snaps are a
relatively new packaging format; at the time I created the
initial snap for llgo, it was only possible to create them
confined by default. In particular, access to the user's
filesystem was blocked by default, which is unhelpful for
a tool like a compiler. Because of that, the initial snap
did not expose the llgo compiler, only the llgoi interpreter.

Now there is the concept of "classic snaps", which use the
snap packaging format but do not confine the application.
The resulting package is much more like what you would get
from a Debian package, but without the arcana.

I have released a new llgo classic snap, exposing the llgoi
interpreter, and the llgo compiler and "go" tool wrapper.
To install the llgo snap, run:

{{< highlight bash "style=bw" >}}
sudo snap install --classic llgo
{{< /highlight >}}

The exposed "llgo" command is an alias for the go tool
wrapper, so you can just run:

{{< highlight bash "style=bw" >}}
$ llgo install <package>
$ llgo run /path/to/source.go
...
{{< /highlight >}}

as you would with the standard go tool. The llgo compiler
itself can be invoked with the "llgo.compiler" command:

{{< highlight bash "style=bw" >}}
$ llgo.compiler -emit-llvm -S -o -o main.go
; ModuleID = 'main'
source_filename = "main"
target datalayout ...
{{< /highlight >}}

The llgoi interpreter is still available, but as it is part
of the same snap it is no longer confined:

{{< highlight bash "style=bw" >}}
$ llgo.llgoi
(llgo) import "fmt"
(llgo) fmt.Println("hello, world")
hello, world
13
<nil>
{{< /highlight >}}
