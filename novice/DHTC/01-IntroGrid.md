---
layout: lesson
root: ../..
title: Introduction to Open Science Grid 
pdf: novice/DHTC/OSG_SWC_March2015.pdf
---
<div class="objectives" markdown="1">

#### Objectives
*   Get to know the Open Science Grid
*   What resources are open to academic researchers?
*   Computation that is a good match for OSG Connect
*   Computation that is NOT a good match for OSG Connect

</div>

## Introduction to Open Science Grid (OSG)  

The Open Science Grid (OSG) is a consortium of research communities who promote science 
via sharing of computing resources. The Open Science Grid (OSG):
<ul> 
<li> enables distributed computing on more than 120 institutions, </li>
<li> supports efficient data processing and  </li>
<li> provides large scale scientific computing of 2 million core CPU hours per day.   </li>
</ul> 


The resources accessible through the OSG are contributed by the community, organized by 
the OSG, and governed by the OSG Consortium. The cores that are free in the OSG shared pool 
are made available to users. Opportunistic resources available at any given time will vary 
depending on usage by resource owners. 



## Computation that is a good match for OSG Connect 

High throughput workflows with simple system and data dependencies are a good 
fit for OSG Connect. Typically these workflows can be decomposed into multiple
tasks that can be carried out independently.  Ideally, these tasks will download 
data for input, run some computation on it and then return results (which may be 
used by future tasks).

Jobs submitted into the OSG Connect will be executed on machines at several 
remote physical clusters. These machines may differ in terms of computing 
environment from the submit node. Therefore it is important that the jobs are 
as self-contained as possible by generic binaries and data that can be either 
carried with the job, or staged on demand. Please consider the following 
guidelines:
<ul>
<li>   Software should preferably be single threaded, using less than 2 GB memory and 
    each invocation should run for 1-12 hours (optimally under 4 hours). There is 
    some support for jobs with longer run time, more memory or multi-threaded codes. 
    Please contact the support listed below for more information about these 
    capabilities.</li>
<li>   Only core utilities can be expected on the remote end. There are no standard 
    versions of software such as 'gcc', 'python', 'BLAS' or others on the grid. 
    Consider using Distributed Environment Modules to manage software dependencies, 
    or read our Developing High-Throughput Applications guide.</li>
<li>   Input and output data for each job should be < 10 GB to allow them to be 
    transferred in by the jobs, processed and returned to the submit node. Note 
    that the OSG Connect Virtual Cluster does not have a global shared file 
    system, so jobs with such dependencies will not work.</li>
<li>   No shared filesystem. Jobs must transfer all executables, input data, and 
    output data. HTCondor can transfer the files for you, but you will have to 
    identify and list the files in your HTCondor job description file. </li>
</ul>

## Computation that is NOT a good match for OSG Connect 

The following are examples of computations that are NOT good matches for 
OSG Connect:
<ul>
<li>   Tightly coupled computations, for example MPI based communication, will 
    not work well on OSG Connect due to the distributed nature of the infrastructure.</li>
<li>   Computations requiring a shared file system will not work, as there is 
    no shared filesystem between the different clusters on OSG Connect.</li>
<li>   Computations requiring complex software deployments or proprietary software 
    are not a good fit.  There is limited support for distributing software to 
    the compute clusters, but for complex software, or licensed software, 
    deployment can be a major task.</li>
</ul>

##How to get help using OSG Connect

Feel free to contact the OSG Connect support team at: [connect-support@opensciencegrid.org](mailto:connect-support@opensciencegrid.org).


<div class="keypoints" markdown="1">
#### Key Points
*   OSG resources are distributed across 120 institutions and  supports scientific computing of 2 million core CPU hours per day.   
*   Many scientific applications are installed on OSG and available for the users. 
</div>



