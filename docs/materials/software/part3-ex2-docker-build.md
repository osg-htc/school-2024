---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: #008; } </style>

Software Exercise 3.2: Build Your Own Docker Container (Optional)
====================================

**Objective**: Build a custom Docker container with `numpy` and use it in a job

**Why learn this?**: Docker containers can be run on both your laptop and OSPool. DockerHub
also provides a convenient platform for sharing containers. If you want to use a custom 
container, run across platforms, and/or share a container amongst a group, building in 
Docker first is a good approach. 

Python Script
-------------------

1. For this example, create a script called `rand_array.py` on the Access Point. 

		:::file
		import numpy as np

		#numpy array with random values
		a = np.random.rand(4,2,3)

		print(a)

To run this script, we will need a copy of Python with the `numpy` library. 
This exercise will walk you through the steps to build your own Docker container 
based on Python, with the `numpy` Python library added on. 

Getting Set Up
--------------

Before building your own Docker container, you need to go through the following 
set up steps: 

1. Install Docker Dekstop on your computer. 
	* [Docker Desktop page](https://www.docker.com/products/docker-desktop)

2. You may need to create a Docker Hub user name to download Docker Desktop; if not 
created at that step, create a user name for [Docker Hub](https://hub.docker.com/) now.

3. (Optional): Once Docker is up and running on your computer, you are welcome to take 
some time to explore the basics of downloading and running a container, as shown in 
the initial sections of this Docker lesson:
	* [Introduction to Docker](https://carpentries-incubator.github.io/docker-introduction/)
	However, this isn't strictly necessary for building your own container. 

Building a Container
--------------------

In order to make our container reproducible, we will be using Docker's capability 
to build a container image from a specification file.  

1. First, create an empty build directory on **your computer**, not the Access Points. 

1. In the build directory, create a file called `Dockerfile` (no file extension!) with 
the following contents: 

		:::file
		# Start with this image as a "base".
		# It's as if all the commands that created that image were inserted here.
		# Always use a specific tag like "4.10.3", never "latest"!
		# The version referenced by "latest" can change, so the build will be 
		# more stable when building from a specific version tag. 
		FROM continuumio/miniconda3:4.10.3

		# Use RUN to execute commands inside the image as it is being built up.
		RUN conda install --yes numpy

		# RUN multiple commands together.
		# Try to always "clean up" after yourself to reduce the final size of your image.
		RUN apt-get update \
 		&& apt-get --yes install --no-install-recommends graphviz \
 		&& apt-get --yes clean \
 		&& rm -rf /var/lib/apt/lists/*

	This is our specification file and provides Docker with the information it needs 
	to build our new container. There are other options besides `FROM` and `RUN`; see 
	the [Docker documentation](https://docs.docker.com/engine/reference/builder/) for more information. 

1. Note that our container is starting from an existing container 
`continuumio/miniconda3:4.10.3`. This container is produced by the `continuumio` 
organization; the number `4.10.3` indicates the container version. When we create our 
new container, we will want to use a similar naming scheme of: 

		USERNAME/CONTAINER:VERSIONTAG

	In what follows, you will want to replace `USERNAME` with your DockerHub user name. 
	The `CONTAINER` name and `VERSIONTAG` are your choice; in what follows, we will 
	use `py3-numpy` as the container name and `2024-08` as the version tag. 

1. To build and name the new container, open a command line window on your computer 
where you can run Docker commands. Use the `cd` command to change your working directory 
to the build directory with the `Dockerfile` inside. 

		:::console
		$ docker build -t USERNAME/py3-numpy:2024-08 .
		
	Note the `.` at the end of the command! This indicates that we're using the current 
	directory as our build environment, including the `Dockerfile` inside. 

Upload Container and Submit Job
-------------------------------

Right now the container image only exists on your computer. To use it in CHTC or 
elsewhere, it needs to be added to a public registry like Docker Hub. 

1. To put your container image in Docker Hub, use the `docker push` command on the 
command line:

		:::console
		$ docker push USERNAME/py3-numpy:2024-08

	If the push doesn't work, you may need to run `docker login` first, enter your 
	Docker Hub username and password and then try the push again. 

1. Once your container image is in DockerHub, you can use it in jobs as described 
in [Exercise 1.3](part1-ex3-docker-jobs.md). 

> Thanks to [Josh Karpel](https://github.com/JoshKarpel/osg-school-example-dockerfile) for 
providing the original sample `Dockerfile`!
