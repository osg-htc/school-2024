---
status: testing
---

Data Exercise 1.3: Splitting Large Input for Better Throughput
==================================================================


The objective of this exercise is to prepare for _blasting_ a much larger input query file by splitting the input for
greater throughput and lower memory and disk requirements.
Splitting the input will also mean that we don't have to rely on additional large-data measures for the input query files.

Setup
-----

1. Log in to `ap40.uw.osg-htc.org`

1. Create a directory for this exercise named `blast-split` and change into it.
1. Copy over the following files from the [previous exercise](../part1-ex2-file-transfer):
    - Your submit file
    - `blastx`
    - `pdbaa_files.tar.gz`
    - `blast_wrapper.sh`
1. Remember to modify the submit file for the new locations of the above files.

### Obtain the large input

We've previously used `blastx` to analyze a relatively small input file of test data, `mouse.fa`, but let's imagine that
you now need to blast a much larger dataset for your research.
This dataset can be downloaded with the following command:

``` console
user@ap40 $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/mouse_rna.tar.gz
```

After un-tar'ing (`tar xzf mouse_rna.tar.gz`) the file, you should be able to confirm that it's size is roughly 100 MB.
Not only is this near the size cutoff for HTCondor file transfer, it would take hours to complete a single `blastx`
analysis for it and the resulting output file would be huge.

### Split the input file

For `blast`, it's scientifically valid to split up the input query file, analyze the pieces, and then put the results
back together at the end!
On the other hand, BLAST databases should not be split, because the `blast` output includes a score value for each
sequence that is calculated relative to the entire length of the database.

Because genetic sequence data is used heavily across the life sciences, there are also tools for splitting up the data
into smaller files.
One of these is called [genome tools](http://genometools.org/), and you can download a package of precompiled binaries
(just like BLAST) using the following command:

``` console
user@ap40 $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/gt-1.5.10-Linux_x86_64-64bit-complete.tar.gz
```

Un-tar the gt package (`tar -xzvf ...`), then run its sequence file splitter as follows, with the target file size of 1MB:

``` console
user@ap40 $ ./gt-1.5.10-Linux_x86_64-64bit-complete/bin/gt splitfasta -targetsize 1 mouse_rna.fa
```

You'll notice that the result is a set of 100 files, all about the size of 1 MB, and numbered 1 through 100.

Run a Jobs on Split Input 
--------------------

Now, you'll submit jobs on the split input files, where each job will use a different piece of the large original input file.

### Modify the submit file

First, you'll create a new submit file that passes the input filename as an argument and use a list of applicable
filenames.
Follow the below steps:

1. Copy the submit file from the [previous exercise](../part1-ex2-file-transfer) to a new file called `blast_split.sub` and modify the "queue" line of the submit file to the
   following:

        queue inputfile matching mouse_rna.fa.*


2. Replace the `mouse.fa` instances in the submit file with `$(inputfile)`, and rename the output, log, and error files
   to use the same `inputfile` variable:

        output = $(inputfile).out
        error = $(inputfile).err
        log = $(inputfile).log

3. Add an `arguments` line to the submit file so it will pass the name of the input file to the wrapper script

        arguments = $(inputfile)

4. Add the `$(inputfile)` to the end of your list of `transfer_input_files`:

        transfer_input_files = ... , $(inputfile)

5. Remove or comment out `transfer_output_files` and `transfer_output_remaps`.

6. Update the memory and disk requests, since the new input file is larger and will also produce larger output.
   It may be best to overestimate to something like 1 GB for each.

### Modify the wrapper file

Replace instances of the input file name in the `blast_wrapper.sh` script so that it will insert the first argument in
place of the input filename, like so:

``` file
./blastx -db pdbaa -query $1 -out $1.result
```

!!! note
    Bash shell scripts will use the first argument in place of `$1`, the second argument as `$2`, etc.

### Submit the jobs

This job will take a bit longer than the job in the last exercise, since the input file is larger (by about 3-fold).
Again, make sure that only the desired `output`, `error`, and `result` files come back at the end of the job.
In our tests, the jobs ran for ~15 minutes.

!!! warning "Jobs on jobs!"
    Be careful to not submit the job again. Why?  Our queue statement says `... matching mouse_rna.fa.*`, and look at
    the current directory.
    There are new files named `mouse_rna.fa.X.log` and other files.
    Submitting again, the `queue` statement would see these new files, and try to run blast on them!
    
    If you want to remove all of the extra files, you can try:
    
        :::console
        user@ap40 $ rm *.err *.log *.out *.result

Update the resource requests
----------------------------

After the job finishes successfully, examine the `log` file for memory and disk usage, and update the requests in the
submit file.


