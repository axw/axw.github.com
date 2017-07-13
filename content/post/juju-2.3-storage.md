+++
date = "2017-07-13T15:41:31+08:00"
title = "Coming in Juju 2.3: storage improvements"
tags = ["juju", "storage", "lxd"]
+++

I've just about wrapped up a set of improvements to storage for
[Juju](https://jujucharms.com/) 2.3, the next "minor" release.
If you're already using, or have been planning to use Juju's
storage support, read on.

### Dynamic storage management

Juju charms can specify storage requirements: the number of
filesystems or block devices its application requires. For
example, the [PostgreSQL](https://jujucharms.com/postgresql)
charm requires a filesystem on which to store the database.
If you don't tell Juju otherwise, the storage will go onto
the root filesystem, but you can also tell Juju to provide
the charm with cloud storage (Amazon EBS, OpenStack Cinder,
etc.)

One of the missing pieces that users have been asking for is
the ability to manage the lifecycle of storage independently
of applications and units, and to reuse existing storage. In
Juju 2.3, when you remove an application or unit, the storage
attached to the unit(s) will (if possible) be detached, rather
than destroyed, and will remain in the model. You can then
either remove the storage using `juju remove-storage`, or
attach it to a new unit using the new `juju attach-storage`
command, or the `--attach-storage` flag added to `juju deploy`
and `juju add-unit`. To complement `juju attach-storage`, there
is also a new `juju detach-storage` command.

So to illustrate, you can now deploy PostgreSQL with cloud
storage, then remove the application, and redeploy (e.g. with
more RAM), using the same storage.

{{<highlight bash "style=bw">}}
juju deploy postgresql --storage=10G
…
juju remove-application postgresql
…
juju deploy postgresql --constraints mem=16G --attach-storage pgdata/0
{{</highlight>}}

We're still working on giving you commands to remove storage
from the model without destroying it, and then import it into
a new model (possibly new controller). This is required
for disaster recovery. Whether this makes it for 2.3 depends
on prioritisation; if it doesn't make it for 2.3, it shouldn't
be far behind.

### LXD Storage Provider

One thing that we hadn't planned for 2.3, but we did manage to
get done, is a LXD storage provider. [LXD has recently added
its own storage management API](https://insights.ubuntu.com/2017/07/12/storage-management-in-lxd-2-15/),
and Juju 2.3 will have a storage provider that uses it. I
originally implemented the Juju side of things as a bit of a
hack, behind a feature flag, in order to speed up the development
of the aforementioned attach/detach changes. The LXD storage
API turned out to be very straight forward to build on, so we
decided to release the Juju changes into the wild in case it's
of use to others. Particularly if you're developing or testing
charms that use storage, this should be useful.

Using the LXD storage provider is as simple as:

{{<highlight bash "style=bw">}}
juju deploy postgresql --storage=10G,lxd
{{</highlight>}}

Each storage pool using the "lxd" storage provider will create
an associated storage in LXD. When you create a storage pool
in Juju, you need to specify two configuration attributes:

 - the LXD storage pool name, as the "lxd-pool" attribute
 - the LXD storage driver, as the "driver" attribute

You can also define driver-specific attributes, which will be
passed through to the LXD storage driver verbatim.

Juju predefines a "lxd-zfs" pool, with the following attributes:

 - lxd-pool=juju-zfs
 - driver=zfs
 - zfs.pool_name=juju-zfs

If you deploy an application with storage using the lxd-zfs
pool, Juju will create a LXD storage pool called "juju-zfs"
with the "zfs" driver, and ZFS pool called "juju-zfs". To
find out more about the LXD storage driver options, see the
[LXD storage docs](https://github.com/lxc/lxd/blob/master/doc/storage.md).
