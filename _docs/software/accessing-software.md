---
title: Accessing Software
tags: 
 - lmod
---

# Accessing software

On many high-performance computing systems, no software is loaded by default. If we want to use a software package, we will need to "load" it ourselves. Here will will review how to load and unload software packages in different environment.

Before we start using individual software packages, however, we should understand the reasoning behind this approach. The two biggest factors are software incompatibilities and versioning.

## Software incompatibility

Software incompatibility is a major headache for programmers. Sometimes the presence (or absence) of a software package will break others that depend on it. Two of the most famous examples are Python 2 and 3 and C compiler versions. Python 3 famously provides a python command that conflicts with that provided by Python 2. Software compiled against a newer version of the C libraries and then used when they are not present will result in a nasty 'GLIBCXX_3.4.20' not found error, for instance.

## Software Versioning

Software versioning is another common issue. A team might depend on a certain package version for their research project - if the software version was to change (for instance, if a package was updated), it might affect their results. Having access to multiple software versions allow a set of researchers to prevent software versioning issues from affecting their results.

## Environment modules (Lmod)

Environment modules ([lmod](https://lmod.readthedocs.io/en/latest/)) are the solution to these problems. A module is a self-contained software package - it contains all of the files required to run a software package and loads required dependencies.

To see available software modules, use module avail

```bash
[remote]$ module avail
```
```bash
----------------------- /farmshare/software/modules/Core -----------------------
   abinit/8.4.2            (D)    gurobipy3/8.1.0        (D)
   ampl/20150630                  hmmer/3.2.1
   anaconda/4.4.0                 idba/1.1.3
   anaconda/5.0.1                 intel/17.0.2
   anaconda/5.2.0          (D)    julia/0.6.4
   anaconda3/4.4.0                julia/0.7.0
   anaconda3/5.0.1                julia/1.0.0
   anaconda3/5.2.0         (D)    julia/1.0.2
   ansys/17.1                     julia/1.1.0            (D)
   ansys/18.2              (D)    kaiju/1.6.2
   caffe/1.0.0-rc5     (g)        krona/2.7
   caffe2/0.7.0        (g)        luarocks/2.4.1
   clang/3.9.1                    mafft/7.402
   clang/4.0.0                    maple/2017
   clang/5.0.0                    mathematica/11.0.1
   clang/6.0.0                    mathematica/11.2       (D)
   clang/7.0.0                    matlab/r2016b
   clang/8.0.0             (D)    matlab/r2017a
   cplex/12.7.1                   matlab/r2017b
   cplex/12.8.0            (D)    matlab/r2018a
   cuda/8.0            (g)        matlab/r2018b          (D)
   cuda/9.0            (g)        maxbin/2.2.5
```

## Loading and unloading software

To load a software module, use module load. In this example we will use Matlab.

Initially, Matlab is not loaded. We can test this by using the which command. which looks for programs the same way that Bash does, so we can use it to tell us where a particular piece of software is stored.

```bash
[remote]$ which matlab
matlab: Command not found.
```

We can load the matlab command with module load:

```bash
[remote]$ module load matlab 
[remote]$ which matlab       
/farmshare/software/non-free/MATLAB-R2018b/bin/matlab    
```

So what just happened?

To understand the output, first we need to understand the nature of the $PATH environment variable. $PATH is a special environment variable that controls where a UNIX system looks for software. Specifically $PATH is a list of directories (separated by :) that the OS searches through for a command before giving up and telling us it can’t find it. As with all environment variables we can print it out using echo.

```bash
[remote]$ echo $PATH       
/farmshare/software/non-free/MATLAB-R2018b/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games    
```

You’ll notice a similarity to the output of the which command. In this case, there’s only one difference: the `/farmshare/software/non-free/MATLAB-R2018b/bin/matlab` directory at the beginning. When we ran module load matlab, it added this directory to the beginning of our $PATH. Let’s examine what’s there:

```bash
[remote]$ ls /farmshare/software/non-free/MATLAB-R2018b
appdata  cefclient  etc       extern  java       licenses  patents.txt  remote     rtw settings  src  toolbox        ui
...
```

Taking this to it’s conclusion, module load will add software to your $PATH. It "loads" software. A special note on this - depending on which version of the module program that is installed at your site, module load will also load required software dependencies. To demonstrate, let’s use module list. module list shows all loaded software modules.

```bash
[remote]$ module load torch 
[remote]$ module list       
Currently Loaded Modules:
  1) cuda/9.2 (g)   2) cudnn/7.1.4 (g)   3) torch/7 (g)

  Where:
   (g):  built for GPU
```

So in this case, loading the torch module (a machine learning software package), also loaded cuda/9.2 and cudnn/7.1.4 as well. Let’s try unloading the torch package.

```bash
[remote]$ module unload torch 
[remote]$ module list

Currently Loaded Modules:
  1) matlab/r2018b
```

So using module unload "un-loads" a module along with its dependencies. If we wanted to unload everything at once, we could run module purge (unloads everything).

```bash
[remote]$ module purge
```
```bash
ml list 
No modules loaded    
```
Note that module purge is informative. It lets us know that all but a default set of packages have been unloaded (and how to actually unload these if we truly so desired). You can also use "ml" as a shortcut for "module".


## Software versioning

So far, we’ve learned how to load and unload software packages. This is very useful. However, we have not yet addressed the issue of software versioning. At some point or other, you will run into issues where only one particular version of some software will be suitable. Perhaps a key bugfix only happened in a certain version, or version X broke compatibility with a file format you use. In either of these example cases, it helps to be very specific about what software is loaded.

Let’s examine the output of module avail more closely.

```bash
[remote]$ module avail
----------------------------------------------------------- Core Modules -----------------------------------------------------------
   StdEnv/2016.4 (S,L)     imkl/11.3.4.258 (L,math,D:11)      mcr/R2014b         (t)          python/3.5.2     (t,D:3:3.5)
   bioperl/1.7.1 (bio)     imkl/2017.1.132 (math,2017)        mcr/R2015a         (t)          qt/4.8.7         (t)
   eclipse/4.6.0 (t)       impute2/2.3.2   (bio)              mcr/R2015b         (t)          qt/5.6.1         (t,D)
   eigen/3.3.2   (math)    intel/2016.4    (L,t,D:16:2016)    mcr/R2016a         (t)          signalp/4.1f     (bio)
   fastqc/0.11.5 (bio)     intel/2017.1    (t,17:2017)        mcr/R2016b         (t,D)        spark/2.1.0      (t)
   g2clib/1.6.0            jasper/1.900.1  (vis)              minimac2/2014.9.15 (bio)        spark/2.1.1      (t,D)
   g2lib/1.4.0             java/1.8.0_121  (L,t)              perl/5.22.2        (t)          tbb/2017.2.132   (t)
   gatk/3.7      (bio)     mach/1.0.18     (bio)              pgi/17.3           (t)          tmhmm/2.0c       (bio)
   gcc/4.8.5     (t)       mcr/R2013a      (t)                picard/2.1.1       (bio)        trimmomatic/0.36 (bio)
   gcc/5.4.0     (t,D)     mcr/R2014a      (t)                python/2.7.13      (t,2:2.7)
```

Let’s take a closer look at the gcc module. GCC is an extremely widely used C/C++/Fortran compiler. Tons of software is dependent on the GCC version, and might not compile or run if the wrong version is loaded. In this case, there are two different versions: gcc/4.8.5 and gcc/5.4.0. How do we load each copy and which copy is the default?

In this case, gcc/5.4.0 has a (D) next to it. This indicates that it is the default - if we type module load gcc, this is the copy that will be loaded.

```bash
[remote]$ module load gcc 
[remote]$ gcc --version       

Lmod is automatically replacing "intel/2016.4" with "gcc/5.4.0".


Due to MODULEPATH changes, the following have been reloaded:
  1) openmpi/2.1.1

gcc (GCC) 5.4.0
Copyright (C) 2015 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

Note that three things happened: the default copy of GCC was loaded (version 5.4.0), the Intel compilers (which conflict with GCC) were unloaded, and software that is dependent on compiler (OpenMPI) was reloaded. The module system turned what might be a super-complex operation into a single command.

So how do we load the non-default copy of a software package? In this case, the only change we need to make is be more specific about the module we are loading. There are two GCC modules: gcc/5.4.0 and gcc/4.8.5. To load a non-default module, the only change we need to make to our module load command is to leave in the version number after the /.

```bash
[remote]$ module load gcc/4.8.5 
[remote]$ gcc --version       


Inactive Modules:
  1) openmpi

The following have been reloaded with a version change:
  1) gcc/5.4.0 => gcc/4.8.5

