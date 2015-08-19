---
layout: lesson
root: ../..
title: Software Resources on OSG 
---
<div class="objectives" markdown="1">

#### Objectives
*   Learn what software resources are available on the OSG and how to access them from compute jobs.

</div>

<h2> Available Resources on OSG </h2> 
Commonly used software and libraries on the Open Science Grid are available in a
central repository (known as OASIS) and accessed via the `module` command. We will see how to 
search for, load, and use software packages, whether on the submit node (`login.osgconnect.net`) or on the OSG grid.

We will also cover the usage of the built-in `tutorial` command. Using `tutorial`,
we load a variety of job templates that cover basic usage, specific use cases, and best practices.

<h3> Software Applications </h3>

Log in to OSG Connect 

~~~
$ ssh username@login.osgconnect.net
~~~


The first step in using the `module` command is to initialize the module system.  This 
step consists of sourcing a shell specific file that adds the module command 
to your environment. For example, initializing module for bash is done as follows:

~~~
$ source /cvmfs/oasis.opensciencegrid.org/osg/modules/lmod/5.6.2/init/bash
~~~

For other shells such as `sh`, `zsh`, `tcsh`, `csh`, etc., you would replace bash with the shell name (e.g. `zsh`).


Once the distributed environment modules system is initialized, you can check the 
available modules: 

~~~
$ module avail
------------------------------------- /cvmfs/oasis.opensciencegrid.org/osg/modules/modulefiles/Core ---------------------------
   R/3.1.1                  casino/2.13.211           gromacs/4.6.5          muscle/3.8.31          sdpa/7.3.8
   SitePackage              cdo/1.6.4                 gromacs/5.0.0   (D)    namd/2.9               serf/1.37
   SparseSuite/4.2.1        cmake/3.0.1               hdf5/1.8.9             nco/4.3.0              settarg/5.6.2
   ant/1.9.4                cp2k/2.5.1                hdf5/1.8.12            netcdf/4.2.0           shrimp/2.2.3
   apr/1.5.1                curl/7.37.1               hdf5/1.8.13     (D)    octave/3.8.1           siesta/3.2
   apr-util/1.5.3           ectools                   hmmer/3.1              openbabel/2.3.2        subversion/1.8.10
   aprutil/1.5.3            espresso/5.1              java/7u71              papi/5.3.2             sundials/2.5
   atlas                    expat/2.1.0               java/8u25       (D)    pbsuite/14.9.9         swift/0.94.1
   autodock/4.2.6           fftw/3.3.4-gromacs        jpeg                   pcre/8.35              tcl/8.6.2
   bedtools/2.21            fftw/3.3.4         (D)    lammps/2.0             proot/2014             uclust/2.22
   blasr/1.3.1              fpc/2.6.4                 lapack                 protobuf/2.5           udunits/2.2.17
   blast                    gamess/2013               lmod/5.6.2             python/2.7      (D)    valgrind/3.10
   blender                  gcc/4.6.2                 madgraph/2.1.2         python/3.4             vmd/1.9.1
   boost/1.50.0             geos/3.4.2                matlab/2013b           qhull/2012.1           wget/1.15
   boost/1.56        (D)    git/1.9.0                 matlab/2014a    (D)    root/5.34-21
   bowtie/2.2.3             glpk/4.54                 mercurial/1.9.1        ruby/2.1
   bwa/2014                 gnuplot/4.6.5             mplayer/1.1            samtools/0.1.17
   canopy/1.4.1             graphviz/2.38.0           mrbayes/3.2.2          scons/2.3.4

  Where:
   (D):  Default Module

Use "module spider" to find all possible modules.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".

~~~

In order to load a module, you need to run `module load [modulename]`.  Say for
example you want to load R package, 

~~~
$ module load R 
~~~

This loads the default R package for you. If the package you want has multiple versions, you should 
give the version when loading the module as follows: 

~~~
$ module load R/3.1.1
~~~

Now you can do some test calculations with R. 

~~~
$ R 

> cos(45)  
[1] 0.525322

~~~

Note that some modules may depend on other modules.  Gromacs is an example of
one such module.  If you try to load it, the `module` command will give you an
error indicating this:

~~~
$ module load gromacs

Lmod has detected the following error: Cannot load module "gromacs/5.0.0"
without these modules loaded:
  fftw/3.3.4-gromacs, atlas, lapack


  While processing the following module(s):

  Module fullname  Module Filename
  ---------------  ---------------
  gromacs/5.0.0
  /cvmfs/oasis.opensciencegrid.org/osg/modules/modulefiles/Core/gromacs/5.0.0.lua
~~~

The `module` command will helpfully let you know which modules are needed. In
this case, you'll need the `fftw/3.3.4-gromacs`, `atlas`, and `lapack` modules in 
order to load `gromacs`.  So you'll need to load them first:

~~~
$ module load fftw/3.3.4-gromacs atlas lapack
$ module load gromacs
$ mdrun --version
GROMACS:    gmx mdrun, VERSION 5.0
~~~

The `module` command also lets you view the currently loaded modules using
`module list`:

~~~
$ module list

Currently Loaded Modules:
  1) fftw/3.3.4-gromacs   2) atlas   3) lapack   4) gromacs/5.0.0
~~~

Finally, if you want to unload a module, type: 

~~~
$ module unload R 
~~~

> #### Challenges
> * Load the `wget` application and run `wget --version`
> * Load the `octave` application and run `octave --version`


<h3> Tutorial Command </h3> 

The built-in `tutorial` command assists a user in getting started on 
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
     cd tutorial-R
~~~ 

The `tutorial R` command creates a directory `tutorial-R` containing the neccessary script and input files. 

~~~
$ ls 
mciP.R       # The example R script file
R-wrapper.sh # The job execution file 
R.submit     # The job submission file (will discuss later in the lesson HTCondor scripts)
~~~

Let's focus on `mciP.R` and the `R-wrapper.sh` scripts. The details of `R.submit` script 
will be discussed later when we learn HTCondor scripts.  

The file `mciP.R` is a R script that calculates the value of *pi* using the Monte Carlo
method.  The `R-wrapper.sh` essentially loads the R module and runs the `mciP.R` 
script. 

~~~
$ cat R-wrapper.sh
#!/bin/bash # Defines the shell environment.
source /cvmfs/oasis.opensciencegrid.org/osg/modules/lmod/5.6.2/init/bash
module load R    # Loads the module 
Rscript  mcpi.R  # Execution of the R script
~~~

Similar to the R tutorial, there are other tutorials available on OSG. The available 
tutorials serve as templates to develop your own scripts and run the 
calculations on OSG. 

<div class="keypoints" markdown="1">

#### Key Points
*   The `module` command gives access to software located in the OASIS system
*   The `tutorial` provides access existing tutorials for using various aspects of OSG Connect.
</div>



