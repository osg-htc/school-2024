---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Software Exercise 4.1: Using a Pre-compiled Binary
===================================================

**Objective**: Identify software that can be downloaded; download it and use it to run a job. 

**Why learn this?**: Some software doesn't require much "installation" - you can just 
download it and run. Recognizing when this is possible can save you time. 

Our Software Example
--------------------

The software we will be using for this example is a common tool for
aligning genome and protein sequences against a
reference database, the BLAST program.

1.  Search the internet for the BLAST software.  Searches might include
"blast executable or "download blast software".  Hopefully these
searches will lead you to a BLAST website page that looks like this:

    ![BLAST landing page](files/part1-ex1-blast-landing-page.png)

1.  Click on the title that says ["Download
BLAST"](files/part1-ex1-blast-front-page.png) and then look for the
link that has the [latest installation and source
code](files/part1-ex1-blast-dl-page.png).  

	This will either open a page in a web browser that looks like this: 
	
	![Download page](files/part1-ex1-blast-dl-list.png)

	Or you will be asked to open the link in your file browser (choose the 
	Connect as Guest option): 
	
	![Download Folder](files/part1-ex1-blast-dl-folder.png)

	In either case, you should end up on a
	page with a list of each version of BLAST that is available for
	different operating systems.

1.  We could download the source and compile it ourselves, but instead,
we're going to use one of the pre-built binaries.  **Before proceeding,
look at the list of downloads and try to determine which one you want.**

1.  Based on our operating system, we want to use the Linux binary,
which is labelled with the `x64-linux` suffix. 

	![BLAST download page](files/part1-ex1-blast-dl-list-linux.png)

	![BLAST download folder](files/part1-ex1-blast-dl-folder-linux.png)

	All the other links are either for source code or other operating
systems. 
	
1. On the Access Point, create a directory for
this exercise. Then download the appropriate `tar.gz` file and un-tar/decompress it
it. If you want to do this all from the command line, the sequence will 
look like this (using `wget` as the download command.) 

        :::console
        user@login $ wget https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/2.15.0/ncbi-blast-2.15.0+-x64-linux.tar.gz
        user@login $ tar -xzf ncbi-blast-2.15.0+-x64-linux.tar.gz

1.  We're going to be using the `blastx` binary in our job. Where is it
in the directory you just decompressed?

Copy the Input Files
--------------------

To run BLAST, we need an input file and reference database. For this
example, we'll use the "pdbaa" database, which contains sequences for
the protein structure from the Protein Data Bank. For our input file,
we'll use an abbreviated fasta file with mouse genome information.

1.  Download these files to your current directory: 

        :::console
        username@login $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/pdbaa.tar.gz
        username@login $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/mouse.fa

1.  Untar the `pdbaa` database: 

        :::console
        username@login $ tar -xzf pdbaa.tar.gz


Submitting the Job
------------------

We now have our program (the pre-compiled `blastx` binary) and our input
files, so all that remains is to create the submit file. The form of a
typical `blastx` command looks something like this:

```file
blastx -db <database_dir/prefix> -query <input_file> -out <output_file>
```

1.   Copy a submit file from one of the Day 1 exercises or previous 
software exercises to use for this exercise. 

1. Think about which lines you will need to change or add to your submit
file in order to submit the job successfully. In particular:    
	-   What is the executable?
	-   How can you indicate the entire command line sequence above?
	-   Which files need to be transferred in addition to the
executable?
	-   Does this job require a certain type of operating system?
	-   Do you have any idea how much memory or disk to request?

1. Try to answer these questions and modify your submit file
appropriately.

1. Once you have done all you can, check your submit file against the
lines below, which contain the exact components to run this particular
job.

    * The executable is `blastx`, which is located in the `bin`
directory of our downloaded BLAST directory. We need to use the
`arguments` line in the submit file to express the rest of the command. 
    
            :::file
            executable = ncbi-blast-2.15.0+/bin/blastx
            arguments = -db pdbaa/pdbaa -query mouse.fa -out results.txt

    * The BLAST program requires our input file and database, so they
must be transferred with `transfer_input_files`. 
    
            :::file
            transfer_input_files = pdbaa, mouse.fa
	    
    * Let's assume that we've run this program before, and we know that
1GB of disk and 1GB of memory will be MORE than enough (the 'log' file
will tell us how accurate we are, after the job runs): 
    
            :::file
            request_memory = 1GB
	    	request_disk = 1GB

1. Submit the blast job using `condor_submit`. Once the job starts, it
should run in just a few minutes and produce a file called
`results.txt`.
