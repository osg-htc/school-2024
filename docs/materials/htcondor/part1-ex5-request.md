---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

HTC Exercise 1.5: Declare Resource Needs
===========================================

The goal of this exercise is to demonstrate how to test and tune the `request_X` statements in a submit file for when you don't know what resources your job needs.

There are three special resource request statements that you can use (optionally) in an HTCondor submit file:

-  `request_cpus` for the number of CPUs your job will use. A value of "1" is always a great starting point, but some software can use more than "1" (however, most softwares will use an argument to control this number).
-  `request_memory` for the maximum amount of run-time memory your job may use.
-  `request_disk` for the maximum amount of disk space your job may use (including the executable and all other data that may show up during the job).

HTCondor defaults to certain reasonable values for these request settings, so you do not need to use them to get *small* jobs to run. 
However, it is in **YOUR** best interest to always estimate resource requests before submitting any job, and to definitely tune your requests before submitting multiple jobs. In many HTCondor pools:

-  If your job goes over the request values, it may be removed from the execute machine and held (status 'H' in the `condor_q` output, awaiting action on your part) without saving any partial job output files. So it is a disadvantage to not declare your resource needs or if you underestimate them. 
-  Conversely, if you overestimate them by too much, your jobs will match to fewer slots and take longer to match to a slot to begin running. Additionally, by hogging up resources that you don't need, other users may be deprived of the resources they require. In the long run, it works better for all users of the pool if you declare what you really need.

But how do you know what to request? In particular, we are concerned with memory and disk here; requesting multiple CPUs and using them is covered a bit in later school materials, but true HTC splits work up into jobs that each use as few CPU cores as possible (one CPU core is always best to have the most jobs running).

Determining Resource Needs Before Running Any Jobs
--------------------------------------------------

!!! note
    If you are running short on time, you can skip to "Determining Resource Needs By Running Test Jobs", below, but try to come back and read over this part at some point.

It can be very difficult to predict the memory needs of your running program without running tests. Typically, the memory size of a job changes over time, making the task even trickier. 
If you have knowledge ahead of time about your job’s maximum memory needs, use that, or maybe a number that's just a bit higher, to ensure your job has enough memory to complete. If this is your first time running your job, you can request a fairly large amount of memory (as high as what's on your laptop or other server, if you know your program can run without crashing) for a first test job, OR you can run the program locally and "watch" it:

### Examining a Running Program on a Local Computer

When working on a shared access point, you should not run computationally-intensive work because it can use resources needed by HTCondor to manage the queue for all uses. 
However, you may have access to other computers (your laptop, for example, or another server) where you can observe the memory usage of a program. The downside is that you'll have to watch a program run for essentially the entire time, to make sure you catch the maximum memory usage.

#### For Memory: 

On Mac and Windows, for example, the "Activity Monitor" and "Task Manager" applications may be useful. On a Mac or Linux system, you can use the `ps` command or the `top` command in the Terminal to watch a running program and see (roughly) how much memory it is using. Full coverage of these tools is beyond the scope of this exercise, but here are two quick examples:

Using `ps`:

``` console
username@ap1 $ ps ux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
alice      24342  0.0  0.0  90224  1864 ?        S    13:39   0:00 sshd: alice@pts/0  
alice      24343  0.0  0.0  66096  1580 pts/0    Ss   13:39   0:00 -bash
alice      25864  0.0  0.0  65624   996 pts/0    R+   13:52   0:00 ps ux
alice      30052  0.0  0.0  90720  2456 ?        S    Jun22   0:00 sshd: alice@pts/2  
alice      30053  0.0  0.0  66096  1624 pts/2    Ss+  Jun22   0:00 -bash
```

The Resident Set Size (`RSS`) column, highlighted above, gives a rough indication of the memory usage (in KB) of each running process. If your program runs long enough, you can run this command several times and note the greatest value.

Using `top`:

