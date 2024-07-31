---
status: testing
---

<style type="text/css">
  pre em { font-style: normal; background-color: yellow; }
  pre strong { font-style: normal; font-weight: bold; color: \#008; }
</style>

Software Exercise 1.5 - Choose Software Options
============================================================

**Objective**: Decide how you want to make your software portable

**Why learn this?**: This is the next step to getting your own 
research jobs running on the OSPool!

Know Your Software
------------------

Pick at least one software you want to use on the OSPool as a test subject. Then: 

1. Find the download and/or installation page and read through the instructions 
and options there. 

1. Is the software available as a binary download, or will you need to run some 
kind of command to install it or compile it from source?  If there are 
multiple download/installation options, which is which? 

1. What pre-requisites does this software need to be installed? 
	
	> Example 1: an R package will require a base R installation
	> 
	> Example 2: some codes require that a library called the "Gnu Scientific
	Library (GSL) be already installed on your computer"

Choose a Strategy
------------------

1. Are there any existing containers that contain this software already? 
	* Explore [OSG-Supported Containers](https://portal.osg-htc.org/documentation/htc_workloads/using_software/available-containers-list/)
	* Explore [DockerHub](https://hub.docker.com/), for example: 
		* [miniconda](https://hub.docker.com/u/continuumio)
		* [rocker](https://hub.docker.com/u/rocker)
		* [jupyter](https://hub.docker.com/u/jupyter)
		* [nvidia](https://hub.docker.com/u/nvidia)
		* (and many more!)
  If yes, try using this container first, as shown in [Exercise 1.2](part1-ex2-apptainer-jobs.md) and [Exercise 1.3](part1-ex3-docker-jobs.md)

1. Is there a simple download or easy compilation process? If so, can you 
 download the software and use it via a wrapper script? See the exercises from 
 Part 4 ([Download Software Files](part4-ex1-download.md), 
 [Use a Wrapper Script](part4-ex2-wrapper.md), 
 [Wrapper Script Arguments](part4-ex3-arguments.md)). To learn more about using 
 this approach for specific softwares, see the examples in [Part 5](../index.md#software-exercises-5-compiled-software-examples-optional). 

1. Are you using conda? See the specific example in [Exercise 5.3](part5-ex3-conda.md)

1. If neither of the above options works (which may be true for more software!), you 
  may want to build your own container. 
    1. If you want to just use this container on the OSPool, build an 
    Apptainer container as described in [Exercise 1.4](part1-ex4-apptainer-build.md) and 
    with more information in [Exercise 3.1](part3-ex1-apptainer-recipes.md)
    1. If you want to use the container on your own computer or share with 
    others who would use it on a laptop or desktop, look at the Docker container 
    example in [Exercise 3.2](part3-ex2-docker-build.md). 

Don't do ALL of the software exercises in parts 3 - 5! Instead, choose the section(s) 
that makes sense based on how you want to manage your software. Talk to the School 
instructors to help make this decision if you are unsure. 

Create an Executable
---------------------

Regardless of which approach you use, check out 
the [Build an HTC-Friendly Executable](part2-ex1-build-executable.md) exercise
for some tips on how to make your script more robust and easy to use with multiple jobs. 