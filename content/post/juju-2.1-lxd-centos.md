+++
date = "2017-02-14T20:23:26+08:00"
title = "Juju 2.1 and CentOS"
tags = ["juju", "lxd"]
draft = true
+++

In the [Juju](https://jujucharms.com/) 2.1 release, I made a couple of small
changes to better support CentOS servers.

The first change was to support "manual provisioning" of CentOS machines.
Manual provisioning is when you point Juju at a machine, and Juju connects
to the machine over SSH and sets it up with a Juju agent. To do this, Juju
needs to run a small shell script to discover the OS version and hardware
characteristics of the machine. With a minor change to that script, you can
now manually provision CentOS machines.

The second change is to support CentOS LXD images. A small change was needed
in the Juju code to support the "centos7" OS version, and alter the way we
handle local LXD image aliases. If an image exists locally with the expected
alias (e.g. "juju/centos7/amd64"), then we'll use that and skip looking in
the remote image sources. This also improves container startup time when
you live in a faraway land like me. Altering Juju is not quite enough though,
as there are no existing CentOS images that Juju can use.

Juju (mostly) requires [cloud-init](https://cloudinit.readthedocs.io/) to be
present on the machines it starts, so that it can inject Juju-specific configuration
and scripts to run on startup. Unforunately, there are no CentOS LXD images
that have cloud-init already. To work around this, I wrote a standalone Go
program to transform the [linuxcontainers.org](https://linuxcontainers.org)
CentOS image: [github.com/axw/juju-lxd-centos-image-builder](https://github.com/axw/juju-lxd-centos-image-builder).
Eventually we hope to have pre-canned CentOS LXD images available to Juju,
but for now you can use this program to prepare an image for Juju. Run it from
the LXD host, and Juju will be able to use the resulting image.

