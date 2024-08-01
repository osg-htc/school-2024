---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Software Exercise 1.1: Run and Explore Containers
============================================================

**Objective**: Run a container interactively

**Why learn this?**: Being able to run a container directly allows you to confirm 
what is installed and whether any additional scripts or code will work in the context 
of the container. 

Setup
--------

Make sure you are logged into `ap40.uw.osg-htc.org`.  For this exercise 
we will be using Apptainer containers maintained by OSG staff or existing 
containers on Docker Hub. 

We will set two environment variables that will help lighten the load on the 
Access Point as we work with containers: 

	:::console
	$ mkdir ~/apptainer_cache
	$ export APPTAINER_CACHEDIR=$HOME/apptainer_cache
	$ export TMPDIR=$HOME/apptainer_cache

Exploring Apptainer Containers
-------------------

First, let's try to run a container from the [OSG-Supported List](https://portal.osg-htc.org/documentation/htc_workloads/using_software/available-containers-list/). 

1. Find the full path for the `ubuntu 22.04` container image. 

1. To run it, use this command: 

		:::console
		$ apptainer shell /cvmfs/singularity.opensciencegrid.org/htc/ubuntu:22.04

	It may take a few minutes to start - don't worry if this happens. 

1. Once the container starts, the prompt will change to either `Singularity>` or 
  `Apptainer>`. Run `ls` and `pwd`. Where are you? Do you see your files? 

1. The `apptainer shell` command will automatically connect your home directory to 
the running container so you can use your files. 

1. How do we know we're in a different Linux environment? Try printing out the Linux 
version, or checking the version of common tools like `gcc` or Python: 

		:::console
		$ cat /etc/os-release
		$ gcc --version
		$ python3 --version

1. Exit out of the container by typing `exit`. 

1. Type the same commands back on the normal Access Point. Should they give the same 
results as when typed in the container, or different? 

		:::console
		$ cat /etc/os-release
		$ gcc --version
		$ python3 --version

Exploring Docker Containers
------------------

The process for interactively running a Docker container will be very 
similar to an apptainer container. The main difference is a `docker://` prefix 
before the container's identifying name. 

1. We are going to be using a [Python image from Docker Hub](https://hub.docker.com/_/python). 
Click on the "Tags" tab to see all the different versions of this container that exists. 

1. Let's use version `3.10`. To run it interactively, use this command: 

		:::console
		$ apptainer shell docker://python:3.10

1. Once the container starts and the prompt changes, try running similar commands 
as above. What version of Linux is used in this container? Does the version of Python 
match what you expect, based on the name of the container? 

1. Once done, type `exit` to leave the container. 
