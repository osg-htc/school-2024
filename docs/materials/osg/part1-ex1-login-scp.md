---
status: testing
---

# OSG Exercise 1.1: Log In to the OSPool Access Point

The main goal of this exercise is to log in to an Open Science Pool Access Point
so that you can start submitting jobs into the OSPool.
But before doing that, you will first prepare a file on Monday&lsquo;s Access Point to copy to the OSPool Access Point.
Then you will learn how to efficiently copy files between the Access Points.

If you have trouble getting `ssh` access to the OSPool Access Point, ask the instructors right away!
Gaining access is critical for all remaining exercises.

## Part 1: On the PATh Access Point

The first few sections below are to be completed on `ap1.facility.path-cc.io`, the PATh Access Point.
This is still the same Access Point you have been using since yesterday.

## Preparing files for transfer

When transferring files between computers, it’s best to limit the number of files as well as their size.
Smaller files transfer more quickly and, if your network connection fails,
restarting the transfer is less painful than it would be if you were transferring large files.

Archiving tools (WinZip, 7zip, Archive Utility, etc.) can compress the size of your files
and place them into a single, smaller archive file.
The Unix `tar` command is a one-stop shop for creating, extracting, and viewing the contents of `tar` archives
(called *tarballs*).
Its usage is as follows:

-   To **create** a tarball named `<archive filename>` containing `<archive contents>`, use the following command:

        :::console
        $ tar -czvf <archive filename> <archive contents>

    Where `<archive filename>` should end in `.tar.gz` and `<archive contents>` can be a list of any number of files
    and/or folders, separated by spaces.

-   To **extract** the files from a tarball into the current directory:

        :::console
        $ tar -xzvf <archive filename>

-   To **list** the files within a tarball:

        :::console
        $ tar -tzvf <archive filename>

### Comparing compressed sizes

You can adjust the level of compression of `tar` by prepending your command with `GZIP=--<COMPRESSION>`, where
`<COMPRESSION>` can be either `fast` for the least compression, or `best` for the most compression (the default
compression is between `best` and `fast`).

While still logged in to `ap1.facility.path-cc.io`:

1.  Create and change into a new folder for this exercise, for example `osg-ex11`
1.  Use `wget` to download the following files from our web server:
    1.  Text file: <http://proxy.chtc.wisc.edu/SQUID/osgschool21/random_text>
    1.  Archive: <http://proxy.chtc.wisc.edu/SQUID/osgschool21/pdbaa.tar.gz>
    1.  Image: <http://proxy.chtc.wisc.edu/SQUID/osgschool21/obligatory_cat.jpg>
1.  Use `tar` on each file and use `ls -l` to compare the sizes of the original file and the compressed version.

Which files were compressed the least?  Why?

## Part 2: On the Open Science Pool Access Point

For many of the remaining exercises, you will be using an OSPool Access Point,
`ap40.uw.osg-htc.org`,
which submits jobs into the OSPool.

To log in to the OSPool Access Point,
use the same username (and SSH key, if you did that) as on `ap1`.
If you have any issues logging in to `ap40.uw.osg-htc.org`,
please ask for help right away!

So please `ssh` in to the server and take a look around:

1.  Log in using `ssh USERNAME@ap40.uw.osg-htc.org` (substitute your own username)
1.  Try some Linux and HTCondor commands; for example:
    *   Linux commands: `hostname`, `pwd`, `ls`, and so on
    *   What is the operating system? `uname` and (in this case) `cat /etc/redhat-release`
    *   HTCondor commands: `condor_version`, `condor_q`, `condor_status -total`

## Transferring files

In the next exercise, you will submit the same kind of job as in the previous exercise.
Wouldn’t it be nice to copy the files instead of starting from scratch?
And in general, being able to copy files between servers is helpful, so let’s explore a way to do that.

### Using secure copy

[Secure copy](https://en.wikipedia.org/wiki/Secure_copy) (`scp`) is a command based on SSH
that lets you securely copy files between two different servers.
It takes similar arguments to the Unix `cp` command but also takes additional information about servers.
Its general form is like this:

```console
scp <source 1> <source 2>...<source N> [username@]<remote server>:<remote path>
```

`<remote path>` may be omitted if you want to copy your sources to your remote home directory
and `[username@]` may be omitted if your usernames are the same across both servers.
For example, if you are logged in to `ap40.uw.osg-htc.org`
and wanted to copy the file `foo` from your current directory
to your home directory on `ap1.facility.path-cc.io`,
and if your usernames are the same on both servers,
the command would look like this:

```console
$ scp foo ap1.facility.path-cc.io:
```

Additionally, you could *pull* files from `ap1.facility.path-cc.io` to `ap40.uw.osg-htc.org`.
The following command copies `bar` from your home directory on `ap1.facility.path-cc.io`
to your current directory on `ap40.uw.osg-htc.org`;
and in this case, the username for `ap1` is specified:

``` console
$ scp USERNAME@ap1.facility.path-cc.io:bar .
```

Also, you can copy folders between servers using the `-r` option.
If you kept all your files from the HTCondor exercise 1.3 in a folder named `htc-1.3` on `ap1.facility.path-cc.io`,
you could use the following command to copy them to your home directory on `ap40.uw.osg-htc.org`:

``` console
$ scp -r USERNAME@ap1.facility.path-cc.io:htc-1.3 .
```

### Secure copy to your laptop

During your research, you may need to transfer output files
from your submit server to inspect them on your personal computer,
which can also be done with `scp`!
To use `scp` on your laptop, follow the instructions relevant to your computer‘s operating system:

#### Mac and Linux users

`scp` should be included by default and available via the terminal on both Mac and Linux operating systems.

#### Windows users

WinSCP is an `scp` client for Windows operating systems. Install WinSCP from <https://winscp.net/eng/index.php>

# Next exercise

Once completed, move onto the next exercise: [Running jobs in the OSG](part1-ex2-submit-osg.md)
