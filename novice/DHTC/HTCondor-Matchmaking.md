---
layout: lesson
root: ../..
title: Selecting Sites and Resources in OSG Connect
---
<div class="objectives" markdown="1">

#### Objectives
*   Learn how HTCondor schedules jobs
*   Learn how to select resources or sites using Classads
</div>

## Basics of HTCondor Matchmaking
As you have seen in the previous lesson, HTCondor is a batch management system 
that handles running jobs on a cluster. Like other full-featured batch systems,
HTCondor provides a job queueing mechanism, scheduling policy, priority scheme, 
resource monitoring, and resource management. Users submit their jobs to 
HTCondor scheduler. HTCondor places them into a queue, chooses when and 
where to run the jobs based upon a policy, carefully monitors their 
progress, and ultimately informs the user upon completion.  This lesson will 
go over the some of the specifics of how HTCondor selects compute nodes where 
it should run particular jobs.  

HTCondor selects nodes on which to run particular jobs using a matchmaking
process.  When a job is submitted to HTCondor, HTCondor generates a set of 
attributes that the job needs in order to run.  These attributes function 
like classified ads in the newspaper and are called classads.  The classads 
for a job indicate what it is looking for, just like a help wanted ad.  For
example:

~~~
MemoryUsage = ( ( ResidentSetSize + 1023 ) / 1024 )
MATCH_EXP_JOBGLIDEIN_ResourceName = "UConn-OSG"
In = "/dev/null"
Requirements = ( TARGET.Arch == "X86_64" ) && ( TARGET.OpSys == "LINUX" ) && ( TARGET.Disk >= RequestDisk ) && ( TARGET.Memory >= RequestMemory ) && ( TARGET.HasFileTransfer )
~~~

Likewise, compute nodes that can run jobs also advertise a set of classads that
indicate what resources are available and some information about the
environment.  For example:

~~~
HasReconnect = true
HasJobDeferral = true
HOLD_GRACE_TIME = 0
GLIDEIN_Job_Max_Time = 34800
GLIDEIN_Country = "US"
GLIDEIN_Glexec_Use = "NEVER"
GLIDEIN_REQUIRE_VOMS = false
CVMFS_oasis_opensciencegrid_org_REVISION = 2921
Arch = "X86_64"
HAS_CVMFS_cms_cern_ch = true
OSGVO_OS_STRING = "RHEL 5"
HAS_TCSH = true
HAS_IRODS_PLUGIN = true
CVMFS_atlas_cern_ch_REVISION = 6605
UpdateSequenceNumber = 136
GLIDEIN_COLLECTOR_NAME = "osg-flock.grid.iu.edu:9731"
LoadAvg = 7.990000
OpSysAndVer = "SL5"
HAS_SQUID = true
~~~

HTCondor takes a list of classads from jobs and from compute nodes and then
tries to make the classads with job requirements with the classads with compute
node capabilities.  When the two match, HTCondor will run the job on the
compute node.

## Using Classads to select resources and sites

Users can also add classads to their job submissions files in order to change
which compute nodes their jobs will match.  This section will show how to 
use classads to select compute nodes with certain resources or at certain sites.

### Selecting specific resources

Nelle Nemo, a marine biologist, would like run her applications using OSG Connect.
However, her applications only run on Scientific Linux 6 (SL6) systems.  On other
linux systems, her applications will crash after about 10 minutes of starting.
Since OSG Connect will run jobs on compute nodes running SL5 or SL6, she would like 
make sure her jobs only run on compute nodes running SL6 to avoid wasting 
execution time.

In order to do this, first let's look at a simple HTCondor submit file:

~~~
# The UNIVERSE defines an execution environment. You will almost always use
# VANILLA. 
Universe = vanilla 

# EXECUTABLE is the program your job will run It's often useful 
# to create a shell script to "wrap" your actual work. 
Executable = checkos.sh

# ERROR and OUTPUT are the error and output channels from your job
# that HTCondor returns from the remote host.
Error = job.error
Output = job.output

# The LOG file is where HTCondor places information about your 
# job's status, success, and resource consumption. 
Log = job.log

# QUEUE is the "start button" - it launches any jobs that have been 
# specified thus far. 
Queue 1
~~~

The job script currently just setups the parameters for a job and then submits a
single job to the queue.  Let's submit this job and look at the requirements for
this job

~~~
[user@login01 ~]$ condor_q -l 1316101.0 | grep 'Requirements ='
Requirements = ( TARGET.Arch == "X86_64" ) && ( TARGET.OpSys == "LINUX" ) && (
TARGET.Disk >= RequestDisk ) && ( TARGET.Memory >= RequestMemory ) && ( (
TARGET.HasFileTransfer ) || ( TARGET.FileSystemDomain == MY.FileSystemDomain ) )
~~~

Although, the submit file didn't include any requirements, HTCondor automatically adds some
to the job's classads so that by default the job will only run on compute nodes
that have the same architecture, OS, and can provide access to input and output
facilities.  

In order to add additional requirements to the job's classads, we will need to 
use the `Requirements` option in the submit file.  HTCondor will include any
requirements specified using this option to the default job requirements.  So,
if we use the following submit file:

~~~
# The UNIVERSE defines an execution environment. You will almost always use
# VANILLA.
Universe = vanilla

# EXECUTABLE is the program your job will run It's often useful
# to create a shell script to "wrap" your actual work.
Executable = checkos.sh

