---
layout: lesson
root: ../..
title: Troubleshooting
---
<div class="objectives" markdown="1">

#### Objectives
*   Learn how to troubleshoot failed jobs.
*   Learn how to periodically retry failed jobs.
</div>

<h2>Overview </h2> 
In this lesson, we'll learn how to troubleshoot jobs that never start or fail in unexpected ways. 

<h2> Troubleshooting techniques </h2> 

<h3> Diagnostics with condor_q  </h3> 
The `condor_q` command shows the status of the jobs and it can be used to diagnose why jobs are not 
running. Using the `-better-analze` flag with `condor_q` can show you detailed information about why a job isn't starting. Since OSG Connect sends jobs to many places, we also need to specify a pool name with the `-pool` flag. 

~~~
$ condor_q -better-analyze JOB-ID -pool POOL-NAME
~~~

Let's do an example. First we'll need to login as usual, and then load the tutorial *error101*.

~~~
$ ssh username@login.osgconnect.net

$ tutorial error101
$ cd tutorial-error101
$ condor_submit error101_job.submit 
~~~

We'll check the job status the normal way:

~~~
condor_q username
~~~

For some reason, our job is still idle. Why? Try using `condor_q -better-analyze` to find out. Remember that you will also need to specify a pool name. In this case we'll use `osg-flock.grid.iu.edu`:

~~~
$ condor_q -better-analyze JOB-ID -pool osg-flock.grid.iu.edu
 
# Produces a long ouput. 
# The following lines are part of the output regarding the job requirements.  

The Requirements expression for your job reduces to these conditions:

         Slots
Step    Matched  Condition
-----  --------  ---------
[0]           0  Memory >= 51200                 ######## BIG MEMORY, NOT AVAILABLE ###### 
[1]       14727  TARGET.Arch == "X86_64"
[2]       14727  TARGET.OpSys == "LINUX"
[3]       14727  TARGET.Disk >= RequestDisk
[4]       14727  TARGET.HasFileTransfer
~~~

By looking through the match conditions, we see that many nodes match our requests for the Linux operating system and the x86_64 architecture, but none of them match our requirement for 51200 MB of memory. This is an error we introduced 
puposefully in the script by including a line *Requirements = (Memory >= 51200)* in the file 
"error101_job.submit". 

We want to edit the job submit file and change the requirements such that we only request 512 MB of memory. The requirements line should look like this: *Requirements = (Memory >= 512)*

~~~
$ nano error101_job.submit
~~~

Finally, resubmit the job:

~~~
$ condor_submit error101_job.submit
~~~

Alternatively, you can edit the resource requirements of the idle job in queue:

~~~
condor_qedit JOB-ID Requirements 'Requirements = (Memory >= 512)' 
~~~

<br/>
<br/>

> ### On your own
> * Use the `connect status` command to get a list of pools (e.g., `uc3-mgt.mwt2.org`) <br/>
> * Edit `error101_job.submit`, replacing the `Requirements` line with `Requirements = (regexp("^uc**", TARGET.Machine, "IM") == True)` before the `queue` statement. <br/>
> *  Use `condor_q -better-analyze` against each pool. Does it match any slots? If so, where?

<br/>
<br/>

<h3> condor_ssh_to_job </h3> 
This command allows you to `ssh` to the compute node where the job is running. After running `condor_ssh_to_job`, you will be connected to the remote system, and you will be able to use normal shell commands to investigate your job.

~~~
condor_ssh_to_job JOB-ID  
~~~

<h3> Held jobs and condor_release </h3>

Occasionally, a job can fail in various ways and go into "Held" state. Held state means that the job has encountered some error, and cannot run. This doesn't necessarily mean that your job has failed, but, for whatever reason, Condor cannot fulfill your request(s).

In this particular case, a user had this in his or her Condor submit file:

~~~
transfer_output_files = outputfile
~~~

However, when the job executed, it went into Held state:

~~~
$ condor_q -analyze 372993.0
-- Submitter: login01.osgconnect.net : <192.170.227.195:56174> : login01.osgconnect.net
---
372993.000:  Request is held.
Hold reason: Error from glidein_9371@compute-6-28.tier2: STARTER at 10.3.11.39 failed to send file(s) to <192.170.227.195:40485>: error reading from /wntmp/condor/compute-6-28/execute/dir_9368/glide_J6I1HT/execute/dir_16393/outputfile: (errno 2) No such file or directory; SHADOW failed to receive file(s) from <192.84.86.100:50805>
~~~

Let's break down this error message piece by piece:

~~~
Hold reason: Error from glidein_9371@compute-6-28.tier2: STARTER at 10.3.11.39 failed to send file(s) to <192.170.227.195:40485>
~~~

This part is quite cryptic, but it simply means that the worker node where your job executed (glidein_9371@compute-6-28.tier2 or 10.3.11.39) tried to transfer a file to the OSG Connect login node (192.170.227.195) but did not succeed. The next part explains why:

~~~
error reading from /wntmp/condor/compute-6-28/execute/dir_9368/glide_J6I1HT/execute/dir_16393/outputfile: (errno 2) No such file or directory
~~~

This bit has the full path of the file that Condor tried to transfer back to `login.osgconnect.net`. The reason why the file transfer failed is because `outputfile` was never created on the worker node. Remember that at the beginning we said that the user specifically requested `transfer_outputfiles = outputfile`! Condor could not complete this request, and so the job went into Held state instead of finishing normally.

It's quite possible that the error was simply transient, and if we retry, the job will succeed. We can re-queue a job that is in Held state by using `condor_release`: 

~~~
condor_release JOB-ID 
~~~


<h3> Retries with periodic_release </h3>

It is important to consider that computing on the Open Science Grid is a very heterogenous environment. You might have a job that works at 95% of remote sites, but inexplicably fails elsewhere. What to do, then? 

Fortunately, we can ask Condor to check if the job failed by looking at its exit code. If you are familiar with UNIX systems, you may be aware that a successful program returns "0". Anything other than 0 might be considered a failure, and so we can ask Condor to monitor for these and retry if it detects any such failures. 

This can be accomplished by adding the following lines to your submit file:

~~~
# Send the job to Held state on failure. 
on_exit_hold = (ExitBySignal == True) || (ExitCode != 0)  

# Periodically retry the jobs every 60 seconds, up to a maximum of 5 retries.
periodic_release =  (NumJobStarts < 5) && ((CurrentTime - EnteredCurrentStatus) > 60)
~~~

<br/>
<br/>

> ### On your own
> * Run `tutorial exitcode` to download a copy of this exercise. This tutorial contains a simple script that has roughly a 50/50 chance of exiting 1 or 0. <br/>
> * Submit the `exitcode.submit` job <br/>
> * Edit the job, uncomment the line containing `periodic release`, and resubmit your job.<br/> 
> * How many jobs go into Held state in part 2? <br/>
> * In part 3, how many jobs remain in Held state after 10 minutes or so?

<br/>
<br/>

<div class="keypoints" markdown="1">
#### Keypoints
*    *condor_q -better-analyze JOB-ID* command is useful to diagnose failed jobs. 
*    Failed jobs are automatically deducted and periodically retried  with *on_exit_old* and *periodic_release* in the condor submit files.
</div>


