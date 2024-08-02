---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Workflows Exercise 1.3: A More Complex DAG
=======================================

The objective of this exercise is to run a real set of jobs with DAGMan.

Make Your Job Submission Files
------------------------------

We'll run our `goatbrot` example. If you didn't read about it yet, [please do so now](part1-ex2-mandelbrot.md). We are going to make a DAG with four simultaneous jobs (`goatbrot`) and one final node to stitch them together (`montage`). This means we have five jobs. We're going to run `goatbrot` with more iterations (100,000) so each job will take longer to run.

You can create your five jobs. The goatbrot jobs are very similar to each other, but they have slightly different parameters and output files.

### goatbrot1.sub

``` file
executable              = goatbrot
arguments               = -i 100000 -c -0.75,0.75 -w 1.5 -s 500,500 -o tile_0_0.ppm
log                     = goatbrot.log
output                  = goatbrot.out.0.0
error                   = goatbrot.err.0.0
request_memory          = 1GB
request_disk            = 1GB
request_cpus            = 1
queue
```

### goatbrot2.sub

``` file
executable              = goatbrot
arguments               = -i 100000 -c 0.75,0.75 -w 1.5 -s 500,500 -o tile_0_1.ppm
log                     = goatbrot.log
output                  = goatbrot.out.0.1
error                   = goatbrot.err.0.1
request_memory          = 1GB
request_disk            = 1GB
request_cpus            = 1
queue
```

### goatbrot3.sub

``` file
executable              = goatbrot
arguments               = -i 100000 -c -0.75,-0.75 -w 1.5 -s 500,500 -o tile_1_0.ppm
log                     = goatbrot.log
output                  = goatbrot.out.1.0
error                   = goatbrot.err.1.0
request_memory          = 1GB
request_disk            = 1GB
request_cpus            = 1
queue
```

### goatbrot4.sub

``` file
executable              = goatbrot
arguments               = -i 100000 -c 0.75,-0.75 -w 1.5 -s 500,500 -o tile_1_1.ppm
log                     = goatbrot.log
output                  = goatbrot.out.1.1
error                   = goatbrot.err.1.1
request_memory          = 1GB
request_disk            = 1GB
request_cpus            = 1
queue
```

### montage.sub

You should notice that the `transfer_input_files` statement refers to the files created by the other jobs.

``` file
+SingularityImage =     "/cvmfs/singularity.opensciencegrid.org/htc/rocky:9"
executable              = montage.sh
arguments               = tile_0_0.ppm tile_0_1.ppm tile_1_0.ppm tile_1_1.ppm -mode Concatenate -tile 2x2 mandel-from-dag.jpg
transfer_input_files    = tile_0_0.ppm,tile_0_1.ppm,tile_1_0.ppm,tile_1_1.ppm
output                  = montage.out
error                   = montage.err
log                     = montage.log
request_memory          = 1GB
request_disk            = 1GB
request_cpus            = 1
queue
```

Notice that the job specified by `montage.sub` uses a container image, as indicated by the `+SingularityImage` flag. This is because `montage` uses libraries that are not installed on the execution nodes. We use a container with `montage` installed and call it using the executable `montage.sh`; thus we will need to create the file `montage.sh`.

``` file
#!/bin/bash
# Pass all arguments to montage
montage "$@"
```

Make your DAG
-------------

In a file called `goatbrot.dag`, you have your DAG specification:

``` file
JOB g1 goatbrot1.sub
JOB g2 goatbrot2.sub
JOB g3 goatbrot3.sub
JOB g4 goatbrot4.sub
JOB montage montage.sub
PARENT g1 g2 g3 g4 CHILD montage
```

Ask yourself: do you know how we ensure that all the `goatbrot` commands can run simultaneously and all of them will complete before we run the montage job?

Running the DAG
---------------

Submit your DAG:

