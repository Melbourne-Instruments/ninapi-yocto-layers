# ninapi-yocto-layers

Repo Manifest file for building Nina OS Images for the Nina Pi Development Kit using the Yocto/Openembedded build system.

## Prerequisites

In order to use the manifest file, you need to install Google's `repo` tool. Installation instructions can be found [here](https://gerrit.googlesource.com/git-repo). Also see **Note 2** below in the Usage notes.

You will also need a host Linux machine with a reasonably recent distribution and at least 50 GB of disk space available. Check the [official Yocto requirements](https://www.yoctoproject.org/docs/latest/ref-manual/ref-manual.html#ref-manual-system-requirements) for more details.
[Docker](https://www.docker.com/) can be used for building under Windows / macOS without installing a Linux Virtual Machine, although there will still be a non-negligible impact on build times compared to native Linux builds. Another popular option is to host the build environment on the cloud using Amazon's EC2 instances, Microsoft Azure or similar services.

You will also need to install [gRPC 1.14.1](https://github.com/grpc/grpc/tree/v1.14.1) and [protobuf 3.6.1](https://github.com/protocolbuffers/protobuf/tree/3.6.x) on your host environment.

## Usage

Initialize the layers using `repo init`. For example, this will use the manifest at the head of this repository:

```bash
repo init -u https://github.com/Melbourne-Instruments/ninapi-yocto-layers.git
```

To check out a particular version tag:

```bash
repo init -u https://github.com/Melbourne-Instruments/ninapi-yocto-layers.git -b regs/tag/<tag name>
```

After the initialization step, run the following command to fetch all the required layers:

```bash
repo sync
```

**Note:** If you do not have repo installed, or are having problems running it (with runtime errors like 'SyntaxError: invalid syntax'), then perform the following to temporarily install repo:

```bash
mkdir -p ~/.bin
PATH="${HOME}/.bin:${PATH}"
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/.bin/repo
chmod a+rx ~/.bin/repo
```

## Updating the layers

When a new version is released and the manifest file is updated, you can simply re-run `repo init` and `repo sync` as described in the previous steps. This will synchronize all the layers to the right commit without needing to refetch everything.

## Setting Up Yocto

After the layers are initialized with `repo`, you should see a new directory named `layers` in your current path.

Run the following command from the same directory as you ran `repo`:


```bash
TEMPLATECONF=./meta-ninapi/conf source layers/oe-init-build-env
```

This will set up the enviroment and you will end up in a directory called `build`. Now you can build the image as described in [meta-ninapi](https://github.com/Melbourne-Instruments/meta-ninapi):

```bash
bitbake ninapi-audio-os-image
bitbake ninapi-audio-os-swu
```

Be careful that the process to build an image from scratch can take up to several hours depending on your machine configuration. Following builds are usually much faster, since most of the heavy-weight components change rarely between updates of Nina OS.

To build the cross-compiling Yocto SDK, run:

```bash
bitbake -c populate_sdk ninapi-audio-os-image
```

---
Copyright 2021-2023 Melbourne Instruments, Australia.
