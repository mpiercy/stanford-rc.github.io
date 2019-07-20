---
title: Using Resources Effectively (Part I)
tags: 
 - optimize
 - slurm
---

# Using Resources Effectively

After this tutorial, you should understand how:

 - to schedule jobs
 - to monitor jobs
 - to profile code

Before starting this tutorial, you should generally know how to log on, submit different types of jobs, use preinstalled software, and install and use software of your own. This tutorial 
will teach you how to use the systems effectively, and is intended to be run on a SLURM cluster.

## Estimating required resources using the scheduler

Although we covered requesting resources from the scheduler earlier, how do we know how much and what type of resources we will need in the first place?

Answer: we don’t. Not until we’ve tried it ourselves at least once. We’ll need to benchmark our job and experiment with it before we know how much it needs in the way of resources.

The most effective way of figuring out how much resources a job needs is to submit a test job, and then ask the scheduler how many resources it used. A good rule of thumb is to ask the scheduler for more time and memory than your job can use. This value is typically two to three times what you think your job will need.

### Example: Benchmarking fastqc

Let's say we want to run [fastq] in a directory with .fastq files. Our command will look something
like:

```bash
$ fastq name_of_fastq_file
```

But we need to figure out a good amount of resources to ask for this job. We might
also want the scheduler to email us when it's done. Hey, we've got things to do!

When you first start, it can be hard to know. It might be logical to submit a test
job and ask for more memory than you need

```bash
$ srun --time=1:00:00 --mem=8000 fastq name_of_fastq_file
```

The trick is using the command `sacct` when the job completes. Once it finishes,
we can query the scheduler to see how long our job took and what resources were used. We will use sacct to get statistics about our job. By itself, `sacct -u yourUsername` shows all commands that we ran since midnight on the previous day (we can change this behavior with the --start-time option).

```bash
[remote]$ sacct -u yourUsername        
JobID JobName Partition Account AllocCPUS State ExitCode ------------ ---------- ---------- ---------- ---------- ---------- -------- 
1964 bash standard default 1 COMPLETED 0:0 
1964.extern extern default 1 COMPLETED 0:0 
1964.0 bash default 1 COMPLETED 0:0 
1965 build-ind+ summer-sc+ default 1 COMPLETED 0:0 
1965.batch batch default 1 COMPLETED 0:0 
1965.extern extern default 1 COMPLETED 0:0    
```

The first number is the job-id, which slurm tells us when we submit the job. Also notice how we see
job numbers multiple timesS? There are actually multiple entries per job. Let's say that our test
run had id 1965. We can get detailed information about it by adding the `-j` (job id) parameter:

```bash
[remote]$ sacct -j 1965 -l    
```

It will show a ton of info, in fact, every single piece of info collected on your job by the scheduler. It may be useful to redirect this information to less to make it easier to view (use the left and right arrow keys to scroll through fields).

```bash
[remote]$ sacct -j 1965 -l | less    
sacct -o reqmem,maxrss,averss,elapsed,alloccpus -j 3413279
ReqMem MaxRSS AveRSS Elapsed AllocCpus
---------- ---------- ---------- ---------------------
16000Mc 1-20:54:49 4
16000Mc 4771852K 4603220K 1-20:54:49 4
...
```
