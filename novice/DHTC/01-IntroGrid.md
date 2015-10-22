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
*   Computation that is a good match for Duke CI Connect
*   Computation that is NOT a good match for Duke CI Connect
*   Learn what software resources are available on the OSG and how to access them from compute jobs.

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



## Computation that is a good match for Duke CI Connect 

High throughput workflows with simple system and data dependencies are a good 
fit for Duke CI Connect. Typically these workflows can be decomposed into multiple
tasks that can be carried out independently.  Ideally, these tasks will download 
data for input, run some computation on it and then return results (which may be 
used by future tasks).

Jobs submitted into Duke CI Connect will be executed on machines at several 
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
    that the Duke CI Connect Virtual Cluster does not have a global shared file 
    system, so jobs with such dependencies will not work.</li>
<li>   No shared filesystem. Jobs must transfer all executables, input data, and 
    output data. HTCondor can transfer the files for you, but you will have to 
    identify and list the files in your HTCondor job description file. </li>
</ul>

## Computation that is NOT a good match for Duke CI Connect 

The following are examples of computations that are NOT good matches for 
Duke CI Connect:
<ul>
<li>   Tightly coupled computations, for example MPI based communication, will 
    not work well on Duke CI Connect due to the distributed nature of the infrastructure.</li>
<li>   Computations requiring a shared file system will not work, as there is 
    no shared filesystem between the different clusters on Duke CI Connect.</li>
<li>   Computations requiring complex software deployments or proprietary software 
    are not a good fit.  There is limited support for distributing software to 
    the compute clusters, but for complex software, or licensed software, 
    deployment can be a major task.</li>
</ul>


<h2> Available Resources on OSG </h2> 
Commonly used software and libraries on the Open Science Grid are available in a
central repository (known as OASIS) and accessed via the `module` command. We will see how to 
search for, load, and use software packages, whether on the submit node (`login.duke.ci-connect.net`) or on the OSG grid.

We will also cover the usage of the built-in `tutorial` command. Using `tutorial`,
we load a variety of job templates that cover basic usage, specific use cases, and best practices.

<h3> Software Applications </h3>

Log in to Duke CI Connect 

~~~
$ ssh username@login.duke.ci-connect.net
~~~

<!---
The first step in using the `module` command is to initialize the module system.  This 
step consists of sourcing a shell specific file that adds the module command 
to your environment. For example, initializing module for bash is done as follows:

~~~
$ source /cvmfs/oasis.opensciencegrid.org/osg/modules/lmod/5.6.2/init/bash
~~~

For other shells such as `sh`, `zsh`, `tcsh`, `csh`, etc., you would replace bash with the shell name (e.g. `zsh`).


Once the distributed environment modules system is initialized, you can check the 
available modules: 
-->
Once logged in, the fist `module` command you may want to issue is `module avail`, which checks the available modules:

~~~
$ module avail

