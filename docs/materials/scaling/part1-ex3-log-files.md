---
status: testing
---

<style type="text/css">
  pre em { font-style: normal; background-color: yellow; }
  pre strong { font-style: normal; font-weight: bold; color: \#008; }
</style>

Getting Job Information from Log Files
======================================

HTCondor job log files contain useful information about submitted, running, and/or completed jobs, but the format of that information may not always be useful *to you*. Here, we have a few examples of how to use some powerful Unix commands (`grep`, `sort`, `uniq`) to pull information out of these job log files. It is now time for you to try these on your own jobs!

Before starting this exercise, copy a couple of your job log files from previous exercises (for example, HTC Exercise 1.5 and/or OSG Exercise 1.1) in to a new directory for this exercise. Use these log files in place of `my-job.log` in the examples below.

The `grep` command displays lines from a file matching a given pattern, where the pattern is the first argument provided to `grep`. For example `grep 'alice' address_book.txt` would print out all lines containing the characters `alice` in the file named `address_book.txt`. While working through this exercise, consider keeping one of your job log files open in a separate window to see if you can figure out how we came up with the patterns presented in this exercise.

Job terminations
----------------

Lines for job termination events in the job log always start with `005` and contain the timestamp of when the job(s) ended. Use the following `grep` command to get a list of when jobs ended in your log files:

``` console
$ grep '^005' my-job.log
```

*Optional challenge*: What is the importance of `^` in the pattern (`^005`) provided above?

Recall that executables typically exit with code `0` when they exit normally, which often (but not always!) means that they exited successfully. Lines containing jobs' exit codes (i.e. return values) all contain the word `termination`. Use `grep` to get a list of jobs' exit codes:

``` console
$ grep termination my-job.log
```

By "piping" the output of the previous command through the `sort` and then `uniq` commands, we can get a count of each exit code:

``` console
$ grep termination my-job.log | sort | uniq -c
```

Here's an example of the output from the previous commands when run on a log file written to from eight jobs. Six jobs exited with exit code `0`, while two exited `1`:

``` console
[username@ap40]$ grep '^005' my-job.log
005 (236881.000.000) 2022-07-27 15:07:38 Job terminated.
005 (236883.000.000) 2022-07-27 15:07:42 Job terminated.
005 (236882.000.000) 2022-07-27 15:08:01 Job terminated.
005 (236880.000.000) 2022-07-27 15:08:07 Job terminated.
005 (236891.000.000) 2022-07-27 15:13:31 Job terminated.
005 (236893.000.000) 2022-07-27 15:13:32 Job terminated.
005 (236892.000.000) 2022-07-27 15:13:58 Job terminated.
005 (236890.000.000) 2022-07-27 15:13:59 Job terminated.

[username@ap40]$ grep 'termination' my-job.log
(1) Normal termination (return value 0)
(1) Normal termination (return value 1)
(1) Normal termination (return value 0)
(1) Normal termination (return value 0)
(1) Normal termination (return value 1)
(1) Normal termination (return value 0)
(1) Normal termination (return value 0)
(1) Normal termination (return value 0)

[username@ap40]$ grep 'termination' my-job.log | sort | uniq -c
6   (1) Normal termination (return value 0)
2   (1) Normal termination (return value 1)
```

Job resource usage
------------------

Jobs' resource usages (and requests and allocations) are logged in the following format:

``` file
Partitionable Resources :    Usage  Request Allocated
    Cpus                 :                 1         1
    Disk (KB)            :    10382  1048576   1468671
    Memory (MB)          :      692     1024      1024
```

Run the following `grep` command to pull out the memory information from your job logs:

``` console
$ grep 'Memory (MB) *:' my-job.log
```

Look back at the format in the example above. Columns after the `:` will first show memory usage, then memory requested, and then the memory allocated to your job.

Similarly, use the following command to get the disk information from your job logs:

``` console
$ grep 'Disk (KB) *:' my-job.log
```

Here's some example output from running the memory `grep` command on the same eight-job log file:

``` console
[username@ap40]$ grep 'Memory (MB) *:' my-job.log
Memory (MB)          :      692     1024      1024 
Memory (MB)          :      714     1024      1024 
Memory (MB)          :      703     1024      1024 
Memory (MB)          :      699     1024      1024 
Memory (MB)          :      705     1024      1024 
Memory (MB)          :      704     1024      1024 
Memory (MB)          :      711     1024      1024 
Memory (MB)          :      697     1024      1024 
```

In this example, the memory usage for the jobs ranged from 692 to 714 MB, and they all requested (and were allocated) 1 GB of memory.


Other job information
---------------------

See if you can come up with `grep` commands to gather the number of bytes sent and received by jobs (i.e. how much data was transferred to/from the access point). Here is some example output for comparison:

``` console
[username@ap40]$ grep '<PATTERN REMOVED>' my-job.log
760393  -  Total Bytes Sent By Job
760395  -  Total Bytes Sent By Job
760397  -  Total Bytes Sent By Job
760395  -  Total Bytes Sent By Job
760393  -  Total Bytes Sent By Job
760395  -  Total Bytes Sent By Job
760397  -  Total Bytes Sent By Job
760395  -  Total Bytes Sent By Job

[username@ap40]$ grep '<PATTERN REMOVED>' my-job.log
19240  -  Total Bytes Received By Job
19240  -  Total Bytes Received By Job
19240  -  Total Bytes Received By Job
19240  -  Total Bytes Received By Job
19240  -  Total Bytes Received By Job
19240  -  Total Bytes Received By Job
19240  -  Total Bytes Received By Job
19240  -  Total Bytes Received By Job
```

Job log files may also contain additional information about held jobs or interrupted jobs. If you feel that your jobs are bouncing from idle to running and back to idle, or that they are otherwise not making as much progress as you expect, the log files are a good place to check. Though they might eventually become impossibly large to read line-by-line once you start scaling up, using `grep` to pull out specific lines and using `sort` and `uniq` to reduce the output can help you make sense of the information contained in the logs.
