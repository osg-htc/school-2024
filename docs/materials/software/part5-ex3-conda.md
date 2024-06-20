---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: #008; } </style>

Software Exercise 5.3: Using Conda Environments
====================================

**Objective**: Create a portable conda environment and use it in a job. 

**Why learn this?**: If you normally use `conda` to manage your Python environments, 
this method of software portability offers great similarity to your usual practices. 

Introduction
------------

Many Python users manage their Python installation and environments with either the
`Anaconda` or `miniconda` distributions. These distribution tools are great 
for creating portable Python installations and can be used on HTC systems with 
some help from a tool called `conda pack`. 

Sample Script
-------------------

1. For this example, create a script called `rand_array.py` on the Access Point. 

		:::file
		import numpy as np

		#numpy array with random values
		a = np.random.rand(4,2,3)

		print(a)

To run this script, we will need a copy of Python with the `numpy` library. 

Create and Pack a Conda Environment
------------------

(For a generic version of these instructions, see the [CHTC User Guide](http://chtc.cs.wisc.edu/conda-installation))

1. Our first step is to create a miniconda installation on the submit server. 
	1. You should be logged into whichever server you made the `rand_array.py` script on. 
	2. Download the latest Linux [miniconda installer](https://docs.conda.io/en/latest/miniconda.html)

			:::console
			user@login $ wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh 

	3. Run the installer to install miniconda; you'll need to accept the license terms and 
	you can use the default installation location: 

			:::console	
			[user@login]$ sh Miniconda3-latest-Linux-x86_64.sh

	4. At the end, you can choose whether or 
	not to "initialize Miniconda3 by running conda init?" The default is no; you would 
	then run the `eval` command listed by the installer to "activate" Miniconda. If you
	choose "no" you'll want to save this command so that you can reactivate the 
	Miniconda installation when needed in the future.

2. Next we'll create our conda "environment" with `numpy` (we've called the environment "py3-numpy"): 

		:::console
		(base)[user@login]$ conda create -n py3-numpy
		(base)[user@login]$ conda activate py3-numpy
		(py3-numpy)[user@login]$ conda install -c conda-forge numpy

3. Once everything is installed, deactivate the environment to go back to the 
Miniconda "base" environment.

		:::console
		(py3-numpy)[user@login]$ conda deactivate

4. We'll now install a tool that will pack up the just created conda environment 
so we can run it elsewhere. Make sure that your job's Miniconda environment is created, but deactivated, so 
that you're in the "base" Miniconda environment, then run: 

		:::console
		(base)[user@login]$ conda install -c conda-forge conda-pack

	Enter `y` when it asks you to install. 

5. Finally, we will run the `conda pack` command, which will automatically create a 
tar.gz file with our environment: 

		:::console
		(base)[user@login]$ conda pack -n py3-numpy

Submit a Job
-------------

1. The executable for this job will need to be a wrapper script. What steps do you 
think need to be included? Write down a rough draft, then compare with the following script. 

3. Create a wrapper script like the following: 

		:::file
		#!/bin/bash

		set -e
		
		export PATH
		mkdir py3-numpy
		tar -xzf py3-numpy.tar.gz -C py3-numpy
		. py3-numpy/bin/activate

		python3 rand_array.py

4. What needs to be included in your submit file for the job to run successfully? Try 
yourself and then check the suggestions in the next point. 

5. In your submit file, make sure to have the following: 
	- Your executable should be the the bash script you created in the previous step. 
	- Remember to transfer your Python script and the environment `tar.gz` file via
	 `transfer_input_files`. 

6. Submit the job and see what happens!
