---
status: testing
---

<style type="text/css">
  pre em { font-style: normal; background-color: yellow; }
  pre strong { font-style: normal; font-weight: bold; color: \#008; }
</style>

# Workflows Exercise 1.1: Coordinating a Set of Jobs With a Simple DAG

The objective of this exercise is to learn the very basics of running a set of jobs, where our set is just one job.

## What is DAGMan?

In short, DAGMan lets you submit complex sequences of jobs as long as they can be expressed as a directed acylic graph.
For example, you may wish to run a large parameter sweep but before the sweep run you need to prepare your data.  After
the sweep runs, you need to collate the results.  This might look like this, assuming you want to sweep over five
parameters:

![simple DAG](files/osgvsp20-workflows-part1-ex1-simple-dag.gif)

DAGMan has many abilities, such as throttling jobs, recovery from failures, and more.  More information about DAGMan can
be found at [in the HTCondor manual](https://htcondor.readthedocs.io/en/latest/automated-workflows/index.html).

## Submitting a Simple DAG

For our job, we will return briefly to the `sleep` program, name it `job.sub`

``` file
executable              = /bin/sleep
arguments               = 4
log                     = simple.log
output                  = simple.out
error                   = simple.error
request_memory = 1GB
request_disk = 1GB
request_cpus = 1
queue
```

We are going to get a bit more sophisticated in submitting our jobs now.  Let's have three windows open.  In one window,
you'll submit the job.  In another you will watch the queue, and in the third you will watch what DAGMan does.

First we will create the most minimal DAG that can be created: a DAG with just one node.  Put this into a file named
`simple.dag`.

``` file
JOB Simple job.sub
```

In your first window, submit the DAG:

``` console
username@ap40 $ condor_submit_dag simple.dag
-----------------------------------------------------------------------
File for submitting this DAG to Condor           : simple.dag.condor.sub
Log of DAGMan debugging messages                 : simple.dag.dagman.out
Log of Condor library output                     : simple.dag.lib.out
Log of Condor library error messages             : simple.dag.lib.err
Log of the life of condor_dagman itself          : simple.dag.dagman.log

Submitting job(s).
1 job(s) submitted to cluster 61.
-----------------------------------------------------------------------
```

In the second window, check the queue (what you see may be slightly different):

``` console
username@ap40 $ condor_q -nobatch -wide:80

-- Submitter: learn.chtc.wisc.edu : <128.104.100.55:9618?sock=28867_10e4_2> : learn.chtc.wisc.edu
 ID      OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD               
  61.0   roy             6/21 22:51   0+00:03:47 R  0   0.3  condor_dagman     
  62.0   roy             6/21 22:51   0+00:00:03 R  0   0.7  simple 4 10      

2 jobs; 0 completed, 0 removed, 0 idle, 2 running, 0 held, 0 suspended
```

In the third window, watch what DAGMan does (what you see may be slightly different):

``` console
username@ap40 $ tail -f --lines=500 simple.dag.dagman.out
08/02/24 15:44:57 ******************************************************
08/02/24 15:44:57 ** condor_scheduniv_exec.271100.0 (CONDOR_DAGMAN) STARTING UP
08/02/24 15:44:57 ** /usr/bin/condor_dagman
08/02/24 15:44:57 ** SubsystemInfo: name=DAGMAN type=DAGMAN(9) class=CLIENT(2)
08/02/24 15:44:57 ** Configuration: subsystem:DAGMAN local:<NONE> class:CLIENT
08/02/24 15:44:57 ** $CondorVersion: 10.7.0 2024-07-10 BuildID: 659788 PackageID: 10.7.0-0.659788 RC $
08/02/24 15:44:57 ** $CondorPlatform: x86_64_AlmaLinux8 $
08/02/24 15:44:57 ** PID = 2340103
08/02/24 15:44:57 ** Log last touched time unavailable (No such file or directory)
08/02/24 15:44:57 ******************************************************
08/02/24 15:44:57 Daemon Log is logging: D_ALWAYS D_ERROR D_STATUS
08/02/24 15:44:57 DaemonCore: No command port requested.
08/02/24 15:44:57 DAGMAN_USE_STRICT setting: 1
08/02/24 15:44:57 DAGMAN_VERBOSITY setting: 3
08/02/24 15:44:57 DAGMAN_DEBUG_CACHE_SIZE setting: 5242880
08/02/24 15:44:57 DAGMAN_DEBUG_CACHE_ENABLE setting: False
08/02/24 15:44:57 DAGMAN_SUBMIT_DELAY setting: 0
08/02/24 15:44:57 DAGMAN_MAX_SUBMIT_ATTEMPTS setting: 6
08/02/24 15:44:57 DAGMAN_STARTUP_CYCLE_DETECT setting: False
08/02/24 15:44:57 DAGMAN_MAX_SUBMITS_PER_INTERVAL setting: 100
08/02/24 15:44:57 DAGMAN_AGGRESSIVE_SUBMIT setting: False
08/02/24 15:44:57 DAGMAN_USER_LOG_SCAN_INTERVAL setting: 5
08/02/24 15:44:57 DAGMAN_QUEUE_UPDATE_INTERVAL setting: 300
08/02/24 15:44:57 DAGMAN_DEFAULT_PRIORITY setting: 0
08/02/24 15:44:57 DAGMAN_SUPPRESS_NOTIFICATION setting: True
08/02/24 15:44:57 allow_events (DAGMAN_ALLOW_EVENTS) setting: 114
08/02/24 15:44:57 DAGMAN_RETRY_SUBMIT_FIRST setting: True
08/02/24 15:44:57 DAGMAN_RETRY_NODE_FIRST setting: False
08/02/24 15:44:57 DAGMAN_MAX_JOBS_IDLE setting: 1000
08/02/24 15:44:57 DAGMAN_MAX_JOBS_SUBMITTED setting: 0
08/02/24 15:44:57 DAGMAN_MAX_PRE_SCRIPTS setting: 20
08/02/24 15:44:57 DAGMAN_MAX_POST_SCRIPTS setting: 20
08/02/24 15:44:57 DAGMAN_MAX_HOLD_SCRIPTS setting: 20
08/02/24 15:44:57 DAGMAN_MUNGE_NODE_NAMES setting: True
08/02/24 15:44:57 DAGMAN_PROHIBIT_MULTI_JOBS setting: False
08/02/24 15:44:57 DAGMAN_SUBMIT_DEPTH_FIRST setting: False
08/02/24 15:44:57 DAGMAN_ALWAYS_RUN_POST setting: False
08/02/24 15:44:57 DAGMAN_CONDOR_SUBMIT_EXE setting: /usr/bin/condor_submit
08/02/24 15:44:57 DAGMAN_USE_DIRECT_SUBMIT setting: True
08/02/24 15:44:57 DAGMAN_DEFAULT_APPEND_VARS setting: False
08/02/24 15:44:57 DAGMAN_ABORT_DUPLICATES setting: True
08/02/24 15:44:57 DAGMAN_ABORT_ON_SCARY_SUBMIT setting: True
08/02/24 15:44:57 DAGMAN_PENDING_REPORT_INTERVAL setting: 600
08/02/24 15:44:57 DAGMAN_AUTO_RESCUE setting: True
08/02/24 15:44:57 DAGMAN_MAX_RESCUE_NUM setting: 100
08/02/24 15:44:57 DAGMAN_WRITE_PARTIAL_RESCUE setting: True
08/02/24 15:44:57 DAGMAN_DEFAULT_NODE_LOG setting: @(DAG_DIR)/@(DAG_FILE).nodes.log
08/02/24 15:44:57 DAGMAN_GENERATE_SUBDAG_SUBMITS setting: True
08/02/24 15:44:57 DAGMAN_MAX_JOB_HOLDS setting: 100
08/02/24 15:44:57 DAGMAN_HOLD_CLAIM_TIME setting: 20
08/02/24 15:44:57 ALL_DEBUG setting: 
08/02/24 15:44:57 DAGMAN_DEBUG setting: 
08/02/24 15:44:57 DAGMAN_SUPPRESS_JOB_LOGS setting: False
08/02/24 15:44:57 DAGMAN_REMOVE_NODE_JOBS setting: True
08/02/24 15:44:57 DAGMAN will adjust edges after parsing
08/02/24 15:44:57 argv[0] == "condor_scheduniv_exec.271100.0"
08/02/24 15:44:57 argv[1] == "-Lockfile"
08/02/24 15:44:57 argv[2] == "simple.dag.lock"
08/02/24 15:44:57 argv[3] == "-AutoRescue"
08/02/24 15:44:57 argv[4] == "1"
08/02/24 15:44:57 argv[5] == "-DoRescueFrom"
08/02/24 15:44:57 argv[6] == "0"
08/02/24 15:44:57 argv[7] == "-Dag"
08/02/24 15:44:57 argv[8] == "simple.dag"
08/02/24 15:44:57 argv[9] == "-Suppress_notification"
08/02/24 15:44:57 argv[10] == "-CsdVersion"
08/02/24 15:44:57 argv[11] == "$CondorVersion: 10.7.0 2024-07-10 BuildID: 659788 PackageID: 10.7.0-0.659788 RC $"
08/02/24 15:44:57 argv[12] == "-Dagman"
08/02/24 15:44:57 argv[13] == "/usr/bin/condor_dagman"
08/02/24 15:44:57 Default node log file is: </home/mats.rynge/dagman-1/./simple.dag.nodes.log>
08/02/24 15:44:57 DAG Lockfile will be written to simple.dag.lock
08/02/24 15:44:57 DAG Input file is simple.dag
08/02/24 15:44:57 Parsing 1 dagfiles
08/02/24 15:44:57 Parsing simple.dag ...
08/02/24 15:44:57 Adjusting edges
08/02/24 15:44:57 Dag contains 1 total jobs
08/02/24 15:44:57 Bootstrapping...
08/02/24 15:44:57 Number of pre-completed nodes: 0
08/02/24 15:44:57 MultiLogFiles: truncating log file /home/mats.rynge/dagman-1/./simple.dag.nodes.log
08/02/24 15:44:57 DAG status: 0 (DAG_STATUS_OK)
08/02/24 15:44:57 Of 1 nodes total:
08/02/24 15:44:57  Done     Pre   Queued    Post   Ready   Un-Ready   Failed   Futile
08/02/24 15:44:57   ===     ===      ===     ===     ===        ===      ===      ===
08/02/24 15:44:57     0       0        0       0       1          0        0        0
08/02/24 15:44:57 0 job proc(s) currently held
08/02/24 15:44:57 Registering condor_event_timer...
08/02/24 15:44:58 Submitting HTCondor Node Simple job(s)...
```

<span style="color:RED">**Here's where the job is submitted**</span>

```file
08/02/24 15:44:58 Submitting HTCondor Node Simple job(s)...
08/02/24 15:44:58 Submitting node Simple from file job.sub using direct job submission
08/02/24 15:44:58       assigned HTCondor ID (271101.0.0)
08/02/24 15:44:58 Just submitted 1 job this cycle...
```

<span style="color:RED">**Here's where DAGMan noticed that the job is running**</span>

```file
08/02/24 15:45:18 Event: ULOG_EXECUTE for HTCondor Node Simple (271101.0.0) {08/02/24 15:45:14}
08/02/24 15:45:18 Number of idle job procs: 0
```

<span style="color:RED">**Here's where DAGMan noticed that the job finished.**</span>

```file
08/02/24 15:45:23 Event: ULOG_JOB_TERMINATED for HTCondor Node Simple (271101.0.0) {08/02/24 15:45:19}
08/02/24 15:45:23 Number of idle job procs: 0
08/02/24 15:45:23 Node Simple job proc (271101.0.0) completed successfully.
08/02/24 15:45:23 Node Simple job completed
08/02/24 15:45:23 DAG status: 0 (DAG_STATUS_OK)
08/02/24 15:45:23 Of 1 nodes total:
08/02/24 15:45:23  Done     Pre   Queued    Post   Ready   Un-Ready   Failed   Futile
08/02/24 15:45:23   ===     ===      ===     ===     ===        ===      ===      ===
08/02/24 15:45:23     1       0        0       0       0          0        0        0
```

<span style="color:RED">**Here's where DAGMan noticed that all the work is done.**</span>

```file
08/02/24 15:45:23 All jobs Completed!
08/02/24 15:45:23 Note: 0 total job deferrals because of -MaxJobs limit (0)
08/02/24 15:45:23 Note: 0 total job deferrals because of -MaxIdle limit (1000)
08/02/24 15:45:23 Note: 0 total job deferrals because of node category throttles
08/02/24 15:45:23 Note: 0 total PRE script deferrals because of -MaxPre limit (20) or DEFER
08/02/24 15:45:23 Note: 0 total POST script deferrals because of -MaxPost limit (20) or DEFER
08/02/24 15:45:23 Note: 0 total HOLD script deferrals because of -MaxHold limit (20) or DEFER
```

Now verify your results:

``` console
username@ap40 $ cat simple.log
000 (271101.000.000) 2024-08-02 15:44:58 Job submitted from host: <128.105.68.92:9618?addrs=128.105.68.92-9618+[2607-f388-2200-100-eaeb-d3ff-fe40-111c]-9618&alias=ap40.uw.osg-htc.org&noUDP&sock=schedd_35391_dc5c>
    DAG Node: Simple
...
040 (271101.000.000) 2024-08-02 15:45:13 Started transferring input files
        Transferring to host: <10.136.81.233:37425?CCBID=128.104.103.162:9618%3faddrs%3d128.104.103.162-9618%26alias%3dospool-ccb.osg.chtc.io%26noUDP%26sock%3dcollector4#23067238%20192.170.231.9:9618%3faddrs%3d192.170.231.9-9618+[fd85-ee78-d8a6-8607--1-73b6]-9618%26alias%3dospool-ccb.osgprod.tempest.chtc.io%26noUDP%26sock%3dcollector10#1512850&PrivNet=comp-cc-0463.gwave.ics.psu.edu&addrs=10.136.81.233-37425&alias=comp-cc-0463.gwave.ics.psu.edu&noUDP>
...
040 (271101.000.000) 2024-08-02 15:45:13 Finished transferring input files
...
021 (271101.000.000) 2024-08-02 15:45:14 Warning from starter on slot1_4@glidein_2635188_104012775@comp-cc-0463.gwave.ics.psu.edu:
        PREPARE_JOB (prepare-hook) succeeded (reported status 000): Using default Singularity image /cvmfs/singularity.opensciencegrid.org/htc/rocky:8-cuda-11.0.3
...
001 (271101.000.000) 2024-08-02 15:45:14 Job executing on host: <10.136.81.233:39645?CCBID=128.104.103.162:9618%3faddrs%3d128.104.103.162-9618%26alias%3dospool-ccb.osg.chtc.io%26noUDP%26sock%3dcollector10#1506459%20192.170.231.9:9618%3faddrs%3d192.170.231.9-9618+[fd85-ee78-d8a6-8607--1-73b4]-9618%26alias%3dospool-ccb.osgprod.tempest.chtc.io%26noUDP%26sock%3dcollector10#1506644&PrivNet=comp-cc-0463.gwave.ics.psu.edu&addrs=10.136.81.233-39645&alias=comp-cc-0463.gwave.ics.psu.edu&noUDP>
        SlotName: slot1_4@comp-cc-0463.gwave.ics.psu.edu
        CondorScratchDir = "/localscratch/condor/execute/dir_2635172/glide_uZ6qXM/execute/dir_3252113"
        Cpus = 1
        Disk = 2699079
        GLIDEIN_ResourceName = "PSU-LIGO"
        Memory = 1024
...
006 (271101.000.000) 2024-08-02 15:45:19 Image size of job updated: 2296464
        47  -  MemoryUsage of job (MB)
        47684  -  ResidentSetSize of job (KB)
...
040 (271101.000.000) 2024-08-02 15:45:19 Started transferring output files
...
040 (271101.000.000) 2024-08-02 15:45:19 Finished transferring output files
...
005 (271101.000.000) 2024-08-02 15:45:19 Job terminated.
        (1) Normal termination (return value 0)
                Usr 0 00:00:00, Sys 0 00:00:00  -  Run Remote Usage
                Usr 0 00:00:00, Sys 0 00:00:00  -  Run Local Usage
                Usr 0 00:00:00, Sys 0 00:00:00  -  Total Remote Usage
                Usr 0 00:00:00, Sys 0 00:00:00  -  Total Local Usage
        0  -  Run Bytes Sent By Job
        38416  -  Run Bytes Received By Job
        0  -  Total Bytes Sent By Job
        38416  -  Total Bytes Received By Job
        Partitionable Resources :    Usage  Request Allocated 
           Cpus                 :                 1         1 
           Disk (KB)            :      149  1048576   2699079 
           Memory (MB)          :       47     1024      1024 

        Job terminated of its own accord at 2024-08-02T20:45:19Z with exit-code 0.
...
```

Looking at DAGMan's various files, we see that DAGMan itself ran as a job (specifically, a "scheduler" universe job).

``` console
username@ap40 $ ls simple.dag.*
simple.dag.condor.sub  simple.dag.dagman.log  simple.dag.dagman.out  simple.dag.lib.err  simple.dag.lib.out 

username@ap40 $ cat simple.dag.condor.sub
# Filename: simple.dag.condor.sub
# Generated by condor_submit_dag simple.dag 
universe        = scheduler
executable      = /usr/bin/condor_dagman
getenv          = CONDOR_CONFIG,_CONDOR_*,PATH,PYTHONPATH,PERL*,PEGASUS_*,TZ,HOME,USER,LANG,LC_ALL
output          = simple.dag.lib.out
error           = simple.dag.lib.err
log             = simple.dag.dagman.log
remove_kill_sig = SIGUSR1
+OtherJobRemoveRequirements     = "DAGManJobId =?= $(cluster)"
# Note: default on_exit_remove expression:
# ( ExitSignal =?= 11 || (ExitCode =!= UNDEFINED && ExitCode >=0 && ExitCode <= 2))
# attempts to ensure that DAGMan is automatically
# requeued by the schedd if it exits abnormally or
# is killed (e.g., during a reboot).
on_exit_remove  = (ExitSignal =?= 11 || (ExitCode =!= UNDEFINED && ExitCode >=0 && ExitCode <= 2))
copy_to_spool   = False
arguments       = "-p 0 -f -l . -Lockfile simple.dag.lock -AutoRescue 1 -DoRescueFrom 0 -Dag simple.dag -Suppress_notification -CsdVersion $CondorVersion:' '10.7.0' '2024-07-10' 'BuildID:' '659788' 'PackageID:' '10.7.0-0.659788' 'RC' '$ -Dagman /usr/bin/condor_dagman"
environment     = "_CONDOR_DAGMAN_LOG=simple.dag.dagman.out _CONDOR_MAX_DAGMAN_LOG=0 _CONDOR_SCHEDD_ADDRESS_FILE=/var/lib/condor/spool/.schedd_address _CONDOR_SCHEDD_DAEMON_AD_FILE=/var/lib/condor/spool/.schedd_classad"
queue
```

If you want to clean up some of these files (you may not want to, at least not yet), run:

``` console
username@ap40 $ rm simple.dag.*
```

## Challenge

- What is the scheduler universe? Why does DAGMan use it?

??? "Show hint"

    1.  HTCondor has several [universes](https://htcondor.readthedocs.io/en/latest/users-manual/choosing-an-htcondor-universe.html) 

    1.  What would happen to your DAGMan workflow if the access point has to be rebooted?

    1.  Jobs in the HTCondor queue are "managed" - they are always tracked, and restarted automatically if needed

    
