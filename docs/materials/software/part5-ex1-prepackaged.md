---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: #008; } </style>

Software Exercise 5.1: Pre-package a Research Code
==========================================

**Objective**: Install software (HMMER) to a folder and run it in a job using a wrapper script. 

**Why learn this?**: If not using a container, this is a template for how to create 
a portable software installation using your own files, especially if the software 
is not available already compiled for Linux. 

Our Software Example
--------------------

For this exercise, we will be using the bioinformatics package HMMER. HMMER is a good example of software that is not compiled to a single executable; it has multiple executables as well as a helper library.

1.  Create a directory for this exercise on the Access Point. 

1.  Do an internet search to find the HMMER software downloads page and the 
installation instructions page. On the installation page, there are short instructions for how to install HMMER. There are two options shown for installation -- which should we use?

1. For the purposes of this example, we are going to use the instructions under the "Current version" heading, with the "Source" link. Download the HMMER source using wget.

1. Go back to the installation 
documentation page and look at the steps for compiling from source. This process 
should be similar to what was described in the lecture! 

Installation
------------

Normally, it is better to install software on a dedicated "build" server, but 
for this example, we are going to compile directly on the Access Point

1.  Before we follow the installation instructions, we should create a directory to hold our installation. You can create this in the current directory. 

		:::console
		username@host $ mkdir hmmer-build

1.  Now run the commands to unpack the source code: 

        :::console
		username@host $ tar -zxf hmmer-3.4.tar.gz
		username@host $ cd hmmer-3.4

1.  Now we can follow the second set of installation instructions. For the prefix, we'll use the variable `$PWD` to capture the name of our current working directory and then a relative path to the `hmmer-build` directory we created in step 1: 

		:::console
		username@host $ ./configure --prefix=$PWD/../hmmer-build
		username@host $ make
		username@host $ make install

1.  **Go back to the previous working directory**: 

		:::console
		username@host $ cd ..

	and confirm that our installation procedure created `bin`,  `lib`, and `share` directories in the `hmmer-build` folder: 

		:::console
		username@host $ ls hmmer-build
		bin share

1.  Now we want to package up our installation, so we can use it in other jobs. We can do this by compressing any necessary directories into a single gzipped tarball. 

		:::console
		username@host $ tar -czf hmmer-build.tar.gz hmmer-build

Note that we now have two tarballs in our directory -- the *source* tarball (`hmmer.tar.gz`), which we will no longer need and our newly built installation (`hmmer-build.tar.gz`) which is what we will actually be using to run jobs.

Wrapper Script
--------------

Now that we've created our portable installation, we need to write a script that opens and uses the installation, similar to the process we used in a [previous exercise](part4-ex2-wrapper.md). These steps should be performed back on the submit server (`ap1.facility.path-cc.io`).

1. Create a script called `run_hmmer.sh`. 

1.  The script will first need to untar our installation, so the script should start out like this:  

		:::bash
		#!/bin/bash

		tar -xzf hmmer-build.tar.gz

1.  We're going to use the same `$PWD` trick from the installation in order to tell the computer how to find HMMER. We will do this by setting the `PATH` environment variable, to include the directory where HMMER is installed: 

		:::bash
		export PATH=$PWD/hmmer-build/bin:$PATH

1.  Finally, the wrapper script needs to not only setup HMMER, but actually run the program. Add the following lines to your `run_hmmer.sh` wrapper script. 

        :::bash
		hmmbuild globins4.hmm globins4.sto
		hmmsearch -o search-results.txt globins4.hmm globins45.fa 

1.  Make sure the wrapper script has executable permissions: 

		:::console
		username@login $ chmod u+x run_hmmer.sh


Run a HMMER job
-------------------

We're almost ready! We need two more pieces to run a HMMER job.

1.  We're going to use some of the tutorial files provided with the HMMER download to 
run the job. You already have these files back in the directory where you unpacked the source code:

		:::console
		username@login $ ls hmmer-3.4/tutorial
		7LESS_DROME    fn3.hmm  globins45.fa  globins4.sto  MADE1.hmm  Pkinase.hmm
		dna_target.fa  fn3.sto  globins4.hmm  HBB_HUMAN     MADE1.sto  Pkinase.sto

	If you don't see these files, you may want to redownload the `hmmer.tar.gz` file and untar it here.

1.  Our last step is to create a submit file for our HMMER job. Think about which lines this submit file will need. Make a copy of a previous submit file (you could use the blast submit file from a [previous exercise](part4-ex2-wrapper.md) as a base) and modify it as you think necessary.

1.  The two most important lines to modify for this job are listed below; check them against your own submit file: 

        :::file
        executable = run_hmmer.sh
        transfer_input_files = hmmer-build.tar.gz, hmmer-3.4/tutorial/

    A wrapper script will always be a job's `executable`.
    When using a wrapper script, you must also always remember to transfer the software/source code using
    `transfer_input_files`.

    !!! note
        The `/` in the `transfer_input_files` line indicates that we are transferring the *contents* of that directory (which in this case, is what we want), rather than the directory itself.

1.  Submit the job with `condor_submit`.

1.  Once the job completes, it should produce a `search-results.txt` file.

	!!! note
		For a very similar compiling example, see this guide on how to 
		compile `samtools`: [Example Software Compilation](https://portal.osg-htc.org/documentation/htc_workloads/using_software/example-compilation/)
