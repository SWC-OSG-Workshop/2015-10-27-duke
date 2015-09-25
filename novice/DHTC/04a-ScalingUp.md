---
layout: lesson
root: ../..
title: Scaling up compute resources
---
<div class="objectives" markdown="1">

#### Objectives
*   Learn why to scale up the computational resources.
*   Learn how to scaling up the computational resources
</div>

## Overview

Scaling up the computational resources is a big advantage for doing
certain large scale calculations on OSG. Consider the extensive
sampling for a multi-dimensional Monte Carlo integration or molecular
dynamics simulation with several initial conditions. These type of
calculations require submitting lot of jobs.

In the previous example, we submitted the job to a single worker
machine. About a million CPU hours per day are available to OSG users
on an opportunistic basis.  Learning how to scale up and control large
numbers of jobs to realizing the full potential of distributed high
throughput computing on the OSG.

In this section, we will see how to scale up the calculations with
simple example. Once we understand the basic HTCondor script, it is easy
to scale up.

~~~
$ tutorial ScalingUp-R
$ cd ScalingUp-R
~~~

As we discussed in the previous section on HTCondor scripts, we need to
prepare the job execution and the job submission scripts. Here again
is our job execution script:

## Submitting jobs concurrently

If we want to submit several jobs, we need to track log, out and error  files for each
job. An easy way to do this is to add the $(Cluster) and $(Process)
macros to the HTCondor submit file. 

~~~
universe = vanilla

Executable = R-wrapper.sh
arguments = mcpi.R $(Process)
transfer_input_files = mcpi.R     # mcpi.R is the R program we want to run

output = job.out.$(Cluster).$(Process)  
error = job.error.$(Cluster).$(Process)
log = job.log.$(Cluster).$(Process)

requirements = (HAS_CVMFS_oasis_opensciencegrid_org =?= TRUE)   # Checks if OASIS available
queue 100

~~~
Note the `Queue 100`.  This tells Condor to enqueue 100 copies of this job
as one cluster.  

Let us take a look at the execution script, R-wrapper.sh

~~~
#!/bin/bash
  source /cvmfs/oasis.opensciencegrid.org/osg/modules/lmod/5.6.2/init/bash
  module load R
  Rscript $1 > mcpi.$2.out
~~~

The wrapper loads the R module and then executes the script with Rscript utility. From the submit 
file described above, the first argument is the name of the R program - mcpi.R and the second argument is the process number. The process number is a sequence of integers and used here to name the output
files. 


You'll see something like the following upon submission:

~~~
$ condor_submit R.submit
Submitting job(s).........................
100 job(s) submitted to cluster 837.
~~~

Apply your `condor_q` and `connect watch` knowledge to see this job
progress. Execute the following bash script to compute the average from all the jobs.

Once the jobs are completed, you might want to invoke the script 

~~~
$mcpi_ave.bash
~~~

to compute the average value of pi from all the available outputs.  

## Interlude: utilization plots

Before we continue, let's look at a URL: [your OSG Connect home
page](http://osgconnect.net/home).  If you have not signed in, you'll be
redirected back to the main site.  Sign In as you did the first time you
signed up, and then click again on the
[your OSG Connect home link](http://osgconnect.net/home).

You see a number of graphs and plots here showing things happening
in OSG Connect.  We'll go over these briefly, then return later.

## Connect Histogram 

We're waiting on 1,000 jobs.  Let's use `connect watch` to
watch for job completions.  As soon as you see some jobs enter R state
(running), press control-C, and let's introduce a new command:

~~~
$ connect histogram
Val                               |Ct (Pct)    Histogram
unl.edu                           |46 (68.66%) ████████████████████████████████▏
bu.edu                            |13 (19.40%) █████████▏
uconn.edu                         |2 (2.99%)   █▌
CRUSH-OSG-10-5-220-34             |1 (1.49%)   ▊
ufhpc                             |1 (1.49%)   ▊
LAW-D-SBA01-S2-its-c6-osg-20141013|1 (1.49%)   ▊
CRUSH-OSG-10-5-10-33              |1 (1.49%)   ▊
iu.edu                            |1 (1.49%)   ▊
vt.edu                            |1 (1.49%)   ▊
~~~

This command gives us a simple histogram of where on the grid our jobs
are running.  The column on the left is (for the most) a list of _sites_
that OSG jobs run on.  At times we don't correctly group job locations
together. For example, the two rows for CRUSH-* above are really the
same site, but histogram doesn't know about that site (yet) so it
displays as two.  But most of the big sites are mapped correctly.  You
see that in my case, 67 of my 100 jobs have begun running, and among
them 69% (46 of 67) are running at University of Nebraska at Lincoln.

`connect histogram` gives metrics on current jobs.  As jobs complete,
they no longer appear.  How to see where jobs have already run? `connect
histogram --last` shows the run sites of your *last* job cluster.

~~~
$ connect histogram --last
Val                               |Ct (Pct)    Histogram
uc3                               |49 (49.00%) ████████████████████████████████▏
bu.edu                            |21 (21.00%) █████████████▊
uconn.edu                         |11 (11.00%) ███████▎
unl.edu                           |9 (9.00%)   ██████
mwt2.org                          |3 (3.00%)   ██
c5a-s22.ufhpc                     |3 (3.00%)   ██
LCS-215-021-S2-its-c6-osg-20141013|3 (3.00%)   ██
cinvestav.mx                      |1 (1.00%)   ▊
~~~

<div class="keypoints" markdown="1">

#### Key Points
*    Scaling up the computational resources on OSG is crucial to taking full advantage of grid computing.
*    Changing the value of *Queue* allows the user to scale up the resources.
*    *Arguments* allows you to pass parameters to a job script.
*    $(Cluster) and $(Process) can be used to name log files uniquely.
*    `connect histogram` gives a nice plot of resource assignments.
*    Your OSG Connect home page gives other useful plots.
</div>
