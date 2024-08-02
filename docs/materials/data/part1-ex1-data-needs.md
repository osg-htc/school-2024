---
status: testing
---

Data Exercise 1.1: Understanding Data Requirements
===============================

Exercise Goal 
-------------

This exercise's goal is to learn to think critically about an application's data needs, especially before submitting a
large batch of jobs or using tools for delivering large data to jobs.
In this exercise we will attempt to understand the input and output of the bioinformatics application
[BLAST](http://blast.ncbi.nlm.nih.gov/).

Setup
-----

For this exercise, we will use the `ap40.uw.osg-htc.org` access point. Log in:

``` hl_lines="1"
$ ssh <USERNAME>@ap40.uw.osg-htc.org
```

Create a directory for this exercise named `blast-data` and change into it

### Copy the Input Files ###

To run BLAST, we need the executable, input file, and reference
database. For this example, we'll use the "pdbaa" database, which
contains sequences for the protein structure from the Protein Data Bank.
For our input file, we'll use an abbreviated fasta file with mouse
genome information.

1. Copy the BLAST executables:
   
        :::console
        user@ap40 $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/ncbi-blast-2.12.0+-x64-linux.tar.gz
        user@ap40 $ tar -xzvf ncbi-blast-2.12.0+-x64-linux.tar.gz

1.  Download these files to your current directory:

        :::console
        user@ap40 $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/pdbaa.tar.gz
        user@ap40 $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/mouse.fa

1.  Untar the `pdbaa` database:

        :::console
        user@ap40 $ tar -xzvf pdbaa.tar.gz

Understanding BLAST
-------------------

Remember that `blastx` is executed in a command like the following:

``` console
user@ap40 $ ./ncbi-blast-2.12.0+/bin/blastx -db <DATABASE ROOTNAME> -query <INPUT FILE> -out <RESULTS FILE>
```

In the above, the `<INPUT FILE>` is the name of a file containing a number of genetic sequences (e.g. `mouse.fa`), and
the database that these are compared against is made up of several files that begin with the same `<DATABASE ROOTNAME>`,
(e.g. `pdbaa/pdbaa`).
The output from this analysis will be printed to `<RESULTS FILE>` that is also indicated in the command.

Calculating Data Needs
----------------------

Using the files that you prepared in `blast-data`, we will calculate how much disk space is needed if we were to
run a hypothetical BLAST job with a wrapper script, where the job:

- Transfers all of its input files (including the executable) as tarballs
- Untars the input files tarballs on the execute host
- Runs `blastx` using the untarred input files

Here are some commands that will be useful for calculating your job's storage needs:

- List the size of a specific file:

        :::console
        user@ap40 $ ls -lh <FILE NAME>

- List the sizes of all files in the current directory:

        :::console
        user@ap40 $ ls -lh

- Sum the size of all files in a specific directory:

        :::console
        user@ap40 $ du -sh <DIRECTORY>

### Input requirements

Total up the amount of data in all of the files necessary to run the `blastx` wrapper job, including the executable itself.
Write down this number.
Also take note of how much total data is in the `pdbaa` directory.

!!! note "Compressed Files"
    Remember, `blastx` reads the un-compressed `pdbaa` files.

### Output requirements

The output that we care about from `blastx` is saved in the file whose name is indicated after the `-out` argument to
`blastx`.
Also, remember that HTCondor also creates the error, output, and log files, which you'll need to add up, too.
Are there any other files?
Total all of these together, as well.

<!--
#removed for 2020 Virtual school since (I assume) we won't have a group discussion forum 
Talk about this as a group!
---------------------------

Once you have completed the above tasks, we'll talk about the totals as a group.

-   How much disk space is required on the submit server for one blastx run with the input files you used before?
    (Input data)
-   How much disk space is required on the worker node? (uncompressed + output data)
-   How *many* files are needed and created for each run? (Output data)
-   How much total disk space would be necessary on the submit server to run 10 jobs?
    (remember that some of the files will be shared by all 10 jobs, and will not be multiplied)

Answers
-------

- Submit server: Only compressed files needed.  Don't need uncompressed on submit server node.
    - pdbaa.tar.gz: 22MB
    - blastx.tar.gz: 14MB
    - mouse.fa.tar.gz: 104K
    - Total: ~36MB
- Worker Node: Compressed files + uncompressed files
    - pdbaa: 97MB
    - blastx: 39MB
    - mouse.fa: 389KB
    - results: 11MB
    - stdout: 0
    - stderr: 0
    - Compressed files: ~36MB
    - Total: ~183MB
- How many files are needed and created for each run?
    - files in pdbaa: 12
    - blastx: 1
    - mouse.fa: 1
    - results: 1
    - stdout + stderr = 2
    - total: 17
- Submit server with 10 jobs
    - Only need multiple queries, because that is what is different.
    - so pdbaa (22MB) + blastx (14MB) + 10 * mouse.fa (104k) = ~37MB

-->

<!--
## Removed 2019, not sure how users are supposed to reasonably get this info
-   Assuming that each file is read completely by BLAST, and since you know how long blastx runs (time it):
    -   At what rate are files read in?
    -   How many MB/s?
- Rates:
    - my run, and this can vary: 198 seconds
    - 17 / 198 = 0.086 files per second (low)
    - 149 / 198 = 0.75 MBs per second


-->

Up next!
--------

Next you will create a HTCondor submit script to transfer the Blast input files in order to run Blast on a worker nodes.
[Next Exercise](part1-ex2-file-transfer.md)
