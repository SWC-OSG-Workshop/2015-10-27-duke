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
2. Change `Queue 1` to `Queue 50`.

This will make the file submit 50 instances of a job that will run for
one minute.

Now we'll submit this.  Recall that using HTCondor directly, you enter
`condor_submit`.  With Connect Client, you use the analogous `connect
submit`:

~~~
username@dscr-slogin-dev-01 ~/tutorial-quickstart $ connect submit tutorial01.submit 
++++++++
7 objects sent; 0 objects up to date; 0 errors
Submitting job(s).
1 job(s) submitted to cluster 1189.
~~~

So, what happened here?  The `connect submit` command *uploaded* your
job repository to the Connect server, and used HTCondor remotely to
submit the job there.  It sends *all* files that are in your job repo,
because it can't predict well which files your job will end up needing.
It's good practice anyway to isolate jobs by directory, but this is why
it's actually very important with Connect Client.
