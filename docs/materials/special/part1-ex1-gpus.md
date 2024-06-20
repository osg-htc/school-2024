---
status: testing
---

Exercise 1.1: GPUs
==================

Exploring Availability
----------------------

For this exercise, we will use the `ap40.uw.osg-htc.org` access point. Log in:

``` hl_lines="1"
$ ssh <USERNAME>@ap40.uw.osg-htc.org
```

Let's first explore what GPUs are available in the OSPool. Remember
that the pool is dynamic - resources are beeing added and removed all
the time - but we can at least find out what the current set of GPUs
are there. Run:

    :::console
    user@ap40 $ condor_status -const 'GPUs > 0'

Once you have that list, pick one of the resources and look at the
classad using the `-l` flag. For example:

    :::console
    user@ap40 $ condor_status -l [MACHINE]

Using the `-autoformat` flag, explore the different attributes
of the GPUs. Some interesting attributes might be `GPUs_DeviceName`,
`GPUs_Capability`, `GLIDEIN_Site` and `GLIDEIN_ResourceName`.

Compare the `Mips` number of a GPU slot with a regular slot. Does
the `Mips` number indicate that GPUs can be much faster than CPUs?
Why/why not?


A sample GPU job
----------------

Create a file named `mytf.py` and chmod it to be executable. The 
content is a sample TensorFlow code:

```
#!/usr/bin/python3

# http://learningtensorflow.com/lesson10/

import sys
import numpy as np
import tensorflow as tf
from datetime import datetime

tf.debugging.set_log_device_placement(True)

# Create some tensors
a = tf.constant([[1.0, 2.0, 3.0], [4.0, 5.0, 6.0]])
b = tf.constant([[1.0, 2.0], [3.0, 4.0], [5.0, 6.0]])
c = tf.matmul(a, b)

print(c)
```

Then, create a submit file to run the code on a GPU, using a
TensorFlow container image. The new bits of the submit file
is provided below, but you will have to fill in the rest
from what you have learnt earlier in the User School.

```
universe = container
container_image = /cvmfs/singularity.opensciencegrid.org/opensciencegrid/tensorflow-gpu:2.2-cuda-10.1

executable = mytf.py

request_gpus = 1
```

Note that TensorFlow also require the AVX2 CPU extensions. Remember
that AVX2 is available in the `x86_64-v3` and `x86_64-v4` 
micro architectures. Add a `requirements` line stating that
`Microarch` has to be one of those two (the operand for
or in the classad experssions is `||`)

Submit the job and watch the queue. Did the job start
running as quickly as when we ran CPU jobs? Why/why not?

Examine the out/err files. Does it indicate somewhere that
the job was mapped to a GPU? (Hint: search for
`Created TensorFlow device`)

Keep a copy of the out/err. Modify the submit file to _not_ run 
on a GPU, and the try the job again. Did the job work? Does
the err from the CPU job look anything like the GPU err?













