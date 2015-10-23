---
layout: lesson
root: ../..
title: Version Control with Git
---


>  
>####Registration: [Please visit the OIT website ](http://training.oit.duke.edu/research)
>
>####Place: The Edge Workshop Room, Bostock Library, Duke University
>
>####Time: 1:30 PM - 4.30 PM, Oct 27th 2015
>
> This Duke-OSG event is being run by the [open science grid (OSG)](http://opensciencegrid.org/), in collaboration with⋅
> [Software Carpentry](http://software-carpentry.org) 
> and <a href="https://rc.duke.edu/author/mdelongduke-edu/">Duke Research Computing</a>.  The 
> Open Science Grid (OSG) is a national scale distributed infrastructure for 
> scientific computing.  Software Carpentry's mission is to help scientists and engineers⋅
>  become more productive by teaching them basic lab skills for computing
>  like program design, version control, data management, and task automation. Duke Research Computing offers 
> services that are useful to research computing “as it is practiced” across Duke and 
> often in collaboration with researchers at other institutions.
>



Version control is the lab notebook of the digital world:
it's what professionals use to keep track of what they've done
and to collaborate with other people.
Every large software development project relies on it,
and most programmers use it for their small jobs as well.
And it isn't just for software:
books (like this one),
papers,
small data sets,
and anything that changes over time or needs to be shared
can and should be stored in a version control system.


Version control is better than mailing files back and forth:

*    Nothing that is committed to version control is ever lost. Since all old versions of files are saved, it's always possible to go back in time to see exactly who wrote what on a particular day, or what version of a program was used to generate a particular set of results.

*    As we have this record of who made what changes when, we know who to ask if we have questions later on, and, if needed it, revert to a previous version, much like the "undo" feature in an editor.

*    When several people collaborate in the same project, it's possible to accidentally overlook or overwrite someone's changes: the version control system automatically notifies users whenever there's a conflict between one person's work and another's.

Teams are not the only ones to benefit from version control: lone researchers can benefit immensely. Keeping a record of what was changed, when, and why is extremely useful for all researchers if they ever need to come back to the project later on (e.g., a year later, when memory has faded).

### Setup Instructions

*    We will do all the exercises on `login.duke.ci-connect.net`. 
*    If you do not have an account on `duke.ci-connect`, [please sign up](https://duke.ci-connect.net/signup). 
*    You have an account on `duke.ci-connect but forgot the password, [click here](http://duke.ci-connect.net/password).  
*    You also need `SSH` installed on your laptop.  For details, follow [this link](http://swc-osg-workshop.github.io/2015-10-27-duke/setup.html)

### Prerequisites
 In this lesson we use Git from the Unix Shell.
 Some previous experience with the shell is expected,
 *but isn't mandatory*.

<div class="toc" markdown="1">
### Topics
1.  [Introducing Version Control](00-intro.html)
2.  [A Better Kind of Backup](01-backup.html)
3.  [Collaborating](02-collab.html)
4.  [Conflicts](03-conflict.html)
5.  [Hosting Service](04-open.html)

</div>