gcc (GCC) 4.8.5
Copyright (C) 2015 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

We now have successfully switched from GCC 5.4.0 to GCC 4.8.5. It is also important to note that there was no compatible OpenMPI module available for GCC 4.8.5. Because of this, the module program has "inactivated" the module. All this means for us is that if we re-load GCC 5.4.0, module will remember OpenMPI used to be loaded and load that module as well.

```bash
[remote]$ module load gcc/5.4.0       

Activating Modules:
  1) openmpi/2.1.1

The following have been reloaded with a version change:
  1) gcc/4.8.5 => gcc/5.4.0
```

## Loading modules during jobs

Running a job is just like logging on to the system in that nothing is loaded by default.
We will need to load modules in our scripts on worker nodes just as we do on the login nodes.
What are ways to do this?

## Using software modules in scripts

If you want to "one off" load a module, you can of course do that in your batch
scripts. For example, here we are loading the Gromacs software before running our script:

```bash
#!/bin/bash
#SBATCH --job-name=cron
#SBATCH --time=00:02:00
#SBATCH --mail-type=FAIL
#SBATCH -p gpu
#SBATCH -c 1
#SBATCH --gres gpu:1

# Load modules here
ml load gromacs/2016.3

# Run your script
/bin/bash /scratch/users/pancakes/runMe.sh
```

