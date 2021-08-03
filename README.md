# GEOS Coupled OCEAN Build Instructions

This document descripves the basic step to obtain, compile and execute the GEOS couple Ocean model.

## Obtain the code
The code is in a CVS repository. To obtain it, issue the command:

    cvs co -P -r tag_name GEOSodas_m7
    
where `tag_name` is the name of the CVS tag of interest. It can be for instance `yuri-s2s3-unstable-SLES12`.

You will get a directory named `GEOSodas`.

## Compile the Code
To compile the code, issue the following commands:

     cd GEOSodas/src
     source g5_modules
     ./parallel_build.csh
     
In the last command, you might be asked to provide a `sponsor code id` is if you have more than one on the platform you are using.

An alternative method to compile the code is use a simple script (C-shell here) for a serial build. It assumes that that the script is located at the same level as the `GEOSodas/` directory.

     #!/bin/csh -f

     source /usr/share/modules/init/csh

     set curDIR = `pwd`
     setenv ESMADIR ${curDIR}/GEOSodas

     cd $ESMADIR/src
     module purge
     source g5_modules

     set CLEAN = 0
     set MAKEALL = 1
     set MAKEALLDEBUG = 0

     if ($CLEAN) gmake realclean
     if ($MAKEALLDEBUG) gmake install BOPT=g
     if ($MAKEALL) gmake pinstall

Regardless the method that you use to build the code, a directory named `Linux/` will be created inside `GEOSodas/`.





