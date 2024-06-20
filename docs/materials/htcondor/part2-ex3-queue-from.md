---
status: testing
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

HTC Exercise 2.3: Submit with “queue from”
=============================================

Exercise Goals
---------------

In this exercise and the next one, you will **explore more ways to use a single
submit file to submit many jobs**. The goal of this exercise is to submit many
jobs from a single submit file by using the `queue ... from` syntax to read
variable values from a file.


Background
----------

In all cases of submitting many jobs from a single submit file, the key questions are:

-   What makes each job unique? In other words, there is one job per \_\_\_\_\_?
-   So, how should you tell HTCondor to distinguish each job?

For `queue *N*`, jobs are distinguished simply by the built-in "process" variable. But with the remaining `queue` forms, you help HTCondor distinguish jobs by other, more meaningful *custom* variables.

Counting Words in Files
-----------------------

Imagine you have a collection of books, and you want to analyze how word usage varies from book to book or author to author. As mentioned in the lecture, HTCondor provides many ways to submit jobs for this task. You could create a separate submit file for each book, and submit all of the files manually, but you'd have a lot of file lines to modify each time (in particular, all five of the last lines before `queue` below):

``` file
executable              = freq.py
request_memory          = 1GB
request_disk            = 20MB
should_transfer_files   = YES
when_to_transfer_output = ON_EXIT

transfer_input_files = AAiW.txt
arguments            = AAiW.txt
output               = AAiW.out
error                = AAiW.err
log                  = AAiW.log
queue
```

This would be overly verbose and tedious. Let's do better.

Queue Jobs From a List of Values
--------------------------------

Suppose we want to modify our word-frequency analysis from a previous exercise so that it outputs only the most common *N* words of a document. However, we want to experiment with different values of *N*. 

For this analysis, we will have a new version of the word-frequency counting
script.  First, we need a new version of the word counting program so that it
accepts an extra number as a command line argument and outputs only that many
of the most common words. Here is the new code (it's still not important that
you understand this code):

``` python
#!/usr/bin/env python3

import os
import sys
import operator

if len(sys.argv) != 3:
    print(f'Usage: {os.path.basename(sys.argv[0])} DATA NUM_WORDS')
    sys.exit(1)
input_filename = sys.argv[1]
num_words = int(sys.argv[2])

words = {}

with open(input_filename, 'r') as my_file:
    for line in my_file:
        line_words = line.split()
        for word in line_words:
            if word in words:
                words[word] += 1
            else:
                words[word] = 1

sorted_words = sorted(words.items(), key=operator.itemgetter(1))
for word in sorted_words[-num_words:]:
    print(f'{word[0]} {word[1]:8d}')
```

To submit this program with a collection of two variable values for each run, one for the number of top words and one for the filename:

1.  Save the script as `wordcount-top-n.py`.
1.  Download and unpack some books from Project Gutenberg:

        :::console
        user@ap1 $ wget http://proxy.chtc.wisc.edu/SQUID/osgschool20/books.zip
        user@ap1 $ unzip books.zip

1.  Create a new submit file (or base it off a previous one!) named `wordcount-top.sub`, including memory and disk requests of 20 MB.
1.  All of the jobs will use the same `executable` and `log` statements.
1.  Update other statements to work with two variables, `book` and `n`:

        :::file
        output = $(book)_top_$(n).out 
        error = $(book)_top_$(n).err 
        transfer_input_files = $(book) 
        arguments = "$(book) $(n)" 
        queue book, n from books_n.txt

    Note especially the changes to the `queue` statement; it now tells HTCondor to read a separate text file of ***pairs*** of values, which will be assigned to `book` and `n` respectively.

1.  Create the separate text file of job variable values and save it as `books_n.txt`:

        :::file
        AAiW.txt, 10 
        AAiW.txt, 25 
        AAiW.txt, 50 
        PandP.txt, 10 
        PandP.txt, 25 
        PandP.txt, 50
        TAoSH.txt, 10
        TAoSH.txt, 25
        TAoSH.txt, 50

    Note that we used 3 different values for *n* for each book.

1.  Submit the file
1.  Do a quick sanity check: How many jobs were submitted? How many log, output, and error files were created?

Extra Challenge 1
-----------------

You may have noticed that the output of these jobs has a messy naming convention. Because our macros resolve to the filenames, including their extension (e.g., `AAiW.txt`), the output filenames contain with multiple extensions (e.g., `AAiW.txt.err`). Although the extra extension is acceptable, it makes the filenames harder to read and possibly organize. *Change your submit file and variable file for this exercise so that the output filenames do not include the `.txt` extension.*

