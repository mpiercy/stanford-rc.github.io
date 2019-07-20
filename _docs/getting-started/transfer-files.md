---
title: Transferring Files
tags: 
 - transfer
---

# Transferring files

How do we download files to the cluster? How do we get data off of it? These
are common questions that we are going to discuss now. There are
several methods to do this from the command line.


## Grabbing files from the internet

To download files from the internet, the easiest tool to use is wget. The syntax is relatively straightforward: wget https://some/link/to/a/file.tar.gz We’ve actually done this before to download our example files:

```bash
[remote]$ wget https://hpc-carpentry.github.io/hpc-intro/files/bash-lesson.tar.gz
```

But as the name implies, this only works to _get_ files, and they must be in a web
accessible location. If we want to move things back and forth, we can use scp.
The syntax can be a little complex for new users, but we’ll break it down here.

To transfer *to* another computer:

```bash
[local]$ scp /path/to/local/file.txt yourUsername@remote.computer.address:/path/on/remote/computer
```

Or even simpler:

```bash
$ scp <path-local> <username>@<cluster>:<path-remote>
```

And then to download *from* another computer, it's just the reverse of that!

```bash
[local]$ scp yourUsername@remote.computer.address:/path/on/remote/computer/file.txt /path/to/local/
```

or more simply:

```bash
$ scp <username>@<cluster>:<path-remote> <path-local> 
```

In the example above, notice how we are targeting a directory? That means that the file will be placed
inside the directory. The command works similar to copy. Also note that for the local paths,
they can be relative to your present working directory.

{% include alert.html title="Remote paths are relative to home" content="You can simplify the remote path greatly if you know that the paths are relative to home. Thus, instead of</br>
```bash
$ scp <username>@<cluster>:/home/dinosaur/scripts/magic.sh <path-local> 
```
I could instead do:
```bash
$ scp <username>@<cluster>:scripts/magic.sh <path-local> 
```"%}

This means that a colon without anything would plop the file into the root of your home directory:

```bash
$ scp <path-local> <username>@<cluster>: 
```

To copy recursively, just add the `-r` flag.

```bash
[local]$ scp -r some-local-folder/ yourUsername@remote.computer.address:target-directory/
```

## Transferring files interactively with sftp

scp is useful, but what if we don’t know the exact location of what we want to transfer? Or perhaps we’re simply not sure which files we want to transfer yet. sftp is an interactive way of downloading and uploading files. Let’s connect to a cluster, using sftp- you’ll notice it works the same way as SSH:

```bash
sftp yourUsername@remote.computer.address    
```

### Help
This will start what appears to be a bash shell (though our prompt says sftp>). However we only have access to a limited number of commands. We can see which commands are available with help:

```
sftp> help       
Available commands:
bye                                Quit sftp
cd path                            Change remote directory to 'path'
chgrp grp path                     Change group of file 'path' to 'grp'
chmod mode path                    Change permissions of file 'path' to 'mode'
chown own path                     Change owner of file 'path' to 'own'
df [-hi] [path]                    Display statistics for current directory or
                                   filesystem containing 'path'
exit                               Quit sftp
get [-afPpRr] remote [local]       Download file
reget [-fPpRr] remote [local]      Resume download file
reput [-fPpRr] [local] remote      Resume upload file
help                               Display this help text
lcd path                           Change local directory to 'path'
lls [ls-options [path]]            Display local directory listing
lmkdir path                        Create local directory
ln [-s] oldpath newpath            Link remote file (-s for symlink)
lpwd                               Print local working directory
ls [-1afhlnrSt] [path]             Display remote directory listing
...
```

Notice the presence of multiple commands that make mention of local and remote. We are actually connected to two computers at once (with two working directories!).

### Working Directories
To show our remote working directory:

```bash
sftp> pwd
Remote working directory: /global/home/yourUsername
```

To show our local working directory, we add an l in front of the command:

```bash
sftp> lpwd       
Local working directory: /home/jeff/Documents/teaching/hpc-intro    
```

The same pattern follows for all other commands:

* ls shows the contents of our remote directory, while lls shows our local directory contents.
* cd changes the remote directory, lcd changes the local one.

### Upload a File
To upload a file, we type put some-file.txt (tab-completion works here).

```bash
sftp> put config.toml       
Uploading config.toml to /global/home/yourUsername/config.toml config.toml 100% 713 2.4KB/s 00:00    
```

### Download a File

To download a file we type get some-file.txt:

```bash
sftp> get config.toml       
Fetching /global/home/yourUsername/config.toml to config.toml /global/home/yourUsername/config.toml 100% 713 9.3KB/s 00:00    
```

And we can recursively put/get files by just adding -r. Note that the directory needs to be present beforehand.

```bash
sftp> mkdir content 
sftp> put -r content/
```
```bash
Uploading content/ to /global/home/yourUsername/content
Entering content/
content/scheduler.md              100%   11KB  21.4KB/s   00:00
content/index.md                  100% 1051     7.2KB/s   00:00
content/transferring-files.md     100% 6117    36.6KB/s   00:00
content/.transferring-files.md.sw 100%   24KB  28.4KB/s   00:00
content/cluster.md                100% 5542    35.0KB/s   00:00
content/modules.md                100%   17KB 158.0KB/s   00:00
content/resources.md              100% 1115    29.9KB/s   00:00
```

To quit, we type exit or bye.


## Compressing files

Sometimes we will want to compress files ourselves to make file transfers easier. The larger the file, the longer it will take to transfer. Moreover, we can compress a whole bunch of little files into one big file to make it easier on us (no one likes transferring 70000 little files)!

The two compression commands we’ll probably want to remember are the following:

Compress a single file with Gzip:

```bash
$ gzip filename
```

Compress a lot of files/folders with Gzip:

```bash
$ tar -czvf archive-name.tar.gz folder1 file2 folder3 
```
