---
status: testing
---

# OSG Exercise 1.4: Software Differences in OSPool

The goal of this exercise is to see some differences in the availability of software in the OSPool.
At your local cluster, you may be used to having certain versions of software.
But in the OSPool,
it is likely that the software you need will not be available at all.

## Comparing operating systems

To really see differences between Execution Points in the PATh Facility versus the OSPool,
you will want to compare the &ldquo;machine&rdquo; ClassAds between the two pools.
Rather than inspecting the very long ClassAd for each Execution Point,
you will look at a specific attribute called `OpSysAndVer`,
which tells us the operating system version of the Execution Point.
An easy way to show this attribute for all Execution Points is by using `condor_status`
in conjunction with the `-autoformat` (or `-af`, for short) option.
The `-autoformat` option is like the `-format` option you learned about earlier,
and outputs the attributes you choose for each slot;
but as you may have guessed, it does some automatic formatting for you.

So, let’s examine the operating system and (major) version of slots on the PATh Facility and the OSPool.

1.  Log in or switch to `ap1.facility.path-cc.io` and run the following command:

        :::console
        $ condor_status -autoformat OpSysAndVer

1.  Log in or switch to `ap40.uw.osg-htc.org` (parallel windows are handy!)
    and run the same command

You will see many values for the operating system and major version.
Some are abbreviated&nbsp;— for example,
`RedHat` stands for “Red Hat Enterprise Linux” and
`SL` stands for “Scientific Linux” (a Red Hat variant).

The only problem is that with hundreds or thousands of slots,
it's difficult to get a feel for the composition of each pool from this output.
You can use the `sort` and `uniq` commands, in sequence, on the `condor_status` output
to get counts of each unique operating system and version string.
Your command line should look something like this:

``` console
$ condor_status -autoformat OpSysAndVer | sort | uniq -c
```

How would you describe the difference between the PATh Facility and OSPool?

## Submitting probe jobs

Now you have some idea of the diversity of operating systems on the OSPool.
This is a step in the right direction to knowing what software is available in general.
But what you really want to know is whether your specific software tool (and version) is available.

### Software probe code

The following shell script probes for software and returns the version if it is installed:

```bash
#!/bin/sh

get_version(){
    program=$1
    $program --version > /dev/null 2>&1
    double_dash_rc=$?
    $program -version > /dev/null 2>&1
    single_dash_rc=$?
    which $program > /dev/null 2>&1
    which_rc=$?
    if [ $double_dash_rc -eq 0 ]; then
        $program --version 2>&1
    elif [ $single_dash_rc -eq 0 ]; then
        $program -version 2>&1
    elif [ $which_rc -eq 0 ]; then
        echo "$program installed but could not find version information"
    else
        echo "$program not installed"
    fi
}

get_version 'R'
get_version 'cmake'
get_version 'python'
get_version 'python3'
```

If there's a specific command line program that your research requires, feel free to add it to the script!
For example, if you wanted to test for the existence and version of `nslookup`, you would add the following to the end
of the script:

``` file
get_version 'nslookup'
```

### Probing several servers

For this part of the exercise, try creating a submit file without referring to previous exercises!

1.  Log in or switch to `ap40.uw.osg-htc.org`
1.  Create and change into a new folder for this exercise, e.g. `osg-ex14`
1.  Save the above script as a file named `sw_probe.sh`
1.  Make sure the script can be run: `chmod a+x sw_probe.sh`
1.  Try running the script in place to make sure it works: `./sw_probe.sh`
1.  Create a submit file that runs `sw_probe.sh` 100 times
    and uses macros to write different `output`, `error`, and `log` files
1.  Submit your job and wait for the results

Will you be able to do your research on the OSG with what's available?
Do not worry if it does not seem like you can:
Later today, you will learn how to make your jobs portable enough so that they can run anywhere!
