---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Software Exercise 1.2: Use Apptainer Containers in OSPool Jobs
============================================================

**Objective**: Submit a job that uses an existing apptainer container; compare default 
job environment with a specific container job environment. 

**Why learn this?**: By comparing a non-container and container job, you'll better 
understand what a container can do on the OSPool. This may also be how you end up 
submitting your jobs if you can find an existing apptainer container with your software. 


Default Environment
-------------------

First, let's run a job without a container to see what the typical job environment is. 

1. Create a bash script with the following lines: 

		:::bash
		#!/bin/bash
	
		hostname
		cat /etc/os-release 
		gcc --version
		python3 --version
	
	This will print out the version of Linux on the computer, the version 
	of `gcc`, a common software compiler, and the version of Python 3. 

1. Make the script executable:

		:::console
		$ chmod +x script.sh

1. Run the script on the Access Point.

		:::console
		$ ./script.sh

	What results did you get? 

1. Copy a submit file from a previous OSPool job and edit it so that the 
script you just wrote is the executable. 

1. Submit the job and read the standard output file when it completes. What version 
of Linux was used for the job? What is the version of `gcc` or Python? 

Container Environment
---------------------

Now, let's try running that same script inside a container. 

1. For this job, we will use the OSG-provided Ubuntu "Focal" image, as we did in the previous exercise. The `container_image` submit file option will tell HTCondor to use this container for the job: 

		:::file
		universe = container
		container_image = /cvmfs/singularity.opensciencegrid.org/htc/ubuntu:22.04
   
1. If the submit file you copied has something like `requirements = (OSGVO_OS_STRING == "RHEL 9")`, remove that. When you use containers, you should not specify an OS in the requirements as that will unnecessarily limit the number of resources you can run on.

1. Submit the job and read the standard output file when it completes. 
What version of Linux was used for the job? What is the version of `gcc`? or Python? 

Experimenting With Other Containers
-------------

1. Look at the list of OSG-Supported containers: [OSG Supported Containers](https://portal.osg-htc.org/documentation/htc_workloads/using_software/available-containers-list/)

1. Try submitting a job that uses one of these containers. Change the executable 
script to explore different aspects of that container. 
