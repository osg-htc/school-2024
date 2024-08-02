---
status: testing
---

# Self-Checkpointing Exercise 1.1: Trying It Out

The goal of this exercise is to practice writing a submit file for self-checkpointing,
and to see the process in action.

## Calculating Fibonacci numbers &hellip; slowly

The sample code for this exercise calculates
[the Fibonacci number](https://en.wikipedia.org/wiki/Fibonacci_number)
resulting from a given set of iterations.
Because this is a trival computation,
the code includes a delay in each iteration through the main loop;
this simulates a more intensive computation.

To get set up:

1.  Log in to `ap40.uw.osg-htc.org`
    (`ap1` is fine, too)

1.  Create and change into a new directory for this exercise

1.  Download the Python script that is the main executable for this exercise:

        :::console
        user@server $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/fibonacci.py

1.  If you want to run the script directly, make it executable first:

        :::console
        user@server $ chmod 0755 fibonacci.py

Take a look at the code, if you like.
It is not very elegant, but it gets the job done.

A few notes:

*   The script takes a single argument, the number of iterations to run.
    To minimize computing time while leaving time to explore, `10` is a good number of iterations.

*   The script checkpoints every other iteration through the main loop.
    The exit status code for a checkpoint is 85.

*   It prints some output to standard out along the way, to let you know what is going on.

*   The final result is written to a separate file named `fibonacci.result`.
    This file does not exist until the very end of the complete run.

*   It is safe to run from the command line on an access point:

        :::console
        user@server $ ./fibonacci.py 10

    If you run it, what happens?  (Due to the 30-second delay, be patient.)
    Can you explain its behavior?
    What happens if you run it again, without changing any files in between?  Why?

## Preparing to run

Now you have an executable and you know how to run it.
It is time to prepare it for submission to HTCondor!

Using what you know about the script (above),
and using information in the slides from today,
try writing a submit file that runs this software and
implements exit-driven self-checkpointing.
The Python code itself is ready and should not need any changes.

Just use a plain `queue` statement, one job is enough to experiment on.

**Before you submit,** read the next section first!

## Running and monitoring

With the 30-second delay per iteration in the code and the suggested 10 iterations,
once the script starts running you have about 5 minutes of runtime in which to see what is going on.
So it may help to read through this section *and then* return here and submit your job.

If your job has problems or finishes before you have the chance to do all the steps below,
just remove the extra files (besides the Python script and your submit file) and try again!

### Submission and first checkpoint

1.  Submit the job
1.  Look at the contents of the submit directory&nbsp;— what changed?
1.  Start watching the log file: `tail -n 100 -f YOUR-LOG-FILENAME.log`

Be patient!  As HTCondor adds more lines to the end of your log file, they will appear automatically.
Thus, nothing much will happen until HTCondor starts running your job.
When it does, you will see three sets of messages in the log file quickly:

*   `Started transferring input files`
*   `Finished transferring input files`
*   `Job executing on host:`

(Of course, each message will contain a lot of other characters!)

Now wait about 1 minute, and you should see two more messages appear:

*   `Started transferring output files`
*   `Finished transferring output files`

That is the first checkpoint happening!

### Forcing your job to stop running

Now, assuming that your job is still running (check `condor_q` again),
you can force HTCondor to remove (*evict*) your job before it finishes:

1.  Run `condor_q` to get the job ID of the running job
1.  Run `condor_vacate_job JOB_ID`, where you replace `JOB_ID` with your job ID from above
1.  Monitor the action again by running `tail -n 100 -f YOUR-LOG-FILENAME.log`

### Finishing the job and wrap-up

Be patient again!
You removed your running job, and so HTCondor put it back in the queue as idle.
If you wait a minute or two, you should see that HTCondor starts running the job again.

1.  In the log file, look carefully for the two `Job executing on host:` messages.
    Does it seem like you ran on the same computer again or on a different one?
    Both are possible!

1.  Let your job finish running this time.
    There should be a `Job terminated of its own accord` message near the end.

1.  Did you get results?  Go through all the files and see what they contain.
    The log and output files are probably the most interesting.
    But did you get a result file, too?

Did the output file&nbsp;—
that is, whatever file you named in the `output` line of your submit file&nbsp;—
contain *everything* that you expected it to?

## Conclusion

This has been a brief and simple tour of self-checkpointing.
If you would like to learn more, please read
[the Self-Checkpointing Applications section](https://htcondor.readthedocs.io/en/latest/users-manual/self-checkpointing-applications.html)
of the HTCondor Manual.
Or talk to School staff about it.
Or contact support@osg-htc.org for further help at any time.
