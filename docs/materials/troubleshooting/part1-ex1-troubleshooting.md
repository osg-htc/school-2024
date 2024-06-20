---
status: in progress
---

# OSG Exercise 2.1: Troubleshooting Jobs

The goal of this exercise is to practice troubleshooting some common problems
that you may encounter when submitting jobs using HTCondor.

This exercise should work on either of the access points- OSPool or Path Facility

**Note:** This exercise is a little harder than some others.
To complete it, you will have to find and fix several issues.
Be patient, keep trying, but if you really get stuck,
you can ask for help or look at the very bottom of this page for a link to answers.
But try not to look at the answers!

## Acquiring the Materials

We have prepared some Python code, data, and submit files for this exercise:

1.  Log into an Access Point
1.  Download a tarball of the materials:

        :::console
        user@server $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2023/troubleshooting.tar.gz

3.  Extract the tarball using the commands that you learned earlier
4.  Change into the newly extracted directory and explore its contents&nbsp;—
    resist the temptation to fix things right away!

## Solving a Project Euler Problem

The contents of the tarball contain a series of submit files, Python scripts, and an input file 
that are designed to solve [Project Euler problem 98](https://projecteuler.net/problem=98):

> By replacing each of the letters in the word CARE with 1, 2, 9, and 6 respectively, we form a square number: 1296 =
> 36^2. What is remarkable is that, by using the same digital substitutions, the anagram, RACE, also forms a square
> number: 9216 = 96^2. We shall call CARE (and RACE) a square anagram word pair and specify further that leading zeroes
> are not permitted, neither may a different letter have the same digital value as another letter.
>
> Using p098_words.txt, a 16K text file containing nearly two-thousand common English words, find all the square
> anagram word pairs (a palindromic word is NOT considered to be an anagram of itself).
>
> What is the largest square number formed by any member of such a pair?
>
> **NOTE:** All anagrams formed must be contained in the given text file.

Unfortunately, there are many issues with the submit files that you will have to work through
before you can obtain the solution to the problem!
The code in the Python scripts themselves is, in theory, free of bugs.

## Finding anagrams

The first step in our workflow takes an input file with a list of words (`p098_words.txt`)
and extracts all of the anagrams using the `find_anagrams.py` script.
Naturally, we want to run this as an HTCondor job, so:

1. Submit the accompanying `find-anagrams.sub` file from the tarball.
1. Resolve any issues that you encounter until the job returns pairs of anagrams as its output.

Once you have satisfactory output, move onto the next section.

!!! note "Please be polite"
    Access points are shared resources, so you should clean up after yourself.
    If you discover any jobs in the Hold state, and after you are done troubleshooting them,
    remove them with the following command:

        :::console
        user@server $ condor_rm -const 'JobStatus =?= 5' <JOB FILTER>

    | Where replacing `<JOB FILTER>` with...                 | Will remove...                              |
    |--------------------------------------------------------|---------------------------------------------|
    | Your username (e.g. `blin`)                            | All of your held jobs                       |
    | A cluster ID (e.g. `74078`)                            | All held jobs matching the given cluster ID |
    | A job ID (e.g. `97932.30`)                             | That specific held job                      |

## Finding the largest square

The next step in the workflow uses the `max_square.py` script to find the largest square number,
if any, for a given anagram word pair.
Let's submit jobs that run `max_square.py` for all of the anagram word pairs (i.e., one job per word pair),
that you found in the previous section:

1. Submit the accompanying `squares.sub` file from the tarball
1. Resolve any issues that you encounter until you receive output for each job.
   Note that some jobs may have empty output since not all anagram word pairs are *square* anagram word pairs.

Next, you can find the largest square among your output by directly using the command line.
For example, if all of your job output has been placed in the `squares` directory
and are named `square-1.out`, `square-2.out`, etc.,
then you could run the following command to find the largest square:

``` console
user@server $ cat squares/square-*.out | sort -n | tail -n 1
```

You can check if you have the right answer with any of the OSG staff
or by submitting the answer to Project Euler (requires an account).

## Answer Key

There is also a working solution on our web server that can be retrieved with

``` console
user@server $ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2023/troubleshooting-key.tar.gz
```

It contains comments labeled `SOLUTION` that you can consult in case you get stuck.
Like any answer key, it is mainly useful as a verification tool,
so try to only use it as a last resort or for detailed explanations to improve your understanding.
