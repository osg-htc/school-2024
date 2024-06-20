---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

HTC Exercise 1.2: Experiment With HTCondor Commands
===================================================

Exercise Goal
-------------

The goal of this exercise is to learn about two very important HTCondor commands, `condor_q` and `condor_status`.
They will be useful for monitoring your jobs and available execute point slots (respectively) throughout the week.

This exercise should take only a few minutes.

Viewing Slots
-------------

As discussed in the lecture, the `condor_status` command is used to view the current state of slots in an HTCondor pool.

At its most basic, the command is:

``` console
username@ap1 $ condor_status
```

When running this command, there is typically a lot of output printed to the screen. Looking at your terminal output, there is one line per execute point slot. **TIP: You can widen your terminal window, which may help you to see all details of the output better.**

*Here is some example output (what you see will be longer):*

``` console
slot1@FIU-PATH-EP.osgvo-docker-pilot-55c74f5b7c-kbs77      LINUX      X86_64 Unclaimed Idle      0.000 8053  0+01:14:34
slot1@UNL-PATH-EP.osgvo-docker-pilot-9489b6b4-9rf4n        LINUX      X86_64 Claimed   Busy      0.930 1024  0+02:42:08
slot1@WISC-PATH-EP.osgvo-docker-pilot-7b46dbdbb7-xqkkg     LINUX      X86_64 Claimed   Busy      3.530 1024  0+02:40:24
slot1@SYRA-PATH-EP.osgvo-docker-pilot-gpu-7f6c64d459       LINUX      X86_64 Owner     Idle      0.300  250  7+03:22:21
```

This output consists of 8 columns:

| Col        | Example                      | Meaning                                                                                                                 |
|:-----------|:-----------------------------|:------------------------------------------------------------------------------------------------------------------------|
| Name       | `slot1@UNL-PATH-EP.osgvo-docker-pilot-9489b6b4-9rf4n` | Full slot name (including the hostname)                                                                                                  |
| OpSys      | `LINUX`                      | Operating system                                                                                                        |
| Arch       | `X86_64`                     | Slot architecture (e.g., Intel 64 bit)                                                                               |
| State      | `Claimed`                    | State of the slot (`Unclaimed` is available, `Owner` is being used by the machine owner, `Claimed` is matched to a job) |
| Activity   | `Busy`                       | Is there activity on the slot?                                                                                          |
| LoadAv     | `0.930`                      | Load average, a measure of CPU activity on the slot                                                                     |
| Mem        | `1024`                       | Memory available to the slot, in MB                                                                                     |
| ActvtyTime | `0+02:42:08`                 | Amount of time spent in current activity (days + hours:minutes:seconds)                                                 |

At the end of the slot listing, there is a summary. Here is an example:

``` console
                     Machines Owner Claimed Unclaimed Matched Preempting  Drain

        X86_64/LINUX    10831     0   10194       631       0          0      6
      X86_64/WINDOWS        2     2       0         0       0          0      0

               Total    10833     2   10194       631       0          0      6
```

There is one row of summary for each machine (i.e. "slot") architecture/operating system combination with columns for the number of slots in each state. The final row gives a summary of slot states for the whole pool.

### Questions:

-   When you run `condor_status`, how many 64-bit Linux slots are available? (Hint: Unclaimed = available.)
-   What percent of the total slots are currently claimed by a job? (Note: there is a rapid turnover of slots, which is what allows users with new submission to have jobs start quickly.)
-   How have these numbers changed (if at all) when you run the `condor_status` command again?

### Viewing Whole Machines, Only

Also try out the `-compact` for a slightly different view of whole machines (i.e. server hostnames), without the individual slots shown.

``` console
username@ap1 $ condor_status -compact
```

**How has the column information changed?**

Viewing Jobs
------------

The `condor_q` command lists jobs that are on this access point machine and that are running or waiting to run. The `_q` part of the name is meant to suggest the word “queue”, or list of job sets *waiting* to finish.

### Viewing Your Own Jobs

The default behavior of the command lists only your jobs:

``` console
username@ap1 $ condor_q
```

