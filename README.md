## Introduction

Anode is an embryonic framework for running node.js applications on Android. There are two main parts to this:

- a port of [node.js](https://github.com/joyent/node) to the Android OS and libraries. The code is [here](https://github.com/paddybyers/node);

- a set of Android projects (this repo) that provide the integration with the Android frameworks.

Anode builds to an Android application package (.apk) that encapsulates the node.js runtime and can run node.js applications through an intent-based API.

## Build

## Set up the build environment

These instructions should work on Mac and Linux. Windows/cygwin is not fully working yet.

Get the latest Android [SDK](http://developer.android.com/sdk/index.html) and [NDK](http://developer.android.com/sdk/ndk/index.html). You need to install support for the [android-9](http://developer.android.com/sdk/android-2.3.html) platform.

Install a [recent version of Python 2.x](http://www.python.org/download/releases/2.7.2/).

Install [Git](http://git-scm.com/download).

Create a directory, we'll refer to as `<work dir>` that will contain all of the repositories you need, and cd into this directory.

## Prerequisites - SSL

anode depends on the openssl libraries `libssl.so` and `libcrypto.so`. These libraries are present on the phone and also on the emulator, but aren't officially part of the ndk. We prefer to use the platform-provided versions of these libraries (instead of building a copy into anode) and so you have to build a copy of each of these libraries before building anode itself.

Starting in `<work dir>`, clone the repo:

    git clone https://github.com/paddybyers/openssl-android.git

To build it:

    cd openssl-android
    ndk-build

## Download the code

cd back to `<work dir>`.

Clone each of the repositories in turn.

    git clone https://github.com/paddybyers/anode.git
    git clone https://github.com/paddybyers/pty.git
    git clone https://github.com/paddybyers/node.git

This last repository has a number of development branches with the Android port, but use the default, `v0.6-android` which is derived from node.js v0.6.

There is also a `master-isolate-android` branch which is derived from master. To use this, you will need to cd into the `node` directory and `git checkout master-isolate-android`. This will be used to track 0.7/0.8 developments but should be expected to be unstable.

## Environment variables

If the organisation of the repo directories is as suggested above, then the build system will find everything.

However, if you specifically wish to build against a node source tree located elsewhere, set up the `NODE_ROOT` variable to point to the top-level `node` directory of that repo.

## Build the native code

There is a single top-level `Application.mk` that builds the libjninode and bridge dependencies.

    cd anode
    ndk-build NDK_PROJECT_PATH=. NDK_APPLICATION_MK=Application.mk

Once the native libraries is built, this needs to be included as an asset in the top-level Android project:

    cp libs/armeabi/libjninode.so ./app/assets/
    cp libs/armeabi/bridge.node ./app/assets/

(The previous instructions involved creating a symlink to the shared library instead of copying the file. However, for some reason this does not work with `bridge.node` - Eclipse does not seem to recognise the file if it is linked and not copied. If anyone understands why this is, please let me know.)

Or if you prefer (or on Windows), edit the top-level makefile to add an install target to perform the copy.

## Set up the Eclipse Android projects

Open Eclipse and do:
 
File->Import->General->Existing projects into workspace

Point to the `<work dir>/anode directory` and import the `app`, `libnode` and `bridge-java` projects.

You are now ready to build and run.

## Status

This work is at an early stage. All input is welcome.

The current target is to support node.js applications, invoked by intent. Multiple instances can be run in parallel. Modules (or addons) for node.js written in JavaScript or native are supported, and support for modules implemented in Java is on the roadmap.

This framework depends on a port of node [here](https://github.com/paddybyers/node).

## More information

Please see the [wiki](https://github.com/paddybyers/anode/wiki/Anode) for more information.
