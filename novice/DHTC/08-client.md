---
layout: lesson
root: ../..
title: The Connect Client
---

<div class="objectives" markdown="1">

#### Objectives
*   Understand the uses of Connect Client.
*   Set up the Client from another system.
*   Submit and check jobs using the Client.
</div>

<h2>Overview </h2> 

The Connect Client builds on the capabilities of the Duke CI Connect
login node.  When you log in directly to `login.duke.ci-connect.net`,
you have a large toolset consisting of many `condor_xyz` commands.  This
gives you access to thousands of core-hours each hour of real time.

But what if you're not logged in directly?  Maybe you have a large set
of data, or pre- or post-processing tools, on another system.  Maybe you
just need to share with collaborators who all are located elsewhere.
Can you bring the grid to your own site?

Yes. Connect Client is how.


<h2>Getting access to the Connect Client</h2> 

Connect Client runs on Linux and MacOS X.  We expect to be able to
support installation on other operating systems such as FreeBSD as well.

For Mac users, there is a MacOS application that you can download and
drop into /Applications.  This application isn't quite the usual kind
of Mac app though -- because Connect Client is a command line tool,
you only need to run the application once when you install it.  After
that, just leave it alone and the `connect` command will be available
in your Mac Terminal.app.

For Red Hat based Linux systems (including Scientific Linux and CentOS),
there's an RPM repository you can install from.

For other systems, the Client is also available as a source archive that
you can "build" on your computer, but this is generally not recommended
unless you are fluent with Linux/Unix system management.  Contact your
site manager to have the Client added there.  We'll be happy to assist;
just write to support@ci-connect.net.

Once the client is installed, you should be able to open a terminal
program and run the `connect` program.


For today's workshop, Duke Research Computing staff have installed
the client on a system called `dscr-slogin-dev-01.oit.duke.edu`.
You should have an account on this system already; however, to get
access you may first need to log in to `login.oit.duke.edu`. This
gets you past some security requirements.


You can get all the client information at
[http://ci-connect.net/getclient](http://ci-connect.net/getclient).

~~~
mylaptop$ ssh username@login.oit.duke.edu
[big security banner]
username@login.oit.duke.edu's password: 
Last login: Fri Oct 23 11:28:12 2015 from localhost

Self provisioned systems are now available for remote usage through the OIT Virtual Computing Lab service. To reserve your own virtual machine please visit vcl.oit.duke.edu. Additional software images will be added to this service in the coming months.
Last login by user username: Fri Oct 23 11:28 - 11:28 (00:00) from: localhost
username@login-teer-05 [production] ~ $ 
~~~

Then log in to dscr-slogin-dev-01:

~~~
username@login-teer-05 [production] ~ $ ssh dscr-slogin-dev-01
[big security banner]
username@dscr-slogin-dev-01's password: 
[big security banner]
Last login by user username: Thu Oct 29 09:17 - 09:18 (00:00) from: login-teer-05.oit.duke.edu
username@dscr-slogin-dev-01  ~ $ 
~~~

Now you're set.  You can perform the steps in this lesson from either
`dscr-slogin-dev-01` or your laptop (if you've installed the Mac
client).



<h2>Setting up the Connect Client</h2>

Once you have the `connect` command on your own system, you'll need
to run a setup step to "bind" your client to your Duke CI Connect
account.  This is a one-time procedure -- once you've done it you'll
be able to use the client freely any time in the future.

Before we begin, run `connect test`.  This will show what it looks
like when you haven't done a setup yet:

~~~
username@dscr-slogin-dev-01  ~ $ connect test
error: SSHError: No key file available.
error: You have no access to login.duke.ci-connect.net.
error: Did you run "connect setup"?
~~~

So, let's run `connect setup`.  You will need the credentials (username
and password) that you use to `ssh` to `login.duke.ci-connect.net`.
These are the same as the username and password that you created while
registering for Duke CI Connect.

~~~
username@dscr-slogin-dev-01 ~ $ connect setup
Please enter the user name that you created during Connect registration.  Note that it
consists only of letters and numbers, with no @ symbol.

You will be connecting via the login.duke.ci-connect.net server.
Enter your Connect username: dgc
Password for dgc@login.duke.ci-connect.net: 
error: Incorrect password for dgc@login.duke.ci-connect.net
~~~

Oops!  I mistyped my password.

