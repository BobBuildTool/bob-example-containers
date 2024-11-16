# Bob Containers Example

[![Build Status](https://ci.bobbuildtool.dev/jenkins/buildStatus/icon?job=example-containers-containers__lighttpd)](https://ci.bobbuildtool.dev/jenkins/job/example-containers-containers__lighttpd/)

This repository is meant as an example of how to build a complete Linux image
with Bob that can be used as-is or with minimal changes for various uses, such
as a Docker container, as a virtual machine, on a SD card in your single board
computer or with `systemd-nspawn`.

It uses the [basement](https://github.com/BobBuildTool/basement) recipe layer to
build a Docker container running lighttpd, with only the necessary dependencies
installed - in contrast to building such a container on top of a Debian release.
For more information about recipe layers and Bob's usage in general see [its
documentation](https://bob-build-tool.readthedocs.io/en/latest/index.html).

If you're interested in building an embedded application using Bob and the
basement layer, head over to the [embedded
example](https://github.com/BobBuildTool/bob-example-embedded).

# Prerequisites

* A `x86_64` system with the regular development tools installed (GCC, make,
  Perl, ...)
* [Bob Build Tool](https://github.com/BobBuildTool/bob)
* Patience :coffee:

# How to build

First of all, you have to clone the recipes and change to the checked-out
directory:

    $ git clone https://github.com/BobBuildTool/bob-example-containers.git
    $ cd bob-example-containers

Then the layers that this project depends on need to be fetched.

    $ bob layers update

The next steps depend on what you want to build.

## Docker Container

These recipes can build a minimal container image that has solely lighttpd and
the required dependencies installed by running the following command:

    $ # Still in the bob-example-containers directory
    $ bob build containers::lighttpd

## Systemd Portable Service

These recipes can also provide a systemd portable service that can be attached
with `portablectl` by issuing the following command:

    $ # Still in the bob-example-containers directory
    $ bob build containers::lighttpd-image

# How to use

Again, usage of the build products depends on the chosen target.

## Docker Container

To use it you have to import it in Docker:

    $ # Still in the bob-example-containers directory after building
    $ # containers::lighttpd
    $ tar -C $(bob query-path -f '{dist}' --release containers::lighttpd) -c . \
        | docker import - lighttpd

Now you can serve any host directory with the lighttpd in the image:

    $ docker run -it --rm -p 8080:80 -v <your-html-dir>:/srv/www lighttpd \
        /usr/sbin/lighttpd -D -f /etc/lighttpd/lighttpd.conf \
        -m /usr/lib/lighttpd

This will expose the lighttpd at port 8080 on your host.

## Systemd Portable Service

First you have to import the portable service:

    $ # Still in the bob-example-containers directory after building
    $ # containers::lighttpd-image
    $ portablectl attach $(bob query-path -f '{dist}' --release containers::lighttpd-image)/lighttpd.raw

Now you can start the lighttpd as any other systemd service on your host:

    $ systemctl start lighttpd.service

lighttpd should now listen on port 80.

# Contributions

Contributions are welcome in form of feedback, bug reports and code. If you want
to contribute in the form of code but are unsure about how to do things exactly,
feel free to open up a pull request and ask for help.

Currently planned extension are:

* [ ] Support generating more image formats
