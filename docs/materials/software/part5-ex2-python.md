---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: #008; } </style>

Software Exercise 5.2: Using Python, Pre-Built
===============================================

In this exercise, you will install Python, package your installation, and then use it to run jobs. It should take about 20 minutes.

Background
----------

**Objective**: Install software (Python) to a folder and run it in a job using a wrapper script. 

**Why learn this?**: This is very similar to the [previous exercise](part5-ex1-prepackaged.md). 


Pre-Building
--------------------------------

The first step in our job process is building a Python installation that we can package up.

1.  Create a directory for this exercise on the Access Point and `cd` into it.
1.  Download the Python source code from <https://www.python.org/>. 

		:::console
		username@login $ wget https://www.python.org/ftp/python/3.10.5/Python-3.10.5.tgz

1.   First, we have to determine how to install Python to a specific location in our working directory.
    1.  Untar the Python source tarball (`tar -xzf Python-3.10.5.tgz`) and look at the `README.rst` file in the `Python-3.10.5` directory (`cd Python-3.10.5`).  You'll want to look for the "Build Instructions" header.  What will the main installation steps be?  What command is required for the final installation?  Once you've tried to answer these questions, move to the next step.
    1.  There are some basic installation instructions near the top of the `README`. Based on that short introduction, we can see the main steps of installation will be: 

			./configure
			make
			make test
			sudo make install

		This three-stage process (configure, make, make install) is a common  way to install many software packages.   The default installation  location for Python requires `sudo` (administrative privileges) to install. However, we'd like to install to a specific location in the working directory  so that we can compress that installation directory into a tarball. 

	1.   You can often use an option called `-prefix` with the `configure` script to change the default installation directory. Let's see if the Python `configure` script has this option by using the "help" option (as suggested in the `README.rst` file): 

			:::console
			username@host $ ./configure --help

		Sure enough, there's a list of all the different options that can be passed to the `configure` script, which includes `--prefix`.  (To see the `--prefix` option, you may need to scroll towards the top of the output.)  Therefore, we can use the  `$PWD` command in order to set the path correctly to a custom installation directory. 

1.  Now let's actually install Python!
    1.  **From the original working directory**, create a directory to hold the installation. 

			:::console
			username@host $ cd  ../
			username@host $ mkdir python310

	1.  Move into the `Python-3.10.5` directory and run the installation commands. These may take a few minutes each. 

			:::console
			username@host $ cd Python-3.10.5
			username@host $ ./configure --prefix=$PWD/../python310
			username@host $ make
			username@host $ make install

		!!! note
			The installation instructions in the `README.rst` file have a `make test` step 
			between the `make` and `make install` steps.  As this step isn't strictly necessary (and takes a long time), it's been omitted above.  

	1.  If I move back to the main job working directory, and look in the `python` subdirectory, I should see a Python installation. 

			:::console
			username@host $ cd ..
			username@host $ ls python310/
			bin  include  lib  share

	1.  I have successfully created a self-contained Python installation. Now it just needs to be tarred up! 

			:::console
			username@host $ tar -czf prebuilt_python.tar.gz python310/

1.  We might want to know how we installed Python for later reference.  Enter the following commands to save our history to a file: 

		:::console
		username@host $ history > python_install.txt

Python Script
-------------

1.  Create a script with the following lines called `fib.py`. 

		:::python
		import sys
		import os

		if len(sys.argv) != 2:
			print('Usage: %s MAXIMUM' % (os.path.basename(sys.argv[0])))
			sys.exit(1)
		maximum = int(sys.argv[1])
		n1 = n2 = 1
		while n2 <= maximum:
			n1, n2 = n2, n1 + n2
		print('The greatest Fibonacci number up to %d is %d' % (maximum, n1))

1. What command line arguments does this script take? Try running it on the submit server.

Wrapper Script
--------------

We now have our Python installation and our Python script - we just need to write a wrapper script to run them.

1.  What steps do you think the wrapper script needs to perform? Create a file called `run_fib.sh` and write them out in plain English before moving to the next step.
1.  Our script will need to
    1.  untar our `prebuilt_python.tar.gz` file
    1.  access the `python` command from our installation to run our `fib.py` script
1.  Try turning your plain English steps into commands that the computer can run.
1.  Your final `run_fib.sh` script should look something like this: 

		:::bash
		#!/bin/bash

		tar -xzf prebuilt_python.tar.gz 
		python310/bin/python3 fib.py 90

	or

		:::bash
		#!/bin/bash

		tar -xzf prebuilt_python.tar.gz 
		export PATH=$(pwd)/python310/bin:$PATH 
		python3 fib.py 90

1.  Make sure your `run_fib.sh` script is executable.

Submit File
-----------

1.  Make a copy of a previous submit file in your local directory (the submit file from 
the [Use a Wrapper Script exercise](part4-ex2-wrapper.md) might be a good candidate). What changes need to be made to run this Python job? 

1. Modify your submit file, then make sure you've included the key lines below: 

		:::file
		executable = run_fib.sh
		transfer_input_files = fib.py, prebuilt_python.tar.gz

1. Submit the job using `condor_submit`. 

1. Check the `.out` file to see if the job completed.

