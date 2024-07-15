---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

HTC Bonus Exercise 1.7: Compile and Run Some C Code
======================================================

The goal of this exercise is to show that compiled code works just fine in HTCondor. It is mainly of interest to people who have their own C code to run (or C++, or really any compiled code, although Java would be handled a bit differently).

Preparing a C Executable
------------------------

When preparing a C program for HTCondor, it is best to compile and link the executable statically, so that it does not depend on external libraries and their particular versions. Why is this important? When your compiled C program is sent to another machine for execution, that machine may not have the same libraries that you have on your submit machine (or wherever you compile the program). If the libraries are not available or are the wrong versions, your program may fail or, perhaps worse, silently produce the wrong results.

Here is a simple C program to try using (thanks, Alain Roy):

``` c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char **argv)
{
    int sleep_time;
    int input;
    int failure;

    if (argc != 3) {
        printf("Usage: simple <sleep-time> <integer>\n");
        failure = 1;
    } else {
        sleep_time = atoi(argv[1]);
        input      = atoi(argv[2]);

        printf("Thinking really hard for %d seconds...\n", sleep_time);
        sleep(sleep_time);
        printf("We calculated: %d\n", input * 2);
        failure = 0;
    }
    return failure;
}
```

Save that code to a file, for example, `simple.c`.

Compile the program with static linking:

``` console
username@ap1 $ gcc -static -o simple simple.c
```

As always, test that you can run your command from the command line first. First, without arguments to make sure it fails correctly:

``` console
username@ap1 $ ./simple
```

and then with valid arguments:

``` console
username@ap1 $ ./simple 5 21
```

Running a Compiled C Program
----------------------------

Running the compiled program is no different than running any other program. Here is a submit file for the C program (call it simple.sub):

``` file
executable = simple
arguments = "60 64"

output = c-program.out
error = c-program.err
log = c-program.log

should_transfer_files = YES
when_to_transfer_output = ON_EXIT

request_cpus = 1
request_memory = 1GB
request_disk = 1MB

queue
```

Then submit the job as usual!

In summary, it is easy to work with statically linked compiled code.
It **is** possible to handle dynamically linked compiled code, but it is trickier.
We will only mention this topic briefly during the lecture on Software.