# ERROR and OUTPUT are the error and output channels from your job
# that HTCondor returns from the remote host.
Error = job.error
Output = job.output

# The LOG file is where HTCondor places information about your
# job's status, success, and resource consumption.
Log = job.log

# Require a SL6/RHEL6 system
Requirements = (OpSys == "LINUX" && OpSysMajorVer == 6)

# QUEUE is the "start button" - it launches any jobs that have been
# specified thus far.
Queue 1
~~~

and then examine requirements that are generated, we'll see that the job now
requires a RHEL6 or SL6 system in addition to the default requirements:

~~~
[user@login01 ~]$ condor_q -l 1316172.0 | grep 'Requirements ='
Requirements = ( ( OpSys == "LINUX" && OpSysMajorVer == 6 ) ) && ( TARGET.Arch == "X86_64" ) && ( TARGET.Disk >= RequestDisk ) && ( TARGET.Memory >= RequestMemory ) && ( ( TARGET.HasFileTransfer ) || ( TARGET.FileSystemDomain == MY.FileSystemDomain ) )
~~~

Using this Nelle can now run her application without wasting cpu time. 

Now suppose Nelle has a new version of her application that requires 3GB of RAM
in order to run.  By default, most sites available through OSG Connect 
terminate jobs that use more than 2GB of RAM.  So in order to get these nodes,
Nelle would have to change the requirements slightly in order to request a
compute node that allows applications to use 3GB of RAM:

~~~
# The UNIVERSE defines an execution environment. You will almost always use
# VANILLA.
Universe = vanilla

# EXECUTABLE is the program your job will run It's often useful
# to create a shell script to "wrap" your actual work.
Executable = checkos.sh

# ERROR and OUTPUT are the error and output channels from your job
# that HTCondor returns from the remote host.
Error = job.error
Output = job.output

# The LOG file is where HTCondor places information about your
# job's status, success, and resource consumption.
Log = job.log

# Require a SL6/RHEL6 system
Requirements = (OpSys == "LINUX" && OpSysMajorVer == 6 && Memory >=3072)

# QUEUE is the "start button" - it launches any jobs that have been
# specified thus far.
Queue 1
~~~

If we look at the new requirements, we'll see that the requirements now reflect 
the memory requirement of 3GB

~~~
[user@login01 ~]$ condor_q -l 1316172.0 | grep 'Requirements ='
Requirements = ( ( OpSys == "LINUX" && OpSysMajorVer == 6 && Memory >= 3072 ) ) && ( TARGET.Arch == "X86_64" ) && ( TARGET.Disk >= RequestDisk ) && ( TARGET.Memory >= RequestMemory ) && ( ( TARGET.HasFileTransfer ) || ( TARGET.FileSystemDomain == MY.FileSystemDomain ) )
~~~

If you let the job run, you'll notice that it takes much longer than usual for
the job to get scheduled and run on a compute node.  This is because as your
requirements get stricter, the number of nodes available decreases in size.  If
you have sufficiently strict job requirements, your jobs may not match with
any compute nodes and never run!

### Selecting sites with classads

Classads can also be used to select or avoid specific sites on OSG Connect.
This is also done using the `Requirements` option in your job submit file.  In
this case, the important thing to know is that compute nodes for every  site that runs jobs
from OSG Connect set a classad called `GLIDEIN_ResourceName` with the name of
the site.  So in order to run on a specific site like Midwest Tier 2, you would 
use a requirement like in in the following submit file:

~~~
# The UNIVERSE defines an execution environment. You will almost always use
# VANILLA.
Universe = vanilla

# EXECUTABLE is the program your job will run It's often useful
# to create a shell script to "wrap" your actual work.
Executable = checkos.sh

# ERROR and OUTPUT are the error and output channels from your job
# that HTCondor returns from the remote host.
Error = job.error
Output = job.output

# The LOG file is where HTCondor places information about your
# job's status, success, and resource consumption.
Log = job.log

# Require a SL6/RHEL6 system
Requirements = (GLIDEIN_ResourceName == "MWT2") && (IS_GLIDEIN == True)

# QUEUE is the "start button" - it launches any jobs that have been
# specified thus far.
Queue 1
~~~

Try submitting a job using this requirement and see how it works.

In order to avoid running jobs on certain sites, the requirements need to be 
changed a little but the general idea is the same:

~~~
# The UNIVERSE defines an execution environment. You will almost always use
# VANILLA.
Universe = vanilla

# EXECUTABLE is the program your job will run It's often useful
# to create a shell script to "wrap" your actual work.
Executable = checkos.sh

# ERROR and OUTPUT are the error and output channels from your job
# that HTCondor returns from the remote host.
Error = job.error
Output = job.output

# The LOG file is where HTCondor places information about your
# job's status, success, and resource consumption.
Log = job.log

# Require a SL6/RHEL6 system
Requirements = (GLIDEIN_ResourceName != "MWT2") && (IS_GLIDEIN == True)

# QUEUE is the "start button" - it launches any jobs that have been
# specified thus far.
Queue 1
~~~


Try submitting a job with this new requirement and see how this affects your
job.  

You'll probably notice that your job ran much faster with the second set of 
requirements.  This is because the selecting compute nodes on a given site
signficantly reduces the number of potential places that will run your job
resulting in longer waits to run your jobs.