``` console
username@learn $ condor_submit_dag goatbrot.dag
-----------------------------------------------------------------------
File for submitting this DAG to Condor           : goatbrot.dag.condor.sub
Log of DAGMan debugging messages                 : goatbrot.dag.dagman.out
Log of Condor library output                     : goatbrot.dag.lib.out
Log of Condor library error messages             : goatbrot.dag.lib.err
Log of the life of condor_dagman itself          : goatbrot.dag.dagman.log

Submitting job(s).
1 job(s) submitted to cluster 71.

-----------------------------------------------------------------------
```

Watch Your DAG
--------------

Let’s follow the progress of the whole DAG:

1.  Use the `condor_watch_q` command to keep an eye on the running jobs. See more information about this tool [here](https://htcondor.readthedocs.io/en/latest/man-pages/condor_watch_q.html).

        :::console
        username@learn $ condor_watch_q 

    <span style="color:RED">**If you're quick enough, you may have seen DAGMan running as the lone job, before it submitted additional job nodes:**</span> 

        :::console
        BATCH                IDLE  RUN  DONE  TOTAL  JOB_IDS
        goatbrot.dag+222059     -    1     -      1  222059.0

        [=============================================================================]

        Total: 1 jobs; 1 running

        Updated at 2024-07-28 13:52:57

    <span style="color:RED">**DAGMan has submitted the goatbrot jobs, but they haven't started running yet**</span>
    
        :::console
        BATCH                IDLE  RUN  DONE  TOTAL  JOB_IDS
        goatbrot.dag+222059     4    1     -      5  222059.0 ... 222063.0

        [===============--------------------------------------------------------------]

        Total: 5 jobs; 4 idle, 1 running

        Updated at 2024-07-28 13:53:53


    <span style="color:RED">**They're running**</span> 

        :::console
        BATCH                IDLE  RUN  DONE  TOTAL  JOB_IDS
        goatbrot.dag+222059     -    5     -      5  222059.0 ... 222063.0
        [=============================================================================]

        Total: 5 jobs; 5 running

        Updated at 2024-07-28 13:54:33

    <span style="color:RED">**They finished, but DAGMan hasn't noticed yet. It only checks periodically:**</span>

        :::console
        BATCH                IDLE  RUN  DONE  TOTAL  JOB_IDS
        goatbrot.dag+222059     -    1    4     -      5  222059.0 ... 222063.0

        [##############################################################===============]

        Total: 5 jobs; 4 completed, 1 running

        Updated at 2024-07-28 13:55:13

    Eventually, you'll see the montage job submitted, then running, then leave the queue, and then DAGMan will leave the queue.

1.  Examine your results. For some reason, goatbrot prints everything to stderr, not stdout.

        :::console
        username@learn $ cat goatbrot.err.0.0 
        Complex image: Center: -0.75 + 0.75i Width: 1.5 Height: 1.5 Upper Left: -1.5 + 1.5i Lower Right: 0 + 0i
         
        Output image: Filename: tile_0_0.ppm Width, Height: 500, 500 Theme: beej Antialiased: no
        
        Mandelbrot: Max Iterations: 100000 Continuous: no
         
        Goatbrot: Multithreading: not supported in this build

        Completed: 100.0%

1.  Examine your log files (`goatbrot.log` and `montage.log`) and DAGMan output file (`goatbrot.dag.dagman.out`). Do they look as you expect? Can you see the progress of the DAG in the DAGMan output file?
1.  As you did earlier, transfer the resulting `mandel-from-dag.jpg` to your computer so that you can view the image. Does the image look correct?
1.  Clean up your results by removing all of the `goatbrot.dag.*` files if you like. Be careful to not delete the `goatbrot.dag` file.

Bonus Challenge
---------------

-   Re-run your DAG. When jobs are running, try `condor_q -nobatch -dag`. What does it do differently?
-   Challenge, if you have time: Make a bigger DAG by making more tiles in the same area.