~~~
username@dscr-slogin-dev-01 ~ $ connect setup
Please enter the user name that you created during Connect registration.  Note that it
consists only of letters and numbers, with no @ symbol.

You will be connecting via the login.duke.ci-connect.net server.
Enter your Connect username: dgc
Password for dgc@login.duke.ci-connect.net: 
notice: Ongoing client access has been authorized at login.duke.ci-connect.net.
notice: Use "connect test" to verify access.
~~~

Better.  Now let's do that `test` command again:

~~~
username@dscr-slogin-dev-01 ~ $ connect test
Success! Your client access to login.duke.ci-connect.net is working.
~~~

Your account is now set up.  You won't need to run `connect setup`
again unless you begin using the Client on another computer.


<h2>Using the tutorial command</h2>

You've seen before how to use the `tutorial` command to get
demonstration jobs for learning.  That command is included in the
Connect Client as well.  Let's use it to get some jobs started quickly.

~~~
username@dscr-slogin-dev-01 ~ $ tutorial quickstart
Installing quickstart (master)...
Tutorial files installed in ./tutorial-quickstart.
Running setup in ./tutorial-quickstart...
username@dscr-slogin-dev-01 ~ $ cd tutorial-quickstart
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ ls
log                      README.md  tutorial01.submit  tutorial03.submit
osg-template-job.submit  short.sh   tutorial02.submit
~~~

<h2>Some Connect Client concepts</h2>

Connect Client is oriented around the idea of a "job directory" or
"job repository" -- or "job repo" for short.  The model is inspired by
version control tools like `git`, which we talked about on Tuesday.
We use some similar approaches to information management, although
there's no actual git under the hood.

The main consequence of this is that to effectively use Connect Client,
you should always make a new directory (folder) for each workload that
you submit.  This helps with data management particularly, as we'll see
shortly.

Joint with this, it's important to understand that whereas HTCondor on
`login.duke.ci-connect.net` will deposit job results into your job
directory continuously as jobs finish, *Connect Client will not*.  This
is important to keeping the client scalable to large numbers of users.
We'll see in a moment how to get the data back.


<h2>Submitting work using the Connect Client</h2>

Let's look at our classic "quickstart" tutorial that we set up above.
We'll borrow the `tutorial01.submit` job and modify it a little.  Here's
the original:

~~~
username@dscr-slogin-dev-01 ~ $ cd tutorial-quickstart
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ ls
log                      README.md  tutorial01.submit  tutorial03.submit
osg-template-job.submit  short.sh   tutorial02.submit
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ cat tutorial01.submit 
# The UNIVERSE defines an execution environment. You will almost always use VANILLA. 
Universe = vanilla 

# EXECUTABLE is the program your job will run It's often useful 
# to create a shell script to "wrap" your actual work. 
Executable = short.sh 

# ERROR and OUTPUT are the error and output channels from your job
# that HTCondor returns from the remote host.
Error = job.error
Output = job.output

# The LOG file is where HTCondor places information about your 
# job's status, success, and resource consumption. 
Log = job.log

# +ProjectName is the name of the project reported to the OSG accounting system
# +ProjectName="ConnectTrain"

# QUEUE is the "start button" - it launches any jobs that have been 
# specified thus far. 
Queue 1
~~~

Open this file in an editor -- `nano`, `vim`, your choice -- and make
the following changes:

1. After `Executable`, add `Arguments = 60`.
2. Change `Queue 1` to `Queue 25`.

This will make the file submit 25 instances of a job that will run for
one minute.

Now we'll submit this.  Recall that using HTCondor directly, you enter
`condor_submit`.  With Connect Client, you use the analogous `connect
submit`:

~~~
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ connect submit tutorial01.submit 
++++++++
7 objects sent; 0 objects up to date; 0 errors
Submitting job(s).........................
25 job(s) submitted to cluster 1189.
~~~

