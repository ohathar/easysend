Mac OS X easysend build instructions
====================================

Authors
-------

* Laszlo Hanyecz <solar@heliacal.net>
* Douglas Huff <dhuff@jrbobdobbs.org>
* Colin Dean <cad@cad.cx>
* Gavin Andresen <gavinandresen@gmail.com>

License
-------

Copyright (c) 2009-2012 Bitcoin Developers
Copyright (c) 2014 Easysend Developers

Distributed under the MIT/X11 software license, see the accompanying
file COPYING or http://www.opensource.org/licenses/mit-license.php.

This product includes software developed by the OpenSSL Project for use in
the OpenSSL Toolkit (https://www.openssl.org/).

This product includes cryptographic software written by
Eric Young (eay@cryptsoft.com) and UPnP software written by Thomas Bernard.

Notes
-----

See `doc/readme-qt.rst` for instructions on building Easysend-Qt, the
graphical user interface.

Tested on OS X 10.5 through 10.10 on Intel processors only. PPC is not
supported because it is big-endian.

All of the commands should be executed in a Terminal application. The
default one is located in `/Applications/Utilities`.

Preparation
-----------

You need to install XCode with all the options checked so that the compiler
and everything is available in /usr not just /Developer. XCode should be
available on your OS X installation media, but if not, you can get the
current version from https://developer.apple.com/xcode/. If you install
Xcode 4.3 or later, you'll need to install its command line tools. This can
be done in `Xcode > Preferences > Downloads > Components` and generally must
be re-done or updated every time Xcode is updated.

There's an assumption that you already have `git` installed, as well. If
not, it's the path of least resistance to install [Github for Mac](https://mac.github.com/)
(OS X 10.7+) or
[Git for OS X](https://code.google.com/p/git-osx-installer/). It is also
available via Homebrew or MacPorts.

You will also need to install [Homebrew](http://brew.sh/)
or [MacPorts](https://www.macports.org/) in order to install library
dependencies. It's largely a religious decision which to choose. If you're unsure, read
the instructions through first in order to assess what you want to do.
Homebrew is a little more popular among those newer to OS X.

The installation of the actual dependencies is covered in the Instructions
sections below.

Instructions: MacPorts
----------------------

### Install dependencies

Installing the dependencies using MacPorts is very straightforward.

    sudo port install openssl miniupnpc autoconf automake db48 boost miniupnpc pkgconfig protobuf-cpp qt4-mac

### Building `easysend`

1. Clone the github tree to get the source code and go into the directory.

        git clone git@github.com:easysend/easysend.git easysend
        cd easysend

2.  Build easysend:

        cd src
        make -f makefile.osx

3.  It is a good idea to build and run the unit tests, too:

        make -f makefile.osx test

Instructions: HomeBrew
----------------------

#### Install dependencies using Homebrew

        brew install boost miniupnpc openssl berkeley-db4

### Building `easysend`

1. Clone the github tree to get the source code and go into the directory.

        git clone git@github.com:easysend-project/easysend.git easysend
        cd easysend

2.  Modify source in order to pick up the `openssl` library.

    Edit `makefile.osx` to account for library location differences. There's a
    diff in `contrib/homebrew/makefile.osx.patch` that shows what you need to
    change, or you can just patch by doing

        patch -p1 < contrib/homebrew/makefile.osx.patch

3.  Build easysend:

        cd src
        make -f makefile.osx

4.  It is a good idea to build and run the unit tests, too:

        make -f makefile.osx test

Creating a release build
------------------------

A easysend binary is not included in the Easysend-Qt.app bundle. You can ignore
this section if you are building `easysend` for your own use.

If you are building `easysend` for others, your build machine should be set up
as follows for maximum compatibility:

All dependencies should be compiled with these flags:

    -mmacosx-version-min=10.5 -arch i386 -isysroot /Developer/SDKs/MacOSX10.5.sdk

For MacPorts, that means editing your macports.conf and setting
`macosx_deployment_target` and `build_arch`:

    macosx_deployment_target=10.5
    build_arch=i386

... and then uninstalling and re-installing, or simply rebuilding, all ports.

As of December 2012, the `boost` port does not obey `macosx_deployment_target`.
Download `http://gavinandresen-bitcoin.s3.amazonaws.com/boost_macports_fix.zip`
for a fix. Some ports also seem to obey either `build_arch` or
`macosx_deployment_target`, but not both at the same time. For example, building
on an OS X 10.6 64-bit machine fails. Official release builds of Easysend-Qt are
compiled on an OS X 10.6 32-bit machine to workaround that problem.

Once dependencies are compiled, creating `Easysend-Qt.app` is easy:

    make -f Makefile.osx RELEASE=1

Running
-------

It's now available at `./easysend`, provided that you are still in the `src`
directory. We have to first create the RPC configuration file, though.

Run `./easysend` to get the filename where it should be put, or just try these
commands:

    echo -e "rpcuser=easysendrpc\nrpcpassword=$(xxd -l 16 -p /dev/urandom)" > "/Users/${USER}/Library/Application Support/easysend/easysend.conf"
    chmod 600 "/Users/${USER}/Library/Application Support/easysend/easysend.conf"

When next you run it, it will start downloading the blockchain, but it won't
output anything while it's doing this. This process may take several hours.

Other commands:

    ./easysend --help  # for a list of command-line options.
    ./easysend -daemon # to start the easysend daemon.
    ./easysend help    # When the daemon is running, to get a list of RPC commands
