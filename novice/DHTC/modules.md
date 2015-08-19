---
layout: lesson
root: ../..
title: Software Applications on OSG
---
<div class="objectives" markdown="1">

#### Objectives
*   Get to know what are the existing applications on OSG. 
*   Learn how to use modules.
*   Learn how to use the *tutorial* command. 

</div>

<h2> Overview </h2> 
Commonly used software and libraries on the Open Science Grid are available in a
central repository (known as OASIS) and accessed via the *module* command. In this lesson,
we will see how to search for, load, and use software packages.

This lesson will also cover the usage of the built-in *tutorial* command. Using *tutorial*,
we load a variety of job templates that cover basic usage, specific use cases, and best practices.

<h2>Modules on OSG</h2> 

Log in OSG with secure shell  

~~~
$ ssh username@login.osgconnect.net
~~~


The first step in using the module command is to initialize the module system.  This 
step consists of sourcing a shell specific file that adds the module command 
to your environment. For example, initializing module for bash is done as follows:

~~~
$ source /cvmfs/oasis.opensciencegrid.org/osg/modules/lmod/5.6.2/init/bash
~~~

For other shells such as sh, zsh, tcsh, csh, etc., you would replace bash with the shell name (e.g. zsh).


Once the distributed environment modules system is initialized, you can check the 
available modules: 

~~~
$ module avail
 
 
--------------------------- /cvmfs/oasis.opensciencegrid.org/osg/modules/modulefiles/Core ----------------------------
   atlas      fftw/fftw-3.3.4-gromacs    lapack              lmod/5.6.2 (D)    python/3.4
   blast      gromacs/4.6.5              lmod/SiteHook       namd/2.9          settarg/5.6.2
   blender    jpeg                       lmod/SitePackage    python/2.7 (D)
 
  Where:
   (D):  Default Module
 
Use "module spider" to find all possible modules.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
~~~

In order to load a module, you need to run "module load [modulename]".  Say for
example you want to load R package, 

~~~
$ module load R 
~~~

This sets up the R package for you. Now you can do some test calculations with R. 

~~~
$ R # invoke R package

> cos(45)  # simple on-screen calculation with cosine function
[1] 0.525322

~~~

If you want to unload a module, type 

~~~
$ module unload R 
~~~

<h2> Tutorial Command on OSG </h2> 

The built-in *tutorial* command assists a user in getting started on 
OSG.  To see the list of existing tutorials, type

~~~
$ tutorial # will print a list tutorials
~~~

Say for example, you are interested in learning how to run R scripts on OSG, the 
tutorial command sets up the R tutorial for you. 

~~~
$ tutorial R  # prints the following message:


Application Example - R (statistical analysis)

This tutorial will introduce you to using the R statistical programming
language on OSG Connect. By the end of the tutorial:

   * You will have set up R from the OSG OASIS service on the submit host
   * You will know how to use the HAS_CVMFS_oasis_opensciencegrid_org job steering requirement. 

Tutorial 'R' is set up.  To begin:
     cd ~/osg-R
~~~ 

The "tutorial R" command creates a directory "osg-R" containing the neccessary script and input files. 

~~~
mciP.R      # The example R script file
R-wrapper.sh # The job executation file 
R.submit  # The job submission file (will discuss later in the lesson HTCondor scripts)
~~~

Lets focus on "mciP.R" and the "R-wrapper" scripts. The details of "R.submit" script 
will be discussed later when we learn HTCondor scripts.  

The file "mciP.R" is a R script that calculates the value of *pi* using the Monte Carlo
method.  The R-wrapper.sh essentially loads the R module and runs the "mciP.R" 
script. 

~~~
#!/bin/bash # Defines the shell environment.
source /cvmfs/oasis.opensciencegrid.org/osg/modules/lmod/5.6.2/init/bash
module load R    # Loads the module 
Rscript  mcpi.R  # Executation of the R script
~~~

Similar to the R tutorial, there are other tutorials available on OSG. The available 
tutorials serve as templates to develop your own scripts and run the 
calculations on OSG. 

<div class="keypoints" markdown="1">

#### Key Points
*   Many scientific applications are installed on OSG and available for the users. 
*   To use an existing application use the module load command. 
*   The command - *tutorial* helps to access the existing tutorials.  
</div>


