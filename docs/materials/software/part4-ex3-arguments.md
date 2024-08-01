---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: #008; } </style>

Software Exercise 4.3: Passing Arguments Through the Wrapper Script
===================================================

**Objective**: Add arguments to a wrapper script to make it more flexible and modular

**Why learn this?**: Using script arguments will allow you to use the same script for
multiple jobs, by providing different inputs or parameters. These
arguments are normally passed on the command line, but in our world of job 
submission, the arguments will be listed in the submit file, in the arguments line.

Identifying Potential Arguments
-------------------------------

1. In the same directory as the last exercise, make sure you're in the directory with your 
BLAST job submission. 

1.  What values might we want to input to the script via arguments?
Hint: anything that we might want to change if we were to run the script
many times.

In this example, some values we might want to change are the name of the 
comparison database, the input file, and the output file. 

Modifying Files
---------------

1. We are going to add three arguments to the wrapper script, controlling 
the database, input and output file. 

1. Make a copy of your last submit file and open it for editing. Add an 
arguments line, or uncomment the one that exists, and add the three input 
values mentioned above. 

1. The arguments line in your submit file should look like this: 

        :::file
		arguments = pdbaa mouse.fa results3.txt

	(We're using `results3.txt`) to distinguish between the previous two runs.)

1.  For bash (the language of our current wrapper
script), the variables `$1`, `$2` and `$3` represent  the first, second, 
and third arguments, respectively.  Thus, in  the main command of the script,
replace the various names with these variables: 

        :::bash
        ./ncbi-blast-2.15.0+/bin/blastx -db $1/$1 -query $2 -out $3

	> If your wrapper script is in a different language, you should use 
	that language's syntax for reading in variables from the command line. 

1.  Once these changes are made, submit your jobs with `condor_submit`.
Use `condor_q -nobatch` to see what the job command looks like to
HTCondor.

It is now easy to change the inputs for the job; we can write them into 
the arguments line of the submit file and they will be propagated to the 
command in the wrapper script. We can even turn the submit file arguments 
into their *own* variables when submitting multiple jobs at once. 

Readability with Variables
---------------

One of the downsides of this approach, is that our command has become 
harder to read. The original script contains all the information at a glance:

	:::bash
	./ncbi-blast-2.15.0+/bin/blastx -db pdbaa/pdbaa -query mouse.fa -out results2.txt

But our new version is more cryptic -- what is `$1`?: 

	:::bash
	./ncbi-blast-2.15.0+/bin/blastx -db $1 -query $2 -out $3

One way to overcome this is to create our own variable names inside the wrapper 
script and assign the argument values to them. Here is an example for our 
BLAST script: 

	:::bash
	#!/bin/bash
	
	DATABASE=$1
	INFILE=$2
	OUTFILE=$3
	
	tar -xzf ncbi-blast-2.15.0+-x64-linux.tar.gz 
	tar -xzf pdbaa.tar.gz

	./ncbi-blast-2.15.0+/bin/blastx -db $DATABASE/$DATABASE -query $INFILE -out $OUTFILE

Here, we are assigning the input arguments (`$1`, `$2` and `$3`) to new variable names, and 
then using **those** names (`$DATABASE`, `$INFILE`, and `$OUTFILE`) in the command, 
which is easier to read. 

1. Edit your script to match the above syntax. 

1.  Submit your jobs with `condor_submit`. When the job finishes, look at the job's 
standard output file to see how the variables printed. 
