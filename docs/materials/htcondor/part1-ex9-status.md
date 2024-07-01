---
status: in progress
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Bonus HTC Exercise 1.9: Explore condor_status
===========================================

The goal of this exercise is try out some of the most common options to the `condor_status` command, so that you can view slots effectively.

The main part of this exercise should take just a few minutes, but if you have more time later, come back and work on the extension ideas at the end to become a `condor_status` expert!

Selecting Slots
---------------

The `condor_status` program has many options for selecting which slots are listed. You've already learned the basic `condor_status` and the `condor_status -compact` variation (which you may wish to retry now, before proceeding).

Another convenient option is to list only those slots that are available now:

``` console
username@ap1 $ condor_status -avail
```

Of course, the individual execute machines only report their slots to the collector at certain time intervals, so this list will not reflect the up-to-the-second reality of all slots. But this limitation is true of all `condor_status` output, not just with the `-avail` option.

Similar to `condor_q`, you can limit the slots that are listed in two easy ways. To list just the slots on a specific machine:

``` console
username@ap1 $ condor_status <hostname>
```

For example, if you want to see the slots on `e2337.chtc.wisc.edu` (in the CHTC pool):

``` console
username@ap1 $ condor_status e2337.chtc.wisc.edu
```

To list a specific slot on a machine:

``` console
username@ap1 $ condor_status <slot>@<hostname>
```

For example, to see the “first” slot on the machine above:

``` console
username@ap1 $ condor_status slot1@e2337.chtc.wisc.edu
```

!!! note
    You can name more than one hostname, slot, or combination thereof on the command line, in which case slots for
    **all** of the named hostnames and/or slots are listed.

Let’s get some practice using `condor_status` selections!

1.  List all slots in the pool — how many are there total?
1.  Practice using all forms of `condor_status` that you have learned:
    -   List the available slots.
    -   List the slots on a specific machine (e.g., `e2337.chtc.wisc.edu`).
    -   List a specific slot from that machine.
    -   Try listing the slots from a few (but not all) machines at once.
    -   Try using a mix of hostnames and slot IDs at once.

Viewing a Slot ClassAd
----------------------

Just as with `condor_q`, you can use `condor_status` to view the complete ClassAd for a given slot (often confusingly called the “machine” ad):

``` console
username@ap1 $ condor_status -long <slot>@<hostname>
```

Because slot ClassAds may have 150–200 attributes (or more), it probably makes the most sense to show the ClassAd for a single slot at a time, as shown above.

Here are some examples of common, interesting attributes taken directly from `condor_status` output:

``` file
OpSys = "LINUX"
DetectedCpus = 24
OpSysAndVer = "SL6"
MyType = "Machine"
LoadAvg = 0.99
TotalDisk = 798098404
OSIssue = "Scientific Linux release 6.6 (Carbon)"
TotalMemory = 24016
Machine = "e242.chtc.wisc.edu"
CondorVersion = "$CondorVersion: 8.5.5 May 03 2016 BuildID: 366162 $"
Memory = 1024
```

As you may be able to tell, there is a mix of attributes about the machine as a whole (hence the name “machine ad”) and about the slot in particular.

Go ahead and examine a machine ClassAd now.

Viewing Slots by ClassAd Expression
-----------------------------------

Often, it is helpful to view slots that meet some particular criteria. For example, if you know that your job needs a lot of memory to run, you may want to see how many high-memory slots there are and whether they are busy. You can filter the list of slots like this using the `-constraint` option and a ClassAd expression.

For example, suppose we want to list all slots that are running Scientific Linux 7 (operating system) and have at least 16 GB memory available. Note that memory is reported in units of Megabytes. The command is:

``` console
username@ap1 $ condor_status -constraint 'OpSysAndVer == "CentOS7" && Memory >= 16000'
```

!!! note
    Be very careful with using quote characters appropriately in these commands.
    In the example above, the single quotes (`'`) are for the shell, so that the entire expression is passed to
    `condor_status` untouched, and the double quotes (`"`) surround a string value within the expression itself.

Currently on PATh, there are only a few slots that meet these criteria (our high-memory servers, mainly used for metagenomics assemblies).

If you are interested in learning more about writing ClassAd expressions, look at section 4.1 and especially 4.1.4 of the HTCondor Manual. This is definitely advanced material, so if you do not want to read it, that is fine. But if you do, take some time to practice writing expressions for the `condor_status -constraint` command.

!!! note
    The `condor_q` command accepts the `-constraint` option as well!
    As you might expect, the option allows you to limit the jobs that are listed based on a ClassAd expression.

Bonus: Formatting Output
----------------------------

The `condor_status` command accepts the same `-autoformat` (`-af`) options that `condor_q` accepts, and the options have the same meanings in both commands. Of course, the attributes available in machine ads may differ from the ones that are available in job ads. Use the HTCondor Manual or look at individual slot ClassAds to get a better idea of what attributes are available.

For example, I was curious about the host name and operating system of the slots with more than 32GB of memory:

``` console
username@ap1 $ condor_status -af Machine -af OpSysAndVer -constraint 'Memory >= 32000'
```

If you like, spend a few minutes now or later experimenting with `condor_status` formatting.

References
----------

As suggested above, if you want to learn more about `condor_q`, you can do some reading:

-   Read the `condor_status` man page or HTCondor Manual section (same text) to learn about more options
-   Read about [ClassAd attributes](https://htcondor.readthedocs.io/en/latest/classad-attributes/index.html) in the appendix of the HTCondor Manual
-   Read about [ClassAd expressions](https://htcondor.readthedocs.io/en/latest/misc-concepts/classad-mechanism.html#old-classads-in-the-htcondor-system) in section 4.1.4 of the HTCondor Manual


