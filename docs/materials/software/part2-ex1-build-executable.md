---
status: in progress
---

<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Software Exercise 2.1 Build an HTC-Friendly Executable
============================================================

**Objective**: Modify an existing script to include arguments and headers. 

**Why learn this?**: A little bit of preparation can make it easier to reuse the 
same script over and over to run many jobs. 

Setup
-------

1. Download and unzip a set of Protein Data Bank (PDB) files: 

		:::console
		$ wget http://proxy.chtc.wisc.edu/SQUID/osg-school-2024/alkanes.tar.gz
		$ tar -xzf alkanes.tar.gz

1. For these exercises, we are going to run a command that counts the number of 
atoms in the PDB file . Run it now as an example: 

		:::console
		$ grep ATOM cubane.pdb | wc -l > atoms_cubane.pdb

Add a Header
----------

1. To create a basic script, you can put the command above into a file 
called `get_atoms.sh`. To make it clear what language we expect to use to 
run the script, we will add the following header on the first line: ``#!/bin/bash`

		:::file
		#!/bin/bash
		
		grep ATOM cubane.pdb | wc -l > atoms_cubane.pdb


	The "header" of `#!/bin/bash` will tell the computer that this is a bash shell script 
	and can be run in the same way that  you would run individual commands on the command line. 
	We use `/bin/bash` instead of just `bash` because that is the full path to the `bash` 
	software file.

    !!! note "Other languages"
		We can use the same principle for any scripting language. For example, the header for a Python script 
		could be either `#!/usr/bin/python3` or `#!/usr/bin/env python3`. Similar logic works 
		for perl, R, julia and other scripting languages. 

1. Can you now run the script? 

		:::console
		$ ./get_atoms.sh

1. This gives "permission denied." Let's add executable permissions to the script 
and try again: 

		:::console
		$ chmod +x get_atoms.sh
		$ ./get_atoms.sh

Incorporate Arguments
----------

Can you imagine trying to run this script on all of our pdb files? It would be tedious
to edit it for each one, even for only six inputs. Instead, we should add arguments 
to the script to make it easy to reuse the script. 
Any information in a script or executable that is going to change or vary across 
jobs or analyses should likely be turned into an argument that is specified on the command line. 

1. In our example above, which pieces of the script are likely to change or vary? 

1. The name of the input file (`cubane.pdb`) and output file (`atoms_cubane.pdb`) should 
be turned into arguments. Can you envision what our script should look like if we ran it 
with input arguments? 

1. Let's say we want to be able to run the following command: 

		:::console
		$ ./get_atoms.sh cubane.pdb atoms_cubane.pdb

	In order to get arguments from the command line into the script, you have 
	to use special variables in the script. In bash, these are `$1` (for the first 
	argument), `$2` (for the second argument) and so on. Try to figure out where 
	these should go in our `get_atoms.sh` script. 

    !!! note "Other Languages"
		Each language is going to have its own syntax for reading command line 
		arguments into the script. In Python, `sys.argv` is a basic method, and more 
		advanced libraries like `argparse` can be used. In R, the `commandArgs()` function 
		can do this. Google "command line arguments in ______" to find the right 
		syntax for your language of choice!

1. A first pass at adding arguments might look like this: 

		:::file
		#!/bin/bash
		
		grep ATOM $1 | wc -l > $2
		
	Try running it as described above. Does it work? 

1. While we now have arguments, we have lost some of the readability of our script. The 
numbers `$1` and `$2` are not very meaningful in themselves! Let's rewrite the script to 
assign the arguments to meaningful variable names: 

 		:::file
		#!/bin/bash
		
		PDB_INPUT=$1
		PDB_ATOM_OUTPUT=$2
		
		grep ATOM ${PDB_INPUT} | wc -l > ${PDB_ATOM_OUTPUT}

    !!! note "Why curly brackets?"
        You'll notice above that we started using curly brackets around our variables. 
        While you technically don't need them (`$PDB_INPUT` would also be fine), using 
        them makes the name of the variable (compared to other text) completely clear. 
        This is especially useful when combining variables with underscores. 

1. There is one final place where we could optimize this script. If we want our output 
files to always have the same naming convention, based on the input file name, then 
we shouldn't have a separate argument for that -- it's asking for typos. Instead, we 
should use variables inside the script to construct the output file name, based on the 
input file. That will look like this: 

 		:::file
		#!/bin/bash
		
		PDB_INPUT=$1
		PDB_ATOM_OUTPUT=atoms_${PDB_INPUT}
		
		grep ATOM ${PDB_INPUT} | wc -l > ${PDB_ATOM_OUTPUT}

	You may want to construct other variables, like paths and filenames in this way. But 
	it depends on how you want to use the script! If we want the flexibility of specifying 
	a custom output file name, then we should undo this last change so it can be 
	treated as a separate argument. 

Your Work
----------

1. Are you using a scripting language where you could add a header to your main script? 
If so, what should it be? 

1. What items in your main code or commands are changing? Do you need to add arguments 
to your code? 
