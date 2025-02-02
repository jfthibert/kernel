# Stadia kernel

This repo contains the Stadia instance kernel and build system.

## External modules

The Stadia kernel integrates an external version of the `amdgpu` kernel module.
The module is imported as a submodule in `external/amd-cloudgpu`. The built-in
`amdgpu` module is not compiled.

## Configuring

Stadia uses a [custom kernel configuration](kokoro/config). To modify it, copy
it to the repo root as `.config` and run `make menuconfig`. Once edits and
testing are done, copy `.config` back to the `kokoro` directory and send a merge
request with the changes.

## Building

Stadia uses a build script that invokes the kernel's build system and performs
additional post-build processing to yield a bootable VM disk image. The build
is performed in a Docker container maintained on Google Cloud.

Prior to building for the first time, you will need to
[install Docker](https://docs.docker.com/engine/install/).

Before building, ensure that submodules have been updated with
`git submodule update --init --depth=1` or a similar command.

To build, run `sudo ./kokoro/docker/glinux_launcher.sh`. You can optionally pass
a build directory as an argument.

### amdgpu firmware

The Stadia boot image includes a custom initramfs which includes a squashfs
filesystem (the "kernel root filesystem" or "krootfs") which includes the set of
amdgpu firmware files required on Stadia. The build script downloads a set of
firmware files from
[linux-firmware](https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git)
by default. To override the default firmware bundle, set the environment
variables `AMDGPU_FIRMWARE_URL` to the download URL and `AMDGPU_FIRMWARE_SHA256`
to the sha256 checksum of a gzipped tar archive containing the firmware files.
When using `sudo`, the environment variables have to be set as part of the
command, otherwise they get filtered out.

```shell
sudo AMDGPU_FIRMWARE_URL=... AMDGPU_FIRMWARE_SHA256=... ./kokoro/docker/glinux_launcher.sh
```

## Contributing

Direct contributions to this kernel are welcome in the form of Merge Requests.
Alternatively, submit your patch to upstream Linux and then open an issue for
the patch to be merged.

## Linux kernel

There are several guides for kernel developers and users. These guides can
be rendered in a number of formats, like HTML and PDF. Please read
Documentation/admin-guide/README.rst first.

In order to build the documentation, use `make htmldocs` or
`make pdfdocs`. The formatted documentation can also be read online at:

[https://www.kernel.org/doc/html/latest/](https://www.kernel.org/doc/html/latest/)

There are various text files in the Documentation/ subdirectory,
several of them using the Restructured Text markup notation.
See Documentation/00-INDEX for a list of what is contained in each file.

Please read the Documentation/process/changes.rst file, as it contains the
requirements for building and running the kernel, and information about
the problems which may result by upgrading your kernel.