--------------------------------- /cvmfs/oasis.opensciencegrid.org/osg/modules/modulefiles/Core ---------------------------------
   MUMmer3.23/3.23             eemt/0.1                   java/7u71                        proot/2014
   OpenBUGS/3.2.3              elastix/2015               java/8u25                 (D)    protobuf/2.5
   OpenBUGS-3.2.3/3.2.3        espresso/5.1               jpeg/6b                          psi4/0.3.74
   R/3.1.1              (D)    espresso/5.2        (D)    jpeg/9a                   (D)    python/2.7        (D)
   R/3.2.0                     ete2/2.3.8                 lammps/2.0                       python/3.4
   R/3.2.1                     expat/2.1.0                lapack/3.5.0                     qhull/2012.1
   R/3.2.2                     ffmpeg/0.10.15             libgfortran/4.4.7                root/5.34-32
   SitePackage                 ffmpeg/2.5.2        (D)    libtiff/4.0.4                    rosetta/2015
   SparseSuite/4.2.1           fftw/3.3.4-gromacs         lmod/5.6.2                       ruby/2.1
   ant/1.9.4                   fftw/3.3.4          (D)    madgraph/2.1.2                   saga/2.2.0
   apr/1.5.1                   fiji/2.0                   madgraph/2.2.2            (D)    samtools/0.1.17
   aprutil/1.5.3               fpc/2.6.4                  matlab/2013b                     scons/2.3.4
   arc-lite/2015               freesurfer/5.3.0           matlab/2014a                     sdpa/7.3.8
   atlas/3.10.1                freetype/2.5.5             matlab/2014b                     serf/1.37
   atlas/3.10.2         (D)    fsl/5.0.8                  matlab/2015a                     settarg/5.6.2
   autodock/4.2.6              gamess/2013                matlab/2015b              (D)    shelx/2015
   bedtools/2.21               gcc/4.6.2                  mercurial/1.9.1                  shrimp/2.2.3
   blasr/1.3.1                 gcc/4.6.4                  mixmodlib/3.1                    siesta/3.2
   blast                       gcc/4.8.1                  mpc/1.0.3                        sqlite/3.8.11.1
   blender                     gcc/4.9.2           (D)    mpfr/3.1.3                       sra/2.5.4
   boost/1.50.0                gdal/2.0.0                 mplayer/1.1                      stashcp/1.0.0
   boost/1.56                  geant4/9.4p02              mrbayes/3.2.2                    stashcp/2.2
   boost/1.57.0         (D)    geos/3.4.2                 muscle/3.8.31                    stashcp/2.5       (D)
   bowtie/2.2.3                git/1.9.0                  mysql/5.1.73                     subversion/1.8.10
   bwa/2014                    glpk/4.54                  namd/2.9                         sundials/2.5
   canopy/1.4.1                gmp/6.0.0                  namd/2.10.cuda                   swift/0.94.1
   casino/2.13.211             gnome_libs/1.0             namd/2.10                 (D)    swift/0.96.2      (D)
   ccp4/2015                   gnuplot/4.6.5              nco/4.3.0                        tcl/8.6.2
   cctools/4.4.2               graphviz/2.38.0            netcdf/4.2.0                     tophat/2.0.13
   cctools/5.2.3        (D)    grass/6.4.4                octave/3.8.1                     uclust/2.22
   cdo/1.6.4                   gromacs/4.6.5              openbabel/2.3.2                  udunits/2.2.17
   clhep/2.1.0.1               gromacs/5.0.0       (D)    opencv/2.4.10                    unixodbc/2.3.2
   clhep/2.2.0.8        (D)    gromacs/5.0.5.cuda         papi/5.3.2                       valgrind/3.10
   cmake/3.0.1                 gromacs/5.0.5              pbsuite/14.9.9                   vmd/1.9.1
   connect-client/0.2.1        gsl/1.16                   pcre/8.35                        wget/1.15
   connect-client/0.3.0        hdf5/1.8.9                 pegasus/4.4.2-image_tools        wxgtk/3.0.2
   connect-client/0.4.0 (D)    hdf5/1.8.12                phenix/1.10                      xrootd/4.1.1
   cp2k/2.5.1                  hdf5/1.8.13         (D)    poppler/0.24.1            (D)    xrootd/4.2.1      (D)
   cufflinks/2.2.1             hmmer/3.1                  poppler/0.32                     zlib/1.2.8
   curl/7.37.1                 intelMKL/11.3.0.109        povray/3.7
   ectools                     jasper/1.900.1             proj/4.9.1

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
one such module.  If you load it, the `module` command will automatically load the modules on which gromacs depends:

~~~
$ module load gromacs
$ module list

Currently Loaded Modules:
  1) fftw/3.3.4-gromacs   2) atlas/3.10.2   3) libgfortran/4.4.7   4) lapack/3.5.0   5) gromacs/5.0.0
~~~

The `module` command will helpfully load the required modules, or let you know which modules are needed. In
this case, you need the `fftw/3.3.4-gromacs`, `atlas`, `libgfortran`, and `lapack` modules in 
order to load `gromacs`.  So they have been conveniently loaded for you.  Alternatively, you can load the required modules yourself first before loading gromacs:

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
  1) fftw/3.3.4-gromacs   2) atlas/3.10.2   3) libgfortran/4.4.7   4) lapack/3.5.0   5) gromacs/5.0.0
~~~

Finally, if you want to unload a module, type: 

~~~
$ module unload gromacs
~~~

> #### Challenges
> * Load the `wget` application and run `wget --version`
> * Load the `octave` application and run `octave --version`


<h3> Tutorial Command </h3> 

The built-in `tutorial` command assists a user in getting started on 
OSG.  To see the list of existing tutorials, type

~~~
$ tutorial
usage: tutorial list                 - show available tutorials
       tutorial info <tutorial-name> - show details of a tutorial
       tutorial <tutorial-name>      - set up a tutorial

