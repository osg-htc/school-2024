---
status: testing
---

# OSG Exercise 1.3: Hardware Differences Between PATh and OSG

The goal of this exercise is to compare hardware differences between the Monday cluster
(the PATh Facility) and the Open Science Pool.
Specifically, we will look at how easy it is to get access to resources
in terms of the amount of memory that is requested.
This will not be a very careful study,
but should give you some idea of one way in which the pools are different.

In the first two parts of the exercise,
you will submit batches of jobs that differ only in how much memory each one requests.
This is called this a *parameter sweep*, in that we are testing many possible values of a parameter.
We will request memory from 8–64 GB, doubling the memory each time.
One set of jobs will be submitted to the PATh Facility, and the other, identical set of jobs will be submitted to the OSPool.
You will check the queue periodically to see how many jobs have completed and how many are still waiting to run.

## Checking PATh memory availability

In this first part, you will create the submit file that will be used for both the PATh and OSPool jobs,
then submit the PATh set.

### Yet another queue syntax

Earlier, you learned about the `queue` statement
and some of the different ways it can be invoked to submit multiple jobs.
Similar to the `queue from` statement to submit jobs based on lines from a specific file,
you can use `queue in` to submit jobs based on a list that is written directly in your submit file:

```
queue <# of jobs> <variable> in (
<item 1>
<item 2>
<item 3>
...
)
```

For example, to submit 6 total jobs that sleep for `5`, `5`, `10`, `10`, `15`, and `15` seconds,
you could write the following submit file:

```
executable = /bin/sleep

request_cpus = 1
request_memory = 1MB
request_disk = 1MB

queue 2 arguments in (
5
10
15
)
```

Try submitting this yourself and verify that all six jobs are in the queue,
using the `condor_q -nobatch` command.

### Create the submit file

To create our parameter sweep,
we will create a **new** submit file with the queue…in syntax
and change the value of our parameter (`request_memory`) for each batch of jobs.

1.  Log in or switch back to `ap1.facility.path-cc.io` (yes, back to PATh!)
1.  Create and change into a new subdirectory called `osg-ex13`
1.  Create a submit file named `sleep.sub` that executes the command `/bin/sleep 300`.

    !!! note
        If you do not remember all of the submit statements to write this file, or just to go faster,
        find a similar submit file from a previous exercise.
        Copy the file and rename it here, and make sure the argument to `sleep` is `300`.

1.  Use the queue…in syntax to submit 10 jobs *each* for the following memory requests: 8, 16, 32, and 64&nbsp;GB.
    There will be 40 jobs total: 10 jobs requesting 8&nbsp;GB, 10 requesting 16&nbsp;GB, etc.
1.  Submit your jobs

### Monitoring the local jobs

Every few minutes, run `condor_q` and see how your sleep jobs are doing.
To display the number of jobs remaining for each `request_memory` parameter specified, 
run the following command:

``` console
$ condor_q <Cluster ID> -af RequestMemory | sort -n | uniq -c
```

The numbers in the left column are the number of jobs left of that type
and the number on the right is the amount of memory you requested, in MB.
Consider making a little table like the one below to track progress.

| Memory | Remaining \#1 | Remaining \#2 | Remaining \#3 |
|:-------|:--------------|:--------------|:--------------|
| 8 GB   | 10            | 6             |               |
| 16 GB  | 10            | 7             |               |
| 32 GB  | 10            | 8             |               |
| 64 GB  | 10            | 9             |               |

In the meantime, between checking on your local jobs, start the next section –
but take a break every few minutes to switch back to `ap1` and record progress on your PATh jobs.

## Checking OSPool memory availability

Now you will do essentially the same thing on the OSPool.

1.  Log in or switch to `ap40.uw.osg-htc.org`

1.  Copy the `osg-ex13` directory from the [section above](#checking-path-memory-availability)
    from `ap1.facility.path-cc.io` to `ap40.uw.osg-htc.org`

    If you get stuck during the copying process, refer to [OSG exercise 1.1](part1-ex1-login-scp.md).

1.  Submit the jobs to the OSPool

### Monitoring the remote jobs

As you did in the first part, use `condor_q` to track how your sleep jobs are doing.
It is fine to move on to the next exercise, but keep tracking the status of both sets of these jobs.
After you are done with the [next exercise](part1-ex4-software-diffs.md),
come back to this exercise and analyze the results.

## Analyzing the results

Have all of your jobs from this exercise completed on both PATh and the OSPool?
How many jobs have completed thus far on PATh?
How many have completed thus far on the OSPool?

Due to the dynamic nature of the OSPool,
the demand for higher memory jobs there may have resulted in a temporary increase in high-memory slots there.
That being said, high-memory are a high-demand, low-availability resource in the OSPool
so your 64&nbsp;GB jobs may have taken longer to run or complete.
On the other hand, PATh has a fair number of 64&nbsp;GB (and greater) slots
so all your jobs have a high chance of running.
