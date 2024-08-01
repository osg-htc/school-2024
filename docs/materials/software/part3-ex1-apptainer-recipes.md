---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Software Exercise 3.1: Create an Apptainer Definition File
============================================================

**Objective**: Describe each major section of an Apptainer Definition file. 

**Why learn this?**: When building your own containers, it is helpful to understand 
the basic options and syntax of the "build" or definition file. 

| Section | 
|---------|
| [Bootstrap/From](#where-to-start) | 
| [%files](#files-needed-for-building-or-running) | 
| [%post](#commands-to-install) | 
| [%env](#environment) | 


Where to start
---------------

	:::file
	Bootstrap: docker
	From: hub.opensciencegrid.org/htc/ubuntu:22.04

A custom container always is always built on an existing container. It is common 
to use a container on Docker Hub, or in this case, hub.opensciencegrid.org.
These lines tell Apptainer to pull the  pre-existing image from the hub, and to
use it as the base for the  container that will be built using this definition file.

When choosing a base container, try to find one that has most of what you need - for 
example, if you want to install R packages, try to find a container that already 
has R installed. 


Files needed for building or running
---------------

	:::file
	%files
	  source_code.tar.gz /opt
	  install.R

If you need specific files for the installation (like source code) or 
for the job to execute (like small data files or scripts), they can be 
copied into the container under the `%files` section.  The first item on a line 
is what to copy (from your computer) and the optional second item is where it 
should be copied in the container. 

Normally the files being copied are in your local working directory where 
you run the build command. 

Commands to install
---------------

	:::file
	%post
	  apt-get update -y
      apt-get install -y \
            build-essential \
            cmake \
            g++ \
            r-base-dev
      install2.r tidyverse

This is where most of the installation happens. You can use any shell command here 
that will work in the base container to install software. These commands might include: 
- Linux installation tools like `apt` or `yum`
- Scripting specific installers like `pip`, `conda` or `install.packages()`
- Shell commands like `tar`, `configure`, `make`

Different distributions of Linux often have distinct sets of tools for installing software. The installers for various common Linux distributions are listed below:
    Ubuntu: apt or apt-get
    Debian: deb
    CentOS: yum 
    
A web search for “install X on Y Linux” is usually a good start for common software installation tasks. [^1]

When installing to a custom location, do *not* install to a `home` directory. This is 
likely to get overwritten when the container is run. Instead, `/opt` is the best 
directory for custom installations. 

Environment
---------------

	:::file
	%environment
	  PATH=/opt/mycode/bin:$PATH
	  JAVA_HOME=/opt/java-1.8

To set environment variables (especially useful for software in a custom location), 
use the `%environment` section of the definition file. 

[^1]: This text and previous list taken from [Introduction to Docker](https://carpentries-incubator.github.io/docker-introduction/)
