---
status: reviewed
---

Data Exercise 2.2: Using OSDF for outputs
=========================================================

In this exercise, we will run a multimedia program that converts and manipulates video files.
In particular, we want to convert large `.mov` files to smaller (10-100s of MB) `mp4` files.
Just like the Blast database in the [previous exercise](../part2-ex1-osdf-inputs), these video
files are potentially too large to send to jobs using HTCondor's default file transfer for 
inputs/outputs, so we will use OSDF.

Data
----

To get the exercise set up:

1.  Log into `ap40.uw.osg-htc.org`

1.  Create a directory for this exercise named `osdf-outputs` and change into it.

1.  Download the input data and store it under the OSDF directory (`cd` to that
    directory first):

        :::console
        user@ap40 $ cd /ospool/ap40/data/[USERNAME]/
        user@ap40 $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/ducks.mov
        user@ap40 $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/teaching.mov
        user@ap40 $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/test_open_terminal.mov

1.  We're going to need a list of these files later.  Below is the final list of movie files.
    `cd` back to your `osdf-outputs` directory and create a file named `movie_list.txt`,
    with the following content:

        :::file
        ducks.mov
        teaching.mov
        test_open_terminal.mov

Software
--------

We'll be using a multi-purpose media tool called `ffmpeg`  to convert video formats.
The basic command to convert a file looks like this: 

``` console
user@ap40 $ ./ffmpeg -i input.mov output.mp4
```

In order to resize our files, we're going to manually set the video bitrate and resize the frames, so that the resulting
file is smaller.

``` console
user@ap40 $ ./ffmpeg -i input.mp4 -b:v 400k -s 640x360 output.mp4
```

To get the `ffmpeg` binary do the following:

1.  We'll be downloading the `ffmpeg` pre-built static binary originally from this page: <http://johnvansickle.com/ffmpeg/>. 

        :::console
        user@ap40 $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/ffmpeg-release-64bit-static.tar.xz

1.  Once the binary is downloaded, un-tar it, and then copy the main `ffmpeg` program into your current directory: 

        :::console
        user@ap40 $ tar -xf ffmpeg-release-64bit-static.tar.xz
        user@ap40 $ cp ffmpeg-4.0.1-64bit-static/ffmpeg ./

Script
------

We want to write a script that runs on the worker node that uses `ffmpeg` to convert a `.mov` file to a smaller format.
Our script will need to run the proper executable. Create a file called `run_ffmpeg.sh`, that does the steps described above.
Use the name of the smallest `.mov` file in the `ffmpeg` command.
An example of that script is below: 

    :::bash
    #!/bin/bash

    ./ffmpeg -i test_open_terminal.mov -b:v 400k -s 640x360 test_open_terminal.mp4

Ultimately we'll want to submit several jobs (one for each `.mov` file), but to start with, we'll run one job to make
sure that everything works.

Remember to `chmod +x run_ffmpeg.sh` to make the script executable.

Submit File
-----------

Create a submit file for this job, based on other submit files from the school. Things to consider:

1.  We'll be copying the video file into the job's working directory from OSDF, so make sure to request enough disk space for the
    input `mov` file and the output `mp4` file.
    If you're aren't sure how much to request, ask a helper.

1.  Add the same requirements as the previous exercise: 

        requirements = (OSGVO_OS_STRING == "RHEL 9")

1.  We need to transfer the `ffmpeg` program that we downloaded above, and the movie from OSDF:

        transfer_input_files = ffmpeg, osdf:///ospool/ap40/data/[USERNAME]/test_open_terminal.mov

1.  Transfer outputs via OSDF. This requires a transfer remap:

        transfer_output_files = test_open_terminal.mp4
        transfer_output_remaps = "test_open_terminal.mp4 = osdf:///ospool/ap40/data/[USERNAME]/test_open_terminal.mp4"


Initial Job
-----------

With everything in place, submit the job. Once it finishes, we should check to make sure everything ran as expected:

1.  Check the OSDF directory. Did the output `.mp4` file return?
3.  Check file sizes. How big is the returned `.mp4` file? How does that compare to the original `.mov` input?

If your job successfully returned the converted `.mp4` file and did **not** transfer the `.mov` file to the submit
server, and the `.mp4` file was appropriately scaled down, then we can go ahead and convert all of the files we uploaded
to OSDF.

Multiple jobs
-------------

We wrote the name of the `.mov` file into our `run_ffmpeg.sh` executable script.
To submit a set of jobs for all of our `.mov` files, what will we need to change in:

1. The script?
1. The submit file?

Once you've thought about it, check your reasoning against the instructions below.

### Add an argument to your script

**Look at your `run_ffmpeg.sh` script. What values will change for every job?**

The input file will change with every job - and don't forget that the output file will too! Let's make them both into
arguments.

To add arguments to a bash script, we use the notation `$1` for the first argument (our input file) and `$2` for the
second argument (our output file name).
The final script should look like this: 

``` file
#!/bin/bash

./ffmpeg -i $1 -b:v 400k -s 640x360 $2
```

### Modify your submit file

1.  We now need to tell each job what arguments to use.
    We will do this by adding an arguments line to our submit file.
    Because we'll only have the input file name, the "output" file name will be the input file name with the `mp4`
    extension.
    That should look like this: 

        :::file
        arguments = $(mov) $(mov).mp4

1.  Update the `transfer_input_files` to have `$(mov)`:

        :::file
        transfer_input_files = ffmpeg, osdf:///ospool/ap40/data/[USERNAME]/$(mov)

1.  Similarly, update the output/remap with `$(mov).mp4`:

        :::file
        transfer_output_files = $(mov).mp4
        transfer_output_remaps = "$(mov).mp4 = osdf:///ospool/ap40/data/[USERNAME]/$(mov).mp4"

1. To set these arguments, we will use the `queue .. from` syntax.
   In our submit file, we can then change our queue statement to:

        queue mov from movie_list.txt

Once you've made these changes, try submitting all the jobs!

Bonus
-----

If you wanted to set a different output file name, bitrate and/or size for each original movie, how could you modify:

1. `movie_list.txt` 
2. Your submit file 
3. `run_ffmpeg.sh`

to do so?


??? "Show hint"

    Here's the changes you can make to the various files:
    
    1.  `movie_list.txt` 
    
            ducks.mov ducks.mp4 500k 1280x720
            teaching.mov teaching.mp4 400k 320x180
            test_open_terminal.mov terminal.mp4 600k 640x360
    
    1. Submit file
    
            arguments = $(mov) $(mp4) $(bitrate) $(size)
    
            queue mov,mp4,bitrate,size from movie_list.txt
    
    
    1. `run_ffmpeg.sh`
    
            #!/bin/bash
            ./ffmpeg -i $1 -b:v $3 -s $4 $2



