---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

HTC Exercise 1.1: Log In and Look Around
===========================================

Background
----------

There are different High Throughput Computing (HTC) systems at universities, government facilities, and other institutions around the world, and they may have different user experiences. For example, some systems have dedicated resources (which means your job will be guaranteed a certain amount of resources/time to complete), while other systems have opportunistic, backfill resources (which means your job can take advantage of some resources, but those resources could be removed at any time). Other systems have a mix of dedicated and opportunistic resources. 

Durring the OSG School, you will practice on two different HTC systems: the "[PATh Facility](https://path-cc.io/facility/)" and "[OSG's Open Science Pool (OSPool)](https://osg-htc.org/services/open_science_pool.html)". This will help prepare you for working on a variety of different HTC systems. 

* PATh Facility: The PATh Facility provides researchers with **dedicated HTC resources and the ability to run larger and longer jobs**. The HTC execute pool is composed of approximately 30,000 cores and 36 A100 GPUs. 
* OSG's Open Science Pool: The OSPool provides researchers with **opportunitistic resources and the ability to run many smaller and shorter jobs silmnulatinously**. The OSPool is composed of approximately 60,000+ cores and dozens of different GPUs. 

Exercise Goal 
---

The goal of this first exercise is to log in to the PATh Facility access point and look around a little bit, which will take only a few minutes. 

**If you have trouble getting SSH access to the submit server, ask the instructors right away! Gaining access is critical for all remaining exercises.**

Logging In
----------

Today, you will use a High Throughput Computing system known as the "[PATh Facility](https://path-cc.io/facility/)". The PATh Facility provides users with dedicated resources and longer runtimes than OSG's Open Science Pool. 


You will login to the access point of the PATh Facility, which is called `ap1.facility.path-cc.io` using the username you previously created. 

To log in, use a [Secure Shell](http://en.wikipedia.org/wiki/Secure_Shell) (SSH) client.

-   From a Mac or Linux computer, start the Terminal app and run the below `ssh` command, replacing <USERNAME\> with your username:

``` hl_lines="1"
$ ssh <USERNAME>@ap1.facility.path-cc.io
```
    
-   On Windows, we recommend a free client called [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/),
    but any SSH client should be fine.

**If you need help finding or using an SSH client, ask the instructors for help right away**!

Running Commands
----------------

In the exercises, we will show commands that you are supposed to type or copy into the command line, like this:

``` console
username@ap1 $ hostname
path-ap2001
```

!!! note
    In the first line of the example above, the `username@ap1 $` part is meant to show the Linux command-line prompt.
    You do not type this part! Further, your actual prompt probably is a bit different, and that is expected.
    So in the example above, the command that you type at your own prompt is just the eight characters `hostname`.
    The second line of the example, without the prompt, shows the output of the command; you do not type this part,
    either.

Here are a few other commands that you can try (the examples below do not show the output from each command):

``` console
username@ap1 $ whoami
username@ap1 $ date
username@ap1 $ uname -a
```

A suggestion for the day: try typing into the command line as many of the commands as you can.
Copy-and-paste is fine, of course, but **you WILL learn more if you take the time to type each command yourself.**

Organizing Your Workspace
-------------------------

You will be doing many different exercises over the next few days, many of them on this access point. Each exercise may use many files, once finished. To avoid confusion, it may be useful to create a separate directory for each exercise.

For instance, for the rest of this exercise, you may wish to create and use a directory named `intro-1.1-login`, or something like that.

``` console
username@ap1 $ mkdir intro-1.1-login
username@ap1 $ cd intro-1.1-login
```

Showing the Version of HTCondor
-------------------------------

HTCondor is installed on this server. But what version? You can ask HTCondor itself:

``` console
username@ap1 $ condor_version
$CondorVersion: 23.9.0 2024-06-27 BuildID: 742143 PackageID: 23.9.0-0.742143 GitSHA: 68fde429 RC $
$CondorPlatform: x86_64_AlmaLinux8 $
```

As you can see from the output, we are using HTCondor 10.7.0.


Reference Materials
-------------------

Here are a few links to reference materials that might be interesting after the school (or perhaps during).

-   [HTCondor manuals](https://htcondor.readthedocs.io/en/latest/); it is probably best to read the manual corresponding to the version of HTCondor that you use. That link points to the latest version of the manual, but you can switch versions using the toggle in the lower left corner of that page.
