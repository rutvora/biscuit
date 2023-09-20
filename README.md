# Biscuit research OS

Biscuit is a monolithic, POSIX-subset operating system kernel in Go for x86-64
CPUs. It was written to study the performance trade-offs of using a high-level
language with garbage collection to implement a kernel with a common style of
architecture. You can find research papers about Biscuit here:
https://pdos.csail.mit.edu/projects/biscuit.html

Biscuit has some important features for getting good application performance:
- Multicore
- Kernel-supported threads
- Journaled FS with concurrent, deferred, and group commit
- Virtual memory for copy-on-write and lazily mapped anonymous/file pages
- TCP/IP stack
- AHCI SATA disk driver
- Intel 10Gb NIC driver

Biscuit also includes a bootloader, a partial libc ("litc"), and some user
space programs, though we could have used GRUB or existing libc
implementations, like musl.

This repo is a fork of the Go repo (https://github.com/golang/go).  Nearly all
of Biscuit's code is in biscuit/.

## Prerequisites
I have tested this on ubuntu 22.04 on an x86_64 system only
```
$ sudo apt install build-essential git qemu-system-x86_64 python2
```

## Install

The root of the repository contains the Go 1.10.1 tools/runtime. Some of
Biscuit's code is modifications to the runtime, mostly in
src/runtime/os_linux.go.

Biscuit used to build on Linux and OpenBSD, but probably only builds on Linux
currently. You must build Biscuit's modified Go runtime before building
Biscuit:
```
$ git clone https://github.com/mit-pdos/biscuit.git
$ cd biscuit
$ # Docker helps avoid installing an older go version on your main system
$ docker run -it --rm -v $(pwd):/go golang:1.10 bash -c 'cd src && ./make.bash'
```

then go to Biscuit's main part and launch it:
```
$ cd biscuit # Yes, the nested "biscuit" folder inside the repository
$ make qemu CPUS=2
```

Biscuit should boot, then you can type a command:
```
# ls
```

Biscuit does not support `exit` or `quit`, so you have to use qemu commands instead (e.g. `Ctrl+A -> X` to terminate/shutdown QEMU instance)

## Troubleshooting

* You need `qemu-system-x86_64` and `python2` in your environment.  If your distribution does not name them that way, you have to fix the naming, path, etc.

* If the GOPATH environment variable doesn't contain biscuit/, the build will fail with something like:
```
src/ahci/ahci.go:8:8: cannot find package "container/list" in any of:
...
```

Either unset GOPATH or set it explicitly, for example (assuming that your working directory is where the `GNUMakefile` is):
```
$ GOPATH=$(pwd) make qemu CPUS=2
```

## Contributing

Please feel free to hack on Biscuit! We're happy to accept contributions.
