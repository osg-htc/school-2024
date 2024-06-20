# Exercise 1.2: Investigating Job Attributes

The objective of this exercise is to your awareness of job "class ad attributes",
especially ones that may help you look for issues with your jobs in the OSPool.

Recall that a job class ad contains attributes and their values that describe what HTCondor knows about the job.
OSPool jobs contain extra attributes that are specific to that pool.
Thus, an OSPool job class ad may have well over 150 attributes.

Some OSPool job attributes are especially helpful when you are scaling up jobs
and want to see if jobs are running as expected or are maybe doing surprising things that are worth extra attention.

## Preparing exercise files

Because this exercise focuses on OSPool job attributes, please use your OSPool account on `ap40.uw.osg-htc.org`. 

1.  Create a shell script for testing called `simple.sh`:

        :::file
        #!/bin/bash

        SLEEPTIME=$1

        hostname
        pwd
        whoami

        for i in {1..5}
        do 
         echo "performing iteration $i"
         sleep $SLEEPTIME
        done

1.  Create an HTCondor submit file that queues three jobs:

        :::file 
        universe = vanilla
        log = logs/$(Cluster)_$(Process).log
        error = logs/$(Cluster)_$(Process).err
        output = $(Cluster)_$(Process).out

        executable = simple.sh

        should_transfer_files = YES
        when_to_transfer_output = ON_EXIT

        request_cpus = 1
        request_memory = 1GB
        request_disk = 1GB

        # set arguments, queue a normal job
        arguments = 600
        queue 1

        # queue a job that will go on hold
        transfer_input_files = test.txt
        queue 1

        # queue a job that will never start
        request_memory = 40TB
        queue 1

## Exploring OSPool job class ad attributes

For this exercise, you will submit the three jobs defined in the submit file above,
then examine their job class ad attributes.

Here are some attributes that may be interesting:

*   `CpusProvisioned` is the number of CPUs given to your job for the current or most recent run
*   `ResidentSetSize_RAW` is the maximum amount of memory that HTCondor has noticed your job using (in KB)
*   `DiskUsage_RAW` is the maximum amount of disk that HTCondor has noticed your job using (in MB)
*   `NumJobStarts` is the number of times HTCondor has started your job; `1` is typical for a running job, and higher counts may indicate issues running the job
*   `LastRemoteHost` identifies the name for the slot where your job is running or most recently ran
*   `MachineAttrGLIDEIN_ResourceName*N*` is a set of numbered attributes that identify the most recent sites where your job ran; *N* is `0` for the most recent (or current) run, `1` for the previous run, and so on up to `9`
*   `ExitCode` exists only if your job exited (completed) at least once; a value of `0` typically means success
*   `HoldReasonCode` exists only if your job went on hold; if so, it is a number corresponding to the main hold reason
    (see [here](https://htcondor.readthedocs.io/en/latest/classad-attributes/job-classad-attributes.html?highlight=HoldReasonCode#job-classad-attributes) for details)
*   `NumHoldsByReason` is a list of all of the main reasons your job has gone on hold so far with counts of each hold type

Let&rsquo;s explore these attributes on real jobs.

1.  Submit the jobs (above) and note the cluster ID

1.  When one job from the cluster is running, view all of its job class ad attributes:

        :::console
        $ condor_q -l <JobId>

    where `<JobId>` is your job's ID, and `-l` stands for `-long`

    This command lists all of the job&rsquo;s class ad attributes.
    Details of some of the attributes are in the
    [HTcondor Manual](https://htcondor.readthedocs.io/en/latest/classad-attributes/job-classad-attributes.html).
    Others are defined (and not well documented) only for the OSPool.
    Can you find any of the attributes listed above?

1.  Next, use `condor_q -af <AttributeName>` to examine one attribute at a time for several jobs:

        :::console
        $ condor_q -af NumJobStarts <ClusterID>

    where `<ClusterID>` is the HTCondor cluster ID noted above, and `-af` stands for `-autoformat`.

    What does the output tell you?

1.  Finally, display several attributes at once for the jobs:

        :::console
        $ condor_q -af:j NumJobStarts DiskUsage_RAW LastRemoteHost HoldReasonCode <ClusterID>

    Why do some values appear as `undefined`?
