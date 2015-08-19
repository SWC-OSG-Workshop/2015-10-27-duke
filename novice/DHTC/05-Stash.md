---
layout: lesson
root: ../..
title: Data storage and transfer
---
<div class="objectives" markdown="1">

#### Objectives
*   Discover how to transfer input and output data  
*   Learn the what, why, how, and whens of using Stash
</div>


<h2> Overview </h2>
In this lesson, we will learn the basics of data storage and transfer on OSG Connect. 

<h2> Stash </h2>
OSG Connect provides a storage system called Stash.  Stash provides a place to
store data needed for jobs or output from jobs in the medium term.  Since
Stash is not backed up, you should transfer job outputs from Stash to your
local system as soon as practical.

This lesson will go over accessing Stash using the OSG Connect login node as
well as other methods such as Globus, and HTTP.
<h2>Exploring the Stash system</h2>

First, we'll look at accessing Stash from the login node. You'll need to log
in to OSG Connect:

~~~
ssh username@login.osgconnect.net #Connect to the login node with your username
passwd:       # your password
~~~

Once done, you can change to the `stash` directory in your home area:

~~~
$ cd ~/stash    
~~~

This directory is an area on Stash that you can use to store files and
directories.  It functions just like any other UNIX directory although it has
additional functions that we'll go over shortly.

For future use, let's create a file in Stash:

~~~
$ cd ~/stash
$ echo "Hello world" > my_hello_world
~~~


<h2>Transferring files to and from Stash using SCP </h2> 

We can transfer files to Stash using `scp`. First, let's 
look at transferring files using `scp`.  `Scp` is a counterpart to ssh that allows for
secure, encrypted file transfers between systems using your ssh credentials.    

To transfer a file from Stash using `scp`, you'll need to run `scp` with the
source and destination.  Files on remote systems are indicated using
user@machine:/path/to/file .  Let's copy the file we just created from Stash to
our local system:

~~~
$ scp username@login.osgconnect.net:~/data/my_hello_world .
~~~

As you can see, `scp` uses similar syntax to the `cp` command that you were shown
previously.  To copy directories using `scp`, you'll just pass the `-r` option to
it.  E.g:

~~~
$ scp -r username@login.osgconnect.net:~/data/my-directory .
~~~

> #### Challenges
>
> * Create a directory with a file called `hello_world_2` in the `~/data` directory and copy it from Stash to your local system.
> * Create a directory called `hello_world_3` on your local system and copy it to the `data` directory.

<h2>Transferring files to and from Stash using Globus</h2>
An alternate method for accessing Stash is to use Globus.  Globus allows you
to initiate transfers between Globus endpoints and will handle the actual file
and directory transfers transparently without needing further input from you.
When the transfer is complete, Globus will send a notification to you indicating
this.

