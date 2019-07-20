---
title: Using Resources Effectively (Part II)
tags: 
 - optimize
---

# Using Resources Effectively

This short guide is an overview of best practices for using shared cluster
resources.

 - [Be kind to the login node](#be-kind-to-the-login-node)
 - [Test before scaling](#test-before-scaling)
 - [Optimize Resources](#optimize-resources)
 - [Software](#software)

## Be kind to the login node

The login node is very busy managing lots and lots of jobs! It doesn’t have any extra space to run computational work. Don’t run jobs on the login node, though quick tasks like downloading a file or compiling a small program are generally fine. You’ll often find limits set on the login/head node that prevent you from using too many resources. Attempting to run programs when resource limits are set will often cause your programs to die with strange errors. Remember, the login node is to be shared with other users.
Most clusters have a queue available for interactive use called debug, testing, etc. This is generally a better way to run small tests or compile programs.

{% include alert.html type="question" title="Which of these commands would be okay to run on a login node?" content="
 1. python physics_sim.py<br>
 2. make<br>
 <span class='correct-1'>3. create_directories.sh<br></span>
 4. molecular_dynamics_2<br>
 5. tar -xzf R-3.3.0.tar.gz<br>
 <span class='correct-1'>6. sbatch run_my_job.sbatch<br></span>
 <span class='correct-1'>7. srun -p normal --mem=24GB --pty bash</span>" %}

<button onclick="$('.correct-1').css('font-weight', 800)" class="btn btn-primary">Show Answer</button>

The answer is that 3, 6, and 7 are definitely okay - creating directories or interacting with
slurm to launch jobs is what is expected on a login node! 

 - Number 5 might be okay, depending on the size of the archive, but if your next step is to install the R software, you'd want to already be on a development node. 
 - Numbers 1 and 4 are running potentially computationally intensive analyses, so those should never be on a login node. 
 - Finally, number 2 is compiling software, which should be done on a development node.

If you notice that a login node is slow and suspect that someone is running computationally intensive commands, you should message an administrator to take a look at things and deal with them.

## Test before scaling

Before submitting a large run of jobs, submit one as a test first to make sure everything works.
If you need to debug further, grab an interactive node and run through the commands manually.

## Have a backup plan

### Code

If you use a version control system like git (e.g., GitHub) to keep track of your code,
you won't risk making a change and not being able to restore to a previous version.
If you find yourself making multiple copies of scripts for backup, it's time to learn git!
A good getting started guide is available [here](https://git-scm.com/book/en/v1/Getting-Started).

### Data

Eventually, your data will need to leave the cluster. You should have a plan of where you’ll store all your results *before* you run jobs. Take a look at the Stanford Library's [Data Archive Services](https://library.stanford.edu/research/data-management-services) for good suggestions.

## Optimize Resources

### Files
If you need to transfer a large number of large files, it's good practice to compress them beforehand.
For analyses, when you are finished, make sure to clean up unecessary or intermediate data that is not
important for the reproduciblity or record of your work.

### Job Submission

It's tempting to use the same script and parameters to submit jobs, but you actually might be
slowing yourself down. The fewer resources that you ask for, the faster your jobs will find a 
slot in which to run. Lots of small jobs generally beat a couple of big jobs.

## Software

You can generally [install software yourself](/docs/software/how-to-install), but if you want a shared installation of some kind, it might be a good idea to message an administrator.

Always use the default compilers if possible. Newer compilers are great, but older stuff generally has less compatibility issues.

>  <strong> Too Long, Didn't Read!
>  - Don’t run stuff on the login node.
>  - Again, don’t run stuff on the login node.
>  - Don’t be a bad person and run stuff on the login node.

