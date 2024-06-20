---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

HTC Exercise 2.2: Use queue *N*, $(Cluster), and $(Process)
==============================================================

Background
------------

Suppose you have a program that you want to run many times with different arguments each time. With what you know so far, you have a couple of choices:

-   Write one submit file; submit one job, change the argument in the submit file, submit another job, change the submit file, …
-   Write many submit files that are nearly identical except for the program argument

Neither of these options seems very satisfying. Fortunately, HTCondor's `queue` statement is here to help!


Exercise Goal
-------------

The goal of the next several exercises is
to learn to submit many jobs from a single HTCondor `queue` statement,
and to control things like filenames and arguments on a per-job basis when doing so.


Running Many Jobs With One queue Statement
------------------------------------------

**Example** 
Here is a C program that uses a stochastic (random) method to estimate the value of π. The single argument to the program is the number of samples to take. More samples should result in better estimates!

``` c
#include <stdio.h>
#include <stdlib.h>
#include <sys/time.h>

int main(int argc, char *argv[])
{
  struct timeval my_timeval;
  int iterations = 0;
  int inside_circle = 0;
  int i;
  double x, y, pi_estimate;

  gettimeofday(&my_timeval, NULL);
  srand48(my_timeval.tv_sec ^ my_timeval.tv_usec);

  if (argc == 2) {
    iterations = atoi(argv[1]);
  } else {
    printf("usage: circlepi ITERATIONS\n");
    exit(1);
  }

  for (i = 0; i < iterations; i++) {
    x = (drand48() - 0.5) * 2.0;
    y = (drand48() - 0.5) * 2.0;
    if (((x * x) + (y * y)) <= 1.0) {
      inside_circle++;
    }
  }
  pi_estimate = 4.0 * ((double) inside_circle / (double) iterations);
  printf("%d iterations, %d inside; pi = %f\n", iterations, inside_circle, pi_estimate);
  return 0;
}
```

1.  In a new directory for this exercise, create and save the code to a file named `circlepi.c`
1.  Compile the code (we will cover this in more detail during the Software lecture):

        :::console
        username@ap1 $ gcc -o circlepi circlepi.c

1.  Test the program with just 1000 samples:

        :::console
        username@ap1 $ ./circlepi 1000

Now suppose that you want to run the program many times, to produce many estimates.
To do so, we can tell HTCondor how many jobs to "queue up" via the `queue` statement
we've been putting at the end of each of our submit files.
Let’s see how it works:

1.  Write a normal submit file for this program
    -   Pass 1 million (`1000000`) as the command line argument to `circlepi`
    -   Make sure to include `log`, `output`, and `error` (with filenames like `circlepi.log`), and `request_*` lines
    -   At the end of the file, write `queue 3` instead of just `queue` ("queue 3 jobs" vs. "queue a job").
1.  Submit the file. Note the slightly different message from `condor_submit`:

        :::console
        3 job(s) submitted to cluster *NNNN*.

1.  Before the jobs execute, look at the job queue to see the multiple jobs

Here is some sample `condor_q -nobatch` output:

``` console
 ID       OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD
10228.0   cat             7/25 11:57   0+00:00:00 I  0    0.7 circlepi 1000000000
10228.1   cat             7/25 11:57   0+00:00:00 I  0    0.7 circlepi 1000000000
10228.2   cat             7/25 11:57   0+00:00:00 I  0    0.7 circlepi 1000000000
```

In this sample, all three jobs are part of **cluster** `10228`, 
but the first job was assigned **process** `0`, 
the second job was assigned process `1`, 
and the third one was assigned process `2`.
(Programmers like to start counting from 0.)

Now we can understand what the first column in the output, the ***job ID***, represents.
It is a job’s *cluster number*, a dot (`.`), and the job’s *process number*.
So in the example above, the job ID of the second job is `10228.1`.

**Pop Quiz:** Do you remember how to ask HTCondor's queue to list the status of all of the jobs from one cluster? How about one specific job ID?

Using queue *N* With Output
---------------------------

When all three jobs in your single cluster are finished, examine the resulting files.

-   What is in the output file? 
-   What is in the error file? (hopefully it is empty!)
-   What is in the log file? Look carefully at the job IDs in each event.
-   Is this what you expected? Is it what you wanted? If the output is not what you expected, what do you think happened?

