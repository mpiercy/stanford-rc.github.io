---
title: How to Install Software
description: How to install software on a linux cluster.
tags: 
 - linux
 - hpc
---

# How to Install Software

Did you know that you are empowered to install your own software, if needed? Let's
talk about different ways to go about this.

 - [Building Software from Source](#building-software-from-source)
 - [Using a Package Manager](#using-a-package-manager)
 - [Using Containers](#using-containers)


## Building Software from Source

Much software is available as source code and can be configured to target a local directory for installation. The process, after downloading the source archive and unpacking it into a new subdirectory, usually involves some combination of the configure and make commands:

```bash
$ ./configure --prefix=$HOME/.local 
$ make 
$ make test 
$ make install
```

If you download source code and see that there is a configure, or a Makefile, you
can usually look for a README or INSTALL file to see complete instructions.

_What does the prefix mean?_

The prefix directory is where build products will end up, organized in subdirectories (*e.g.*, bin, lib, and include). In the example above newly built software is installed in a (hidden) subdirectory of your home directory, ~/.local, but you can choose any subdirectory you like.  For example,
it could be that your $HOME is filled up, and you want to have a base for software in your
scratch:

```bash
$ mkdir -p $SCRATCH/software
```

Whether you use ~/.local or your scratch, you will want to modify your shell environment so new software can be easily found. So let's open up our ~/.bashrc and add a few lines to our $PATH:

```bash
export PATH=$HOME/.local/bin:$PATH  
# or
export PATH=$SCRATCH/software/bin:$PATH  
```

While this method works well for simple applications and libraries, more complex software may require you to apply patches or build any dependencies that are not already installed. Luckily, there are a number of tools that can automate this process!

## Using a Package Manager

Package managers provide simple, single-command installation of software from a central repository, usually with little or no additional configuration required. This is the most convenient method for most users, and you'll probably want to fall back on a manual build only when a package is not available in your preferred package manager.

### Linuxbrew

[Linuxbrew](http://linuxbrew.sh) is a port of the [Homebrew](http://brew.sh) package manager for macOS. Installation is simple!

```bash
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install)"  
```

By default, both Linuxbrew itself and all managed software are installed in ~/.linuxbrew, so you’ll want to add this to your $PATH (again in your ~/.bashrc):

```bash
export PATH=$HOME/.linuxbrew/bin:$PATH
```

The brew command is used to search the repository, get information on software, and install packages.
Let's [look for](https://formulae.brew.sh/formula/) something fun.

```bash
$ brew search nyancat
$ brew info nyancat
$ brew install nyancat
```

Let's run it!

<script id="asciicast-258360" src="https://asciinema.org/a/258360.js" async></script>

Linuxbrew is great because it can also update itself and the list of available packages, and upgrade already installed software when new versions become available.

```bash
$ brew update          # update linuxbrew
$ brew update nyancat  # update a package
```

### Spack

[Spack](http://spack.readthedocs.io) is a package manager targeted at scientific computing. It has support for fewer packages than Linuxbrew and is a bit more difficult to install, configure, and use, but it provides *far* more extensive control over versions, dependencies, and build-time options when installing software. Spack also supports installing more than one version of a single package, or multiple installations of the same version with different build options, and integrates with FarmShare's existing module system, allowing you to select from among those versions at runtime.

To install it, just clone the Spack [git repository](https://github.com/LLNL/spack).

```bash
$ git clone https://github.com/llnl/spack.git .spack
```

In this example Spack and managed software is installed in ~/.spack, but you can choose another directory if you like. You should probably add $HOME/.spack/bin to your $PATH but, unlike Linuxbrew, installed software is *not* linked in ~/.spack/bin. Instead, Spack automatically creates modules that manage the environment for each package. You’ll need to enable shell integration by running:

```bash
$ source $HOME/.spack/share/spack/setup-env.sh  # sh or bash
$ source $HOME/.spack/share/spack/setup-env.csh # csh
```

so that you can load the modules.

The spack command can be used to search the software repository and get information on packages.

```bash
$ spack list python
$ spack info python
```

When building a package you can control a number of options, including the specific version to install (using @), what "variant" to build (+ and -), the exact versions of any dependencies that will be installed (^), and even which compiler to use (%). The list of options provided is called a "spec;" you can list installed packages and load a particular version according to its spec.

```bash
$ spack install python @3.5.2
$ spack load python@3.5.2
```

Spack can’t update itself, but because it is a git repository you can check out releases as they are tagged or use git pull to stay in sync with the latest changes. It also won't upgrade installed packages automatically, but you can use spack install to build a new version whenever one becomes available, and choose which version to load by spec. Run spack help for details on syntax and usage, and read the excellent Spack [manual](http://spack.readthedocs.io) for more information.

## Using Containers

Another option for running software with special requirements or missing dependencies is containerization. 
You can pull a container of interest using Singularity. For example, here again is [nyancat](https://hub.docker.com/r/supertest2014/nyan/).

```bash
$ singularity pull docker://supertest2014/nyan
```

And then execute commands to the container, run it like a binary, or shell inside:

```bash
$ singularity exec nyan_latest.sif which nyancat
$ singularity run nyan_latest.sif 
$ singularity shell nyan_latest.sif
```

You get the same result! See the [Singularity user guide](https://www.sylabs.io/guides/latest/user-guide/)
for more examples and getting started.

{% include alert.html title="Important!" content="It is <strong>essential</strong> that you pull containers first, and then execute commands, run, or otherwise interact if you run jobs in parallel." %}
