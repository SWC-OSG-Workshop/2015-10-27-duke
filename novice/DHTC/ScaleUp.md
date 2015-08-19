---
layout: lesson
root: ../..
title: Scaling up the resources 
---
<div class="objectives" markdown="1">

#### Objectives
*   Learn why to scale up the computational resources.
*   Learn how to scaling up the computational resources  
</div>

<h2>Overview </h2> 
Scaling up the computational resources is a big advantage for doing certain
large scale calculations on OSG. Consider the  extensive 
sampling for a multi-dimensional Monte Carlo integration or molecular dynamics 
simulation with several initial conditions. These type of calculations definetly 
require submitting lot of jobs.  

In the past examples, we submitted the job to a single worker machine. About a 
million CPU hours per day is 
available to the users on an oppertunistic basis for the OSG users. 
Learning how to scale up the resources is important to utilize 
the full potential of the distributed high throughput computing on 
OSG. 


In this section, we will see how to scale up the calculations with 
simple example. Once we understand the basic HTCondor script, it is easy 
to scale up of the resources. 

##Example##

Run the command 

~~~
$ tutorial scaling-up-resources
$ cd tutorial-scaling-up-resourcew
~~~

As we discussed in the previous section on HTCondor scripts, we need to 
prepare the job execution and the job submission scripts. For simplicity, 
we keep the same job execution script we discussed before in the HTCondor
scripts section. 

~~~
The "short.sh" script is:
$ nano short.sh
file: short.sh
#!/bin/bash
# short.sh: a short discovery job
printf "Start time: "; /bin/date
printf "Job is running on node: "; /bin/hostname
printf "Job running as user: "; /usr/bin/id
printf "Job is running in directory: "; /bin/pwd
echo
echo "Working hard..."
sleep ${1-15}
echo "Science complete!"
~~~


Now we prepare the job submission script. 

~~~
$ nano campus.submit
Universe = vanilla

Executable = short.sh
Arguments = 5 # to sleep 5 seconds

Error = log/campus.err.$(Cluster)-$(Process) #differs from the old script
Output = log/campus.out.$(Cluster)-$(Process) #differs from the old script
Log = log/campus.log.$(Cluster) #differs from the old script

Queue 25 #differs from the old script
~~~

In the above script, the value of Queue is 25, so you will see 25 jobs in the queue when 
you try 

~~~
condor_q username
~~~

<div class="keypoints" markdown="1">

#### Keypoints
*    Scaling up the computational resources on OSG is beneficial to many scientific calculations and simulations.  
*    Changing the value of *Queue* allows the user to scale up the resources.   
</div>