The main part of the output (which will be empty, because you haven't submitted jobs yet) shows one set ("batch") of submitted jobs per line. If you had a single job in the queue, it would look something like the below:

``` console
-- Schedd: ap1.facility.path-cc.io : <128.104.100.43:9618?... @ 07/12/23 09:59:31
OWNER  BATCH_NAME            SUBMITTED   DONE   RUN    IDLE  TOTAL JOB_IDS
alice CMD: run_ffmpeg.sh   7/12 09:58      _      _      1      1 18801.0               
```

This output consists of 8 (or 9) columns:

| Col         | Example         | Meaning                                                                                                                        |
|:------------|:----------------|:-------------------------------------------------------------------------------------------------------------------------------|
| OWNER       | `alice`        | The user ID of the user who submitted the job                                                                                  |
| BATCH\_NAME | `run_ffmpeg.sh` | The executable or "jobbatchname" specified within the submit file(s)                                                           |
| SUBMITTED   | `7/12 09:58`    | The date and time when the job was submitted                                                                                   |
| DONE        | `_`             | Number of jobs in this batch that have completed                                                                               |
| RUN         | `_`             | Number of jobs in this batch that are currently running                                                                        |
| IDLE        | `1`             | Number of jobs in this batch that are idle, waiting for a match                                                                |
| HOLD        | `_`             | Column will show up if there are jobs on "hold" because something about the submission/setup needs to be corrected by the user |
| TOTAL       | `1`             | Total number of jobs in this batch                                                                                             |
| JOB\_IDS    | `18801.0`       | Job ID or range of Job IDs in this batch                                                                                       |

At the end of the job listing, there is a summary. Here is a sample:

``` console
1 jobs; 0 completed, 0 removed, 1 idle, 0 running, 0 held, 0 suspended
```

It shows total counts of jobs in the different possible states.

**Questions:**

-   For the sample above, when was the job submitted?
-   For the sample above, was the job running or not yet? How can you tell?

### Viewing Everyone’s Jobs

By default, the `condor_q` command shows **your** jobs only. To see everyone’s jobs that are queued on the machine, add the `-all` option:

``` console
username@ap1 $ condor_q -all
```

-   How many jobs are queued in total (i.e., running or waiting to run)?
-   How many jobs from this submit machine are running right now?

### Viewing Jobs without the Default "batch" Mode

The `condor_q` output, by default, groups "batches" of jobs together (if they were submitted with the same submit file or "jobbatchname"). To see more information for EVERY job on a separate line of output, use the `-nobatch` option to `condor_q`:

``` console
username@ap1 $ condor_q -all -nobatch
```

**How has the column information changed?** (Below is an example of the top of the output.)

``` console
-- Schedd: ap1.facility.path-cc.io : <128.104.100.43:9618?... @ 07/12/23 11:58:44
 ID       OWNER            SUBMITTED     RUN_TIME ST PRI SIZE   CMD
18203.0   s16_alirezakho  7/11 09:51   0+00:00:00 I  0      0.7 pascal
18204.0   s16_alirezakho  7/11 09:51   0+00:00:00 I  0      0.7 pascal
18801.0   alice           7/12 09:58   0+00:00:00 I  0      0.0 run_ffmpeg.sh
18997.0   s16_martincum   7/12 10:59   0+00:00:32 I  0    733.0 runR.pl 1_0 run_perm.R 1 0 10
19027.5   s16_martincum   7/12 11:06   0+00:09:20 I  0   2198.0 runR.pl 1_5 run_perm.R 1 5 1000
```

The `-nobatch` output shows a line for every job and consists of 8 columns:

| Col       | Example         | Meaning                                                                        |
|:----------|:----------------|:-------------------------------------------------------------------------------|
| ID        | `18801.0`       | Job ID, which is the `cluster`, a dot character (`.`), and the `process`       |
| OWNER     | `alice`         | The user ID of the user who submitted the job                                  |
| SUBMITTED | `7/12 09:58`    | The date and time when the job was submitted                                   |
| RUN\_TIME | `0+00:00:00`    | Total time spent running so far (days + hours:minutes:seconds)                 |
| ST        | `I`             | Status of job: `I` is Idle (waiting to run), `R` is Running, `H` is Held, etc. |
| PRI       | `0`             | Job priority (see next lecture)                                                |
| SIZE      | `0.0`           | Current run-time memory usage, in MB                                           |
| CMD       | `run_ffmpeg.sh` | The executable command (with arguments) to be run                              |

**In future exercises, you'll want to switch between `condor_q` and `condor_q -nobatch` to see different types of information about YOUR jobs.**

Extra Information
-----------------

Both `condor_status` and `condor_q` have many command-line options, some of which significantly change their output.
You will explore a few of the most useful options in future exercises, but if you want to experiment now, go ahead!
There are a few ways to learn more about the commands:

-   Use the (brief) built-in help for the commands, e.g.: `condor_q -h`
-   Read the installed man(ual) pages for the commands, e.g.: `man condor_q`
-   Find the command in [the online manual](https://htcondor.readthedocs.io/en/latest/); **note:** the text online is the same as the `man` text, only formatted for the web


