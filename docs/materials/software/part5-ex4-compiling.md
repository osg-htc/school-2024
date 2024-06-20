---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Software Exercise 5.4: Compile Statically Linked Code
==========================================================

**Objective**: Compile code using static linking, explain why this can be useful. 

**Why learn this?**: When code is compiled, it is usually linked to other pieces 
of code on the computer. This can cause it to not work when moved to other computers. 
Static linking means that all the needed references are included in the compiled code, 
meaning that it can run almost anywhere. 

Our Software Example
--------------------

For this compiling example, we will use a script written in C. C code depends on libraries and therefore will benefit from being statically linked.

Our C code prints 7 rows of Pascal's triangle.

1.  Log into the Access Point. Create a directory for this exercise and `cd` into it.
1.  Copy and paste the following code into a file named `pascal.c`.

		:::c++
		#include "stdio.h"

		long factorial(int);

		int main()
		{
		int i, n, c;
		n=7;
		for (i = 0; i < n; i++){
		  for (c = 0; c <= (n - i - 2); c++)
		  printf(" ");
			  for (c = 0 ; c <= i; c++)
				 printf("%ld ",factorial(i)/(factorial(c)*factorial(i-c)));
			  printf("\n");
		   }
		   return 0;
		}

		long factorial(int n)
		{
		   int c;
		   long result = 1;
		   for (c = 1; c <= n; c++)
				 result = result*c;
		   return result;
		}

Compiling
---------

In order to use this code in a job, we will first need to statically compile the code. 

1. Most linux servers (including our Access Point) have the `gcc` (GNU compiler collection) installed, so we already have a compiler on the Access Point. Furthermore, this is a simple piece of C code, so the compilation will not be computationally intensive. Thus, we should be able to compile directly on the Access Point

1. Compile the code, using the command: 

        :::console
        username@login $ gcc -static pascal.c -o pascal

	Note that we have added the `-static` option to make sure that the compiled binary includes the necessary libraries. This will allow the code to run on any Linux machine, no matter where those libraries are located. 

1. Verify that the compiled binary was statically linked:

        :::console
        username@login $ file pascal

The Linux `file` command provides information about the *type* or *kind* of file that is given as an argument. In this case, you should get output like this:

```hl_lines="2 3"
username@host $ file pascal
pascal: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), statically linked,
for GNU/Linux 2.6.18, not stripped
```

The output clearly states that this executable (software) is statically linked. The same command run on a non-statically linked executable file would include the text `dynamically linked (uses shared libs)` instead. So with this simple verification step, which could even be run on files that you did not compile yourself, you have some further reassurance that it is safe to use on other Linux machines. (Bonus exercise: Try the `file` command on lots of other files)

Submit the Job
--------------

Now that our code is compiled, we can use it to submit a job.

1.  Think about what submit file lines we need to use to run this job:
    -   Are there input files?
    -   Are there command line arguments?
    -   Where is its output written?

1.  Based on what you thought about in 1., find a submit file from earlier that you can modify to run our compiled `pascal` code.

1. Copy it to the directory with the `pascal` binary and make those changes. 

1. Submit the job using `condor_submit`. 

1. Once the job has run and left the queue, you should be able to see the results (seven rows of Pascal's triangle) in the `.out` file created by the job.

