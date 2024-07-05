# Organizing HTC Workloads

Imagine you have a collection of books,
and you want to analyze how word usage varies from book to book or author to author. 

This exercise is similar to HTCondor exercise 2.4,
in that it is about counting word frequencies in multiple files.
But the focus here is on organizing the files more effectively on the Access Point,
with an eye to scaling up to a larger HTC workload in the future.

## Log into an OSPool Access Point

Make sure you are logged into `ap40.uw.osg-htc.org`. 

## Get Files

To get the files for this exercise:

1.  Type `wget https://github.com/osg-htc/school-2024/raw/main/docs/materials/scaling/files/osgus23-day4-ex11-organizing-files.tar.gz` to download the tarball.
1.  As you learned earlier, expand this tarball file; it will create a `organizing-files` directory.
1.  Change to that directory, or create a separate one for this exercise and copy the files in.

## Our Workload

We can analyze one book by running the `wordcount.py` script, with the 
name of the book we want to analyze: 

    :::console
    $ ./wordcount.py Alice_in_Wonderland.txt

Try running the command to see what the output is for the script.
Once you have done that delete the output file created (`rm counts.Alice_in_Wonderland.txt`).

We want to run this script on all the books we have copies of. 

*   What is the input set for this HTC workload?
*   What is the output set?

## Make an Organization Plan

Based on what you know about the script, inputs, and outputs,
how would you organize this HTC workload in directories (folders) on the Access Point?

There will also be system and HTCondor files produced when we submit a job&nbsp;&mdash;
how would you organize the log, standard output, and standard error files?

Try making those changes before moving on to the next section of the tutorial.

## Organize Files

There are many different ways to organize files;
a simple method that works for most workloads is having a directory for your input files
and a directory for your output files.

1.  Set up this structure on the command line by running: 

        :::console
        $ mkdir input
        $ mv *.txt input/
        $ mkdir output

2.  View the current directory and its subdirectories by using the `ls` command with the *recursive* (`-R`) flag:

        :::console
        $ ls -R
        README.md    books.submit input        output       wordcount.py

        ./input:
        Alice_in_Wonderland.txt Huckleberry_Finn.txt
        Dracula.txt             Pride_and_Prejudice.txt

        ./output:

3.  Next, create directories for the HTCondor log, standard output, and standard output files (in one directory):

        :::console
        $ mkdir logs
        $ mkdir errout

## Submit One Job

Now we want to submit a test job that uses this organizing scheme,
using just one item in our input set&nbsp;&mdash;
in this example, we will use the `Alice_in_Wonderland.txt` file from our `input` directory.

1.  Fill in the incomplete lines of the submit file, as shown below:

        :::console
        executable    = wordcount.py
        arguments     = Alice_in_Wonderland.txt

        transfer_input_files    = input/Alice_in_Wonderland.txt
        transfer_output_files   = counts.Alice_in_Wonderland.txt
        transfer_output_remaps  = "counts.Alice_in_Wonderland.txt=output/counts.Alice_in_Wonderland.txt"

    To tell HTCondor the location of the input file, we need to include the input directory.
    Also, this submit file uses the `transfer_output_remaps` feature that you learned about;
    it will move the output file to the `output` directory by renaming or remapping it.

1.  Next, edit the submit file lines that tell the log, output, and error files where to go:

        :::console
        output        = logs/job.$(ClusterID).$(ProcID).out
        error         = errout/job.$(ClusterID).$(ProcID).err
        log           = errout/job.$(ClusterID).$(ProcID).log

1.  Submit your job and monitor its progress.

## Submit Multiple Jobs

Now, you are ready to submit the whole workload.

1.  Create a file with the list of input files (the input set);
    here, this is the list of the book files to analyze.
    Do this by using the shell `ls` command and redirecting its output to a file:

        :::console
        $ ls input > booklist.txt
        $ cat booklist.txt

1.  Modify the submit file to reference the file of inputs and replace the fixed value (`Alice_in_Wonderland.txt`) with a variable (`$(book)`):

        :::console
        executable    = wordcount.py
        arguments     = $(book)

        transfer_input_files    = input/$(book)
        transfer_output_files   = counts.$(book)
        transfer_output_remaps  = "counts.$(book)=output/counts.$(book)"

        queue book from booklist.txt

1.  Submit the jobs

1.  When complete, look at the complete set of input and (now) output files to see how they are organized.
