---
status: testing
---

# OSG Exercise 1.2: Running Jobs in OSPool

The goal of this exercise is to map the physical locations of some Execution Points in the OSPool.
We will provide the executable and associated data,
so your job will be to write a submit file that queues multiple jobs.
Once complete, you will manually collate the results.

## Where in the world are my jobs?

To find the physical location of the computers your jobs our running on, you will use a method called *geolocation*.
Geolocation uses a registry to match a computer’s network address to an approximate latitude and longitude.

### Geolocating several Execution Points

Now, let’s try to remember some basic HTCondor ideas from the HTC exercises:

1.  Log in to `ap40.uw.osg-htc.org` if you have not yet.
1.  Create and change into a new folder for this exercise, for example `osg-ex12`
1.  Download the geolocation code:

        :::console
        $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/location-wrapper.sh \
               http://proxy.chtc.wisc.edu/SQUID/osgschool21/wn-geoip.tar.gz

    You will be using `location-wrapper.sh` as your executable and `wn-geoip.tar.gz` as an input file.
 
1.  Create a submit file that queues **fifty** jobs that run `location-wrapper.sh`,
    transfers `wn-geoip.tar.gz` as an input file,
    and uses the `$(Process)` macro to write different `output` and `error` files.
    Also, add the following requirement to the submit file (it’s not important to know what it does):

        container_image = http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/python27.sif

    Try to do this step without looking at materials from the earlier exercises.
    But if you are stuck, see [HTC Exercise 2.2](../htcondor/part2-ex2-queue-n.md).

1.  Submit your jobs and wait for the results

### Collating your results

Now that you have your results, it’s time to summarize them.
Rather than inspecting each output file individually,
you can use the `cat` command to print the results from all of your output files at once.
If all of your output files have the format `location-#.out` (e.g., `location-10.out`),
your command will look something like this:

``` console
$ cat location-*.out
```

The `*` is a wildcard so the above cat command runs on all files that start with `location-` and end in `.out`.
Additionally, you can use `cat` in combination with the `sort` and `uniq` commands using "pipes" (`|`)
to print only the unique results:

``` console
$ cat location-*.out | sort | uniq
```

## Mapping your results

To visualize the locations of the Execution Points that your jobs ran on,
you will be using <http://www.mapcustomizer.com/>.
Copy and paste the collated results into the text box that pops up
when clicking on the 'Bulk Entry' button on the right-hand side.
Where did your jobs run?

## Next exercise

Once completed, move onto the next exercise: [Hardware Differences in the OSG](part1-ex3-hardware-diffs.md)

## Extra Challenge: Cleaning up your submit directory

If you run `ls` in the directory from which you submitted your job, you may see that you now have thousands of files!
Proper data management starts to become a requirement as you start to develop true HTC workflows;
it may be helpful to separate your submit files, code, and input data from your output data.

1.  Try editing your submit file so that all your output and error files are saved to separate directories within your
    submit directory.

    !!! note "Tip"
        Experiment with fewer job submissions until you’re confident you have it right,
        then go back to submitting 500 jobs.
        Remember: Test small and scale up!

1.  Submit your file and track the status of your jobs.

Did your jobs complete successfully with output and error files saved in separate directories?
If not, can you find any useful information in the job logs or hold messages?
If you get stuck, review the [slides from Tuesday](../index.md).