Using $(Process) to Distinguish Jobs
------------------------------------

As you saw with the experiment above, each job ended up overwriting the same output and error filenames in the submission directory.
After all, we didn't tell it to behave any differently when it ran three jobs.

We need a way to separate output (and error) files *per job that is queued*, not just for the whole cluster of jobs. Fortunately, HTCondor has a way to separate the files easily.

When processing a submit file, HTCondor will replace any instance of `$(Process)` with the process number of the job, for each job that is queued. 
For example, you can use the `$(Process)` variable to define a separate output file name for each job:

``` file
output = my-output-file-$(Process).out
queue 10
```

Even though the `output` filename is defined only once, HTCondor will create separate output filenames for each job:

|                  |                        |
|------------------|------------------------|
| First job        | `my-output-file-0.out` |
| Second job       | `my-output-file-1.out` |
| Third job        | `my-output-file-2.out` |
| ...              | ...                    |
| Last (tenth) job | `my-output-file-9.out` |

Let’s see how this works for our program that estimates π.

1.  In your submit file, change the definitions of `output` and `error` to use `$(Process)` in the filename, similar to the example above.
1.  Delete any standard output, standard error, and log files from previous runs.
1.  Submit the updated file.

When all three jobs are finished, examine the resulting files again.

-   How many files are there of each type? What are their names?
-   Is this what you expected? Is it what you wanted from the π estimation process?

Using $(Cluster) to Separate Files Across Runs
----------------------------------------------

With `$(Process)`, you can get separate output (and error) filenames for each job within a run. However, the next time you submit the same file, all of the output and error files are overwritten by new ones created by the new jobs. Maybe this is the behavior that you want. But sometimes, you may want to separate files by run, as well.

In addition to `$(Process)`, there is also a `$(Cluster)` variable that you can use in your submit files. It works just like `$(Process)`, except it is replaced with the cluster number of the entire submission. Because the cluster number is the same for all jobs within a single submission, it does not separate files by job within a submission. But when used **with** `$(Process)`, it can be used to separate files by run. For example, consider this `output` statement:

``` file
output = my-output-file-$(Cluster)-$(Process).out
```

For one particular run, it might result in output filenames like `my-output-file-2444-0.out`, `myoutput-file-2444-1.out`, `myoutput-file-2444-2.out`, etc.

However, the next run would have different filenames, replacing `2444` with the new Cluster number of that run.

Using $(Process) and $(Cluster) in Other Statements
---------------------------------------------------

The `$(Cluster)` and `$(Process)` variables can be used in any submit file statement, although they are useful in some kinds of submit file statements and not really for others. For example, consider using $(Cluster) or $(Process) in each of the below:

-   `log`
-   `transfer_input_files`
-   `transfer_output_files`
-   `arguments`

Unfortunately, HTCondor does not easily let you perform math on the `$(Process)` number when using it. So, for example, if you use `$(Process)` as a numeric argument to a command, it will always result in jobs getting the arguments 0, 1, 2, and so on. If you have control over your program and the way in which it uses command-line arguments, then you are fine. Otherwise, you might need a solution like those in the next exercises.

(Optional) Defining JobBatchName for Tracking
---------------------------------------------

It is possible to define arbitrary attributes in your submit file, and that one purpose of such attributes is to track or report on different jobs separately. In this optional exercise, you will see how this technique can be used.

Once again, we will use `sleep` jobs, so that your jobs remain in the queue long enough to experiment on.

1.  Create a submit file that runs `sleep 120`.
1.  Instead of a single `queue` statement, write this:

        :::file
        jobbatchname = 1
        queue 5

1.  Submit the submit file to HTCondor. 
1.  Now, quickly edit the submit file to instead say:

        :::file
        jobbatchname = 2

1.  Submit the file again.

Check on the submissions using a normal `condor_q` and `condor_q -nobatch`. Of course, your special attribute does not appear in the `condor_q -nobatch` output, but it is present in the `condor_q` output and in each job’s ClassAd. You can see the effect of the attribute by limiting your `condor_q` output to one type of job or another. First, run this command:

``` console
username@ap1 $ condor_q -constraint 'JobBatchName == "1"'
```

Do you get the output that you expected? Using the example command above, how would you list your other five jobs?
(There will be more on how to use HTCondor constraints in later exercises.)
