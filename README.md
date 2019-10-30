# Bob containers example

This repository uses the [basement](https://github.com/BobBuildTool/basement)
recipes to build a lighttpd container.

[![Build Status](https://ci.bobbuildtool.dev/jenkins/buildStatus/icon?job=example-containers-containers__lighttpd)](https://ci.bobbuildtool.dev/jenkins/job/example-containers-containers__lighttpd/)

# Prerequisites

* A x86_64 system with the regular development tools installed (gcc, make,
  perl, ...)
* Bleeding edge Bob Build Tool (https://github.com/BobBuildTool/bob)
* Patience

# How to build

Clone the recipes and build them with Bob:

    $ git clone https://github.com/BobBuildTool/bob-example-containers.git \
	    --recurse-submodules
    $ bob build containers::lighttpd

This recipe builds a minimal container image that has solely lighttpd and the
required dependencies installed.

# How to use

To use it you have to import it in docker:

    $ tar -C $(bob query-path -f '{dist}' --release containers::lighttpd) -c . \
        | docker import - lighttpd

Now you can serve any host directory with the lighttpd in the image:

    $ docker run -it --rm -p 8080:80 -v <your-html-dir>:/srv/www lighttpd \
        /usr/sbin/lighttpd -D -f /etc/lighttpd/lighttpd.conf \
        -m /usr/lib/lighttpd

This will expose the lighttpd at port 8080 on your host.