### Loading a module by default

Adding a set of module load commands to all of your scripts and having to manually load modules every time you log on can be tiresome. Fortunately, there is a way of specifying a set of “default modules” that always get loaded, regardless of whether or not you’re logged on or running a job. 

Every user has two hidden files in their home directory: .bashrc and .bash_profile (you can see these files with ls -la ~). These scripts are run every time you log on or run a job. Adding a module load command to one of these shell scripts means that that module will always be loaded. Modify either your .bashrc or .bash_profile scripts to load a commonly used module like Python. 


## Installing software of our own

Most HPC clusters have a pretty large set of preinstalled software. Nonetheless, it’s unlikely that all of the software we’ll need will be available. Sooner or later, we’ll need to install some software of our own.

Though software installation differs from package to package, the general process is the same: download the software, read the installation instructions (important!), install dependencies, compile, then start using our software. For more details, see the [installing software](/docs/software/how-to-install) page.
We will walk through an example here so we can show you how to add your custom software
to your path.

As an example we will install the bioinformatics toolkit seqtk. We’ll first need to obtain the source code from GitHub using git.

```bash
[remote]$ git clone https://github.com/lh3/seqtk.git       
Cloning into 'seqtk'...
remote: Counting objects: 316, done.
remote: Total 316 (delta 0), reused 0 (delta 0), pack-reused 316
Receiving objects: 100% (316/316), 141.52 KiB | 0 bytes/s, done.
Resolving deltas: 100% (181/181), done.
```

Now, using the instructions in the README.md file, all we need to do to complete the install is to cd into the seqtk folder and run the command make. Note that we should do this on a development node, and not
the login nodes.

```bash
[remote]$ cd seqtk 
[remote]$ make       
gcc -g -Wall -O2 -Wno-unused-function seqtk.c -o seqtk -lz -lm
seqtk.c: In function ‘stk_comp’:
seqtk.c:399:16: warning: variable ‘lc’ set but not used [-Wunused-but-set-variable]
    int la, lb, lc, na, nb, nc, cnt[11];
...
```

It’s done! Now all we need to do to use the program is invoke it like any other program.

```bash
[remote]$ ./seqtk
```

We’ve successfully installed our first piece of software! But what happens if we change directory?
We wouldn't be able to find it. This means that we have two choices:

 1. Call the binary directly with the complete path, e.g., `/home/<username>/seqtk/bin/seqtk --help`
 2. Add the bin folder to our path in our .bashrc or .bash_profile.


> ## Key Points
> *  Load software with module load softwareName
> *  Unload software with module purge
> *  The module system handles software versioning and package conflicts for you automatically.
> *  You can edit your .bashrc file to automatically load a software package.
