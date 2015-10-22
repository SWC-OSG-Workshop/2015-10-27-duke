---
layout: lesson
root: ../..
title: Programming with Python
---


>
>####Registration: [Please visit the OIT website ](http://training.oit.duke.edu/research)
>
>####Place: The Edge Workshop Room, Bostock Library, Duke University
>
>####Time: 9:00 AM - 12.00 PM (part 1) and 1.30 PM - 4.30 PM (part2), Oct 28th 2015
>
> This Duke-OSG event is being run by the [open science grid (OSG)](http://opensciencegrid.org/), in collaboration with⋅
> [Software Carpentry](http://software-carpentry.org)⋅
> and <a href="https://rc.duke.edu/author/mdelongduke-edu/">Duke Research Computing</a>.  The⋅
> Open Science Grid (OSG) is a national scale distributed infrastructure for⋅
> scientific computing.  Software Carpentry's mission is to help scientists and engineers⋅
>  become more productive by teaching them basic lab skills for computing
>  like program design, version control, data management, and task automation. Duke Research Computing offers⋅
> services that are useful to research computing “as it is practiced” across Duke and⋅
> often in collaboration with researchers at other institutions.
>


The best way to learn how to program is to do something useful,
so this introduction to Python is built around a common scientific task:
data analysis.

Our real goal isn't to teach you Python,
but to teach you the basic concepts that all programming depends on.
We use Python in our lessons because:

1.  we have to use *something* for examples;
2.  it's free, well-documented, and runs almost everywhere;
3.  it has a large (and growing) user base among scientists; and
4.  experience shows that it's easier for novices to pick up than most other languages.

But the two most important things are
to use whatever language your colleagues are using,
so that you can share your work with them easily,
and to use that language *well*.

### Setup Instructions
 We will do all the exercises on `login.duke.ci-connect.net`.  If you do not have an account on
 `duke.ci-connect`, [please sign up](https://duke.ci-connect.net/signup). You also need⋅
`SSH` installed on your laptop.  For details, follow [this link](http://swc-osg-workshop.github.io/2015-10-27-duke/setup.html)

### Prerequisites
 Learners need to understand the concepts of files and directories
 (including the working directory) and how to start a Python
 interpreter before tackling this lesson. This lesson references the Jupyter (IPython)
 Notebook although it can be taught through any Python interpreter. The commands in this
 this lesson pertain to Python 2.7.

### Getting ready
 You need to download some files to follow this lesson:

~~~
$ git clone https://github.com/SWC-OSG-Workshop/ExampleData.git
~~~

 We host the IPython server for training here: https://ipython.osgconnect.net/swc-duke
 (You will need to log in with account and password on login.duke.ci-connect.net)

<div class="toc" markdown="1">

### Part-1 (9.00 AM - 12.00 PM)
1.  [Analyzing Patient Data](01-numpy.html)
2.  [Creating Functions](02-func.html)
3.  [Analyzing Multiple Data Sets](03-loop.html)

### Part-2 (1.30 PM - 4.30 PM) 
4.  [Making Choices](04-cond.html)
5.  [Defensive Programming](05-defensive.html)
6.  [Command-Line Programs](06-cmdline.html)

</div>