Let's transfer a file from your laptop to Globus.  Doing this will require an 
application called Globus Connect Personal that allows Globus to transfer files
and directories to and from your laptop. First go to the [Globus
page](https://www.globus.org/globus-connect-personal) and download and install
the globus connect personal installer specific to your system.  

While that's running, you'll need to get a setup key from Globus in order to
setup the Globus Connect Personal software.  

*   First login to [OSG Connect](http://www.osgconnect.net) using your OSG Connect username and password
*   Next go to this [page](https://portal.osgconnect.net/xfer/ManageEndpoints#)
*   Click on the add Globus Connect Personal link
*   Enter a name for your endpoint on the page (remember this!)
*   Click on "Generate setup Key"
*   Select the key and copy the key

Finally, start the Globus online personal software that you just installed.  The
installer will ask for the setup key that you obtained from the Globus website.
At this point, the install and setup of Globus Connect Personal is complete.

Now go to [http://www.osgconnect.net](http://www.osgconnect.net) and under the
Transfer menu, select Start Transfer.  For the first endpoint, enter username#name
where name is the name you choose for the endpoint above. You should now see the
files from your laptop displayed.  For the second endpoint, enter
`osgconnect#stash` and hit enter.  You should now see the contents of your home
directory on OSG Connect.  Now double click on the `data` directory.  Select a
file on your laptop and click on the right arrow on the top of the screen to
start a transfer to Stash. You can transfer files or directories to your
laptop by selecting it in the Stash window and selecting the left arrow.

> #### Challenges 
>
> * Copy a file to Stash from your laptop using Globus.  
> * Next copy the `my_hello_world` file from Stash to your laptop using Globus.


<h2>Transferring files from Stash using HTTP</h2>
Stash also allows you to access files using your web browser.  In order to do
this, you'll need to put your file in `~/public`or `~/data/public` (the two locations 
point to the same directory). Any file or directory that is placed 
here  will be made available in the Stash webserver.  Let's make a file
available using the Stash webserver

~~~
$ cd ~/public
$ echo "This is served over the web" > web-file
~~~

Now go to `http://stash.osgconnect.net/+username/` in your browser.  You should
see the file in the listing.  Clicking on the file should give you the contents.

> #### Challenge 
>
> * Create a file called `my-web-file` and make it available through the Stash webserver.


<h2>Using data on Stash in compute jobs</h2> 

Let us do an example calculation to understand the use of Stash and how we download 
the data from the web. We will peform a  molecular dynamics simulation of a small 
protein in implicit water. To get the necessary files, we use the *tutorial* command on 
OSG. 

Log in to OSG Connect:

~~~
$ ssh username@login.osgconnect.net
~~~

Type:

~~~
$ tutorial stash-namd
$ cd ~/tutorial-stash-namd
~~~

*Aside*: [NAMD](http://www.ks.uiuc.edu/Research/namd/) is a widely used molecular dynamics simulation program. It lets users specify a molecule in some initial state and then observe its time evolution subject to forces. Essentially, it lets you go from a specifed molecular [structure](http://en.wikipedia.org/wiki/Superoxide_dismutase#mediaviewer/File:Superoxide_dismutase_2_PDB_1VAR.png) to a [simulation](https://www.youtube.com/watch?v=mk3cLd9PUPA&list=PL418E1C62DD9FC8BA&index=1) of its behavior in a particular environment.  It has been used to study polio eradication, similations of graphene, and studies of biofuels.

You should see the following files in the directory:

~~~
$ ls
namd_stash_run.submit #HTCondor job submission script file.
namd_stash_run.sh #Job execution script file.
ubq_gbis_eq.conf #Input configuration for NAMD.
ubq.pdb #Input pdb file for NAMD.
ubq.psf #Input file for NAMD.
par_all27_prot_lipid.inp #Parameter file for NAMD.
~~~

The file `par_all27_prot_lipid.inp` is the parameter file and is required for 
the NAMD simulations. The parameter file is common data file for the NAMD
simulations. It is a good practice to keep the common files, like  the parameter file 
in our example, in the Stash storage.  

~~~
mv par_all27_prot_lipid.inp ~/public/.  
~~~

You can view the parameter file using your web browser by going to 
`http://stash.osgconnect.net/+yourusername`.

Now we want the parameter file available on the execution (worker) machine when the 
simulation starts to run. As mentioned early, the data on the Stash is available to 
the execution machines. This means the execution machine can transfer the data from 
Stash as a part of the job execution. So we have to script this in the job execution 
script. 

You can see that the job script `namd_stash_run.sh` has the following lines:

~~~
$ cat namd_stash_run.sh
#!/bin/bash 
source /cvmfs/oasis.opensciencegrid.org/osg/modules/lmod/5.6.2/init/bash 
module load namd/2.9  
wget http://stash.osgconnect.net/+username/par_all27_prot_lipid.inp  
namd2 ubq_gbis_eq.conf  
~~~

In the above script, you will have to insert your "username" in URL address. The
parameter file located on Stash is downloaded using the #wget# utility.  
 

Now we submit the NAMD job. 

~~~
$ condor_submit namd_stash_run.submit 
~~~

Once the job completes, you will see non-empty "job.out.0" file where 
the standout output from the programs are written as default.   

~~~
$ tail job.out.0

WallClock: 6.084453  CPUTime: 6.084453  Memory: 53.500000 MB
Program finished.
~~~

The above lines indicate the NAMD simulation was successful. 

 
<div class="keypoints" markdown="1">

#### Key Points
* Stash is located at ~/stash and ~/public on login.osgconnect.net. 
* Data can be transferred in and out of Stash using scp, Globus, and HTTP 
* Data on Stash can be accessed by jobs running on compute nodes. 
</div>