``` console
username@ap1 $ top -u <USERNAME>
top - 13:55:31 up 11 days, 20:59,  5 users,  load average: 0.12, 0.12, 0.09
Tasks: 198 total,   1 running, 197 sleeping,   0 stopped,   0 zombie
Cpu(s):  1.2%us,  0.1%sy,  0.0%ni, 98.5%id,  0.2%wa,  0.0%hi,  0.1%si,  0.0%st
Mem:   4001440k total,  3558028k used,   443412k free,   258568k buffers
Swap:  4194296k total,      148k used,  4194148k free,  2960760k cached

  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
24342 alice       15   0 90224 1864 1096 S  0.0  0.0   0:00.26 sshd
24343 alice       15   0 66096 1580 1232 S  0.0  0.0   0:00.07 bash
25927 alice       15   0 12760 1196  836 R  0.0  0.0   0:00.01 top
30052 alice       16   0 90720 2456 1112 S  0.0  0.1   0:00.69 sshd
30053 alice       18   0 66096 1624 1236 S  0.0  0.0   0:00.37 bash
```

The `top` command (shown here with an option to limit the output to a single user ID) also shows information about running processes, but updates periodically by itself. Type the letter `q` to quit the interactive display. Again, the highlighted `RES` column shows an approximation of memory usage.

#### For Disk: 
Determining disk needs may be a bit easier, because you can check on the size of files that a program is using while it runs. However, it is important to count all files that HTCondor counts to get an accurate size. HTCondor counts **everything** in your job sandbox toward your job’s disk usage:

-   The executable itself
-   All "input" files (anything else that gets transferred TO the job, even if you don't think of it as "input")
-   All files created during the job (broadly defined as "output"), including the captured standard output and error files that you list in the submit file.
-   All temporary files created in the sandbox, even if they get deleted by the executable before it's done.

If you can run your program within a single directory on a local computer (not on the access point), you should be able to view files and their sizes with the `ls` and `du` commands.

Determining Resource Needs By Running Test Jobs (BEST)
------------------------------------------------------

Despite the techniques mentioned above, by far the easiest approach to measuring your job’s resource needs is to run one or a small number of sample jobs and have HTCondor itself tell you about the resources used during the runs.

For example, here is a strange Python script that does not do anything useful, but consumes some real resources while running:

``` python
#!/usr/bin/env python3
import time
import os
size = 1000000
numbers = []
for i in range(size): numbers.append(str(i))
with open('numbers.txt', 'w') as tempfile:
    tempfile.write(' '.join(numbers))
time.sleep(60)
```

Without trying to figure out what this code does or how many resources it uses, create a submit file for it, 
and run it once with HTCondor, starting with somewhat high memory requests ("1GB" for memory and disk is a good starting point, unless you think the job will use far more).
When it is done, examine the log file. In particular, we care about these lines:

``` file
    Partitionable Resources :    Usage  Request Allocated
       Cpus                 :                 1         1
       Disk (KB)            :     6739  1048576   8022934
       Memory (MB)          :        3     1024      1024
```

So, now we know that HTCondor saw that the job used 6,739 KB of disk (= about 6.5 MB) and 3 MB of memory!

This is a great technique for determining the real resource needs of your job. If you think resource needs vary from run to run, submit a few sample jobs and look at all the results. You should round up your resource requests a little, just in case your job occasionally uses more resources.

Setting Resource Requirements
-----------------------------

Once you know your job’s resource requirements, it is easy to declare them in your submit file. For example, taking our results above as an example, we might slightly increase our requests above what was used, just to be safe:

```hl_lines="1 3"
# rounded up from 3 MB
request_memory = 4MB  
# rounded up from 6.5 MB
request_disk = 7MB  
```

Pay close attention to units:

-   Without explicit units, `request_memory` is in MB (megabytes)
-   Without explicit units, `request_disk` is in KB (kilobytes)
-   Allowable units are `KB` (kilobytes), `MB` (megabytes), `GB` (gigabytes), and `TB` (terabytes)

HTCondor translates these requirements into attributes that become part of the job's `requirements` expression. However, do not put your CPU, memory, and disk requirements directly into the `requirements` expression; use the `request_XXX` statements instead.

**If you still have time in this working session, Add these requirements to your submit file for the Python script, rerun the job, and confirm in the log file that your requests were used.**

After changing the requirements in your submit file, did your job run successfully? If not, why?
(Hint: HTCondor polls a job's resource use on a timer. How long are these jobs running for?)