So, what happened here?  The `connect submit` command *uploaded* your
job repository to the Connect server, and used HTCondor remotely to
submit the job there.  It sends *all* files that are in your job repo,
because it can't predict well which files your job will end up needing.
(It's good practice anyway to isolate jobs by directory, but this is
why it's actually very important with Connect Client.)  The `+` symbols
represent files that were sent to the server.  If you send this job
again (feel free to do so!) you'll see some `.` characters there,
indicating files that did not need to be uploaded because they're
already there.


<h2>Checking status</h2>

With HTCondor, recall that the `condor_q` command shows jobs in the
queue.  The Client provides an analogous command: `connect q`.  This
command implicitly limits its results to **your** jobs, unless you use
other job selection criteria.

~~~
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ connect q

-- Submitter: duke-login.osgconnect.net : <192.170.227.203:37320> : duke-login.osgconnect.net
 ID      OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD               
1209.0   username       10/29 09:21   0+00:00:00 I  0   0.0  short.sh 60       
1209.1   username       10/29 09:21   0+00:00:00 I  0   0.0  short.sh 60       
1209.2   username       10/29 09:21   0+00:00:00 I  0   0.0  short.sh 60       
...
25 jobs; 0 completed, 0 removed, 25 idle, 0 running, 0 held, 0 suspended
~~~

You can use `connect watch` with the Client, too.  Or you can visit
the [Duke CI Connect site](http://duke.ci-connect.net) and pick **Job
Queue** from the **Resources** menu, and watch jobs flow through the
queue.

`connect histogram` is a fun tool for seeing just where your jobs
are landing.


<h2>Retrieving results</h2>

Okay, great -- now we can submit jobs, but as we've noted, that doesn't
mean that any results come back automatically.  So how can we retrieve
our outputs?

Remember `git` -- to receive updates made elsewhere, you use the command
`git pull`.  Likewise, `connect pull` will grab everything that's been
delivered to your job (on the server) and bring it back to the client 
job repo.

~~~
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ ls log
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ connect pull
+.+++..+...++.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
100 objects retrieved; 7 objects up to date; 0 errors
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ ls log
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ \ls log
job.error.252546-0   job.error.252546-9  job.output.252546-0   job.output.252546-9
job.error.252546-1   job.error.252593-0  job.output.252546-1   job.output.252593-0
job.error.252546-10  job.error.252593-1  job.output.252546-10  job.output.252593-1
job.error.252546-11  job.error.252593-2  job.output.252546-11  job.output.252593-2
job.error.252546-12  job.error.252593-3  job.output.252546-12  job.output.252593-3
[a bunch more...]
~~~

In this case I have submitted about 50 jobs in total, so I'm getting
back all the outputs created in the `log` directory.


<h2>Other HTCondor mimicry</h2>

There are other HTCondor commands that you should be familiar with
by now that are mimicked by Connect Client.  In general these work
just like their regular HTCondor analogues.

* `connect history` -- condor_history (see old job information)
* `connect release` -- condor_release (un-hold a held job)
* `connect rm` -- condor_rm (remove a job from the queue)


Additionally, the `connect status` command works the same as it
does on `login.duke.ci-connect.net`:

~~~
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ connect status
Summary of available resources for all HTCondor pools:
    Total  Owner  Claimed  Unclaimed  Matched  Preempting
=== LOCAL ===
=== Duke Compute Cluster ===
       15      5        2          0        8           0
=== OSG Grid ===
    12427      0    11900        521        0           6
=== UChicago UC3 ===
      622      2      551         69        0           0
=== Syracuse OrangeGrid ===
      312     19      195         98        0           0
~~~

This is an easier to understand version of `condor_status`.


<h2>Other Connect Client commands</h2>

There are a handful of other `connect` commands that may be useful.
You've already seen `connect watch`, for example.

You'll learn later today about software access using OASIS modules.  You
can see a list of available modules while using the Client:

~~~
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ connect modules | head

------------------ /cvmfs/oasis.opensciencegrid.org/osg/modules/modulefiles/Core -------------------
   MUMmer3.23/3.23             freesurfer/5.3.0           namd/2.10                 (D)
   OpenBUGS/3.2.3              freetype/2.5.5             nco/4.3.0
   OpenBUGS-3.2.3/3.2.3        fsl/5.0.8                  netcdf/4.2.0
   R/3.1.1              (D)    gamess/2013                octave/3.8.1
...
~~~

`connect push` will upload your current job repo without submitting.  You shouldn't need to use this very often.

Later you'll learn also about workflow management using DAGs.  `connect dag`
can be used analogously to `condor_submit_dag` to initiate a DAG-based
job.


<h2>More information</h2>

This is an introduction to Connect Client - we hope you find it helpful.
There's a [section of our support site](https://support.opensciencegrid.org/solution/categories/5000161595) dedicated to the Connect Client --
please visit it for ideas and assistance, and as always please contact
us with any problems: connect-support@opensciencegrid.org .


