---
status: testing
---

<style type="text/css">
  pre em { font-style: normal; background-color: yellow; }
  pre strong { font-style: normal; font-weight: bold; color: \#008; }
</style>

# Bonus Workflows Exercise 1.5: YOUR Jobs and More on Workflows

The objective of this exercise is to learn the very basics of running a set of jobs, where our set is just one job.

Challenge 1
-----------

Do you have any extra computation that needs to be done? Real work, from your life outside this summer school? If so, try it out on our HTCondor pool. Can't think of something? How about one of the existing distributed computing programs like [distributed.net](http://www.distributed.net), [SETI@home](http://setiathome.ssl.berkeley.edu/), [Einstien@Home](http://www.einsteinathome.org/) or others that you know. We prefer that you do your own work rather than one of these projects, but they are options.

Challenge 2
-----------

Try to generate other Mandelbrot images. Some possible locations to look at with goatbroat:

``` console
goatbrot -i 1000 -o ex1.ppm -c 0.0016437219722,-0.8224676332988 -w 2e-11 -s 1000,1000
goatbrot -i 1000 -o ex2.ppm -c 0.3958608398437499,-0.13431445312500012 -w 0.0002197265625 -s 1000,1000
goatbrot -i 1000 -o ex3.ppm -c 0.3965859374999999,-0.13378125000000013 -w 0.003515625  -s 1000,1000
```

You can convert ppm files with `convert`, like so:

``` console
convert ex1.ppm ex1.jpg
```

Now make a movie! Make a series of images where you zoom into a point in the Mandelbrot set gradually. (Those points above may work well.) Assemble these images with the "convert" tool which will let you convert a set of JPEG files into an MPEG movie.

Challenge 3
-----------

Try out Pegasus. Pegasus is a workflow manager that uses DAGMan and can work in a grid environment and/or run across different types of clusters (with other queueing software). It will create the DAGs from abstract DAG descriptions and ensure they are appropriate for the location of the data and computation.

Links to more information:

-   [Pegasus Website](https://pegasus.isi.edu)
-   [Pegasus Documentation](https://pegasus.isi.edu/documentation)
-   [Pegasus on OSG](https://portal.osg-htc.org/documentation/htc_workloads/automated_workflows/tutorial-pegasus/)

If you have any questions or problems, please feel free to contact the Pegasus team by emailing <pegasus-support@isi.edu>