Currently available tutorials:
AutoDockVina ............... Ligand-Receptor docking with AutoDock Vina
R .......................... Estimate Pi using the R programming language
R-addlibSNA ................ Add-on external R library - sna example
ScalingUp-R ................ Scaling up compute resources - R example
blast ...................... blast sequence analysis
cp2k ....................... How-to for the electronic structure package CP2K
dagman-namd ................ Launch a series of NAMD simulations via an HTCondor DAG
error101 ................... Use condor_q -better-analyze to analyze stuck jobs
exitcode ................... Use HTCondor's periodic_release to retry failed jobs
gromacs .................... Molecular dynamics using GROMACS
htcondor-transfer .......... Transfer data via HTCondor's own mechanisms
matlab-HelloWorld .......... Creating standalone MATLAB application - Hello World 
matlab-RandomMatrix ........ Creating standalone MATLAB application - Random Matrix and Wigner Distribution
matlab-ResonanceODE ........ Creating standalone MATLAB application - Ordinary Differential Equations
matlab-SimulatedAnnealing .. Creating standalone MATLAB application - Optimization Tool box
namd ....................... Run a molecular dynamics simulation using NAMD
nelle-nemo ................. Running Nelle Nemo's goostats on the grid
oasis-parrot ............... Software access with OASIS and Parrot
octave ..................... Matrix manipulation via the Octave programming language
pegasus .................... An introduction to the Pegasus job workflow manager
pegasus-blast .............. BLAST search on large data set using Pegasus
pegasus-namd ............... Pegasus workflow to run large scale simulations - NAMD examples
pegasus-vina ............... Pegasus workflow for docking set of ligands on a give receptor with vina program 
photodemo .................. A complete analysis workflow using HTTP transfer
quickstart ................. How to run your first OSG job
root ....................... Inspect ntuples using the ROOT analysis framework
scaling .................... Learn to steer jobs to particular resources
scaling-up-resources ....... A simple multi-job demonstration
software ................... Software access tutorial
stash-chirp ................ Use the chirp I/O protocol for remote data access
stash-http ................. Retrieve job input files from Stash via HTTP
stash-namd ................. Provide input files for NAMD via Stash's HTTP interface
swift ...................... Introduction to the SWIFT parallel scripting language

Enter "tutorial name-of-tutorial" to clone and try out a tutorial.
~~~

Each of these rows above is a tutorial that you can work through on your
own, after the workshop.  We add new tutorials from time to time, as well.
Each tutorial has a `README.md` file within that gives teaching material
on what the tutorial is trying to illustrate, and you can read them online
at the [OSG Helpdesk](https://support.opensciencegrid.org/support/home).

Note the word _clone_ in that last line of output.  There's no mistake
that it's the same term as we used for copying a git repository.  Each
tutorial is version-controlled, and actually resides on GitHub.  When
you use the `tutorial` command you're getting content from there.

Say for example, you are interested in learning how to run R scripts on OSG, the 
tutorial command sets up the R tutorial for you. 

~~~
$ tutorial R  # prints the following message:


Installing R (master)...
Tutorial files installed in ./tutorial-R.
Running setup in ./tutorial-R...
~~~ 

The `tutorial R` command creates a directory `tutorial-R` containing the neccessary script and input files. 

~~~
$ ls 
mciP.R       # The example R script file
README.md    # The tutorial README file (with instructions and explanation of the material)
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
#!/bin/bash

EXPECTED_ARGS=1

if [ $# -ne $EXPECTED_ARGS ]; then
  echo "Usage: R-wrapper.sh file.R"
  exit 1
else
  source /cvmfs/oasis.opensciencegrid.org/osg/modules/lmod/current/init/bash
  module load R
  Rscript $1
fi
~~~

Similar to the R tutorial, there are other tutorials available on OSG. The available 
tutorials serve as templates to develop your own scripts and run the 
calculations on OSG. 


##How to get help using Duke CI Connect

Feel free to contact the OSG User Support team at: [user-support@opensciencegrid.org](mailto:user-support@opensciencegrid.org).


<div class="keypoints" markdown="1">
#### Key Points
*   OSG resources are distributed across 120 institutions and  supports scientific computing of 2 million core CPU hours per day.   
*   Many scientific applications are installed on OSG and available for the users. 
*   The `module` command gives access to software located in the OASIS system
*   The `tutorial` provides access existing tutorials for using various aspects of Duke CI Connect.
</div>



