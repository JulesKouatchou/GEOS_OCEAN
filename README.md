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

Regardless the method that you use to build the code, a directory named `Linux/` will be created inside `GEOSodas/`. The `Linux/` directory contains temporary files, libraries and executables created during the compilation process. To make sure that the code was successfully compiled, check that the file `GEOSgcm.x` exists in the directory `GEOSodas/src/Applications/GEOSgcm_App/`.

## Create the Experiment Directory
After building the executable, we need to create an experimenet directory. The code comes with a tool that allows you to do that. We recommend that you always go through this process whenever to start a new experiment.

The first step is to go to the directory `GEOSodas/src/Applications/GEOSgcm_App/`. Then check that the following files are available:

   AGCM.rc.tmpl GAAS_GridComp.rc.tmpl GEOSgcm.x gcm_run.j gcm_setup ocean_das_config.tmpl

There are many other files in the directory but the above ones play an imporatnt role in setting the experiment directory and driving the model.

The script `gcm_setup`, when executed, asks a series of questions to determine a set of parameters and input files needed to run the model. 

Issue the command:

      ./gcm_setup
      
and provide the information requested. When you are asked if:
- Yoou want to run the COUPLED Ocean/Sea-Ice Model, answer `YES`.
- You want to to run the REPLAY DUAL OCEAN Model, answer
      - `YES` is you intend to run '`ODAS`
      - `NO` otherwise.

The avialbale reoslutions are:

| Atmosphere | Ocean |
| :--- | :--- |
| c90x72  | 360x200x50 |
| c180x72 | 720x410x40 | 
| c180x72 | 1440x1080x50 (S2Sv3 resolution) |


Here, we provide a sample dialogue:
  
     Enter the Experiment ID:
          test_odas
     Enter a 1-line Experiment Description:
          Test ODAS
     Do you wish to CLONE an old experiment? (Default: NO or FALSE)
          NO
     Enter the Atmospheric Horizontal Resolution code:
     -----------------------------------------------------------
     Lat/Lon                     Cubed-Sphere
     -----------------------------------------------------------
        b --  2  deg                c48  --  2   deg 
        c --  1  deg                c90  --  1   deg 
        d -- 1/2 deg                c180 -- 1/2  deg (56-km) 
        e -- 1/4 deg (35-km)        c360 -- 1/4  deg (28-km)  
                                    c720 -- 1/8  deg (14-km) 
                                    c1440 - 1/16 deg ( 7-km) 
                                              DYAMOND Grids                 
                                    c768 -- 1/8  deg (12-km) 
                                    c1536 - 1/16 deg ( 6-km) 
                                    c3072 - 1/32 deg ( 3-km) 
 
          c180
     Enter the Atmospheric Model Vertical Resolution: LM (Default: 72)
          72
     Do you wish to run the COUPLED Ocean/Sea-Ice Model? (Default: NO or FALSE)
          Yes

     Enter the Ocean Lat/Lon Horizontal Resolution: IM JM (Default: 360 200)
          1440 1080
     Enter the Ocean Model Vertical Resolution: LM (Default: 50)
          50
     Do you wish to run the REPLAY DUAL OCEAN Model? (Default: NO or FALSE)
          YES

     Do you wish to run the AODAS Model? (Default: NO or FALSE)
          YES

     Enter the choice of  Restart Record Parameters: Frequency (hhmmss): (Default: 240000)
          240000
     Enter the choice of  Restart Record Parameters: Reference Date (YYYYMMDD): (Default: 19800101)
          19800101
     Enter the choice of  Restart Record Parameters: Reference Time (hhmmss): (Default: 210000)
          210000
     Enter the choice of  Land Surface Model: use 1 (Default: Catchment Model), 2 (CatchmentCN Model)
          1

     Do you wish to run  the Runoff Routing Model? (Default: NO or FALSE)
          NO

     Do you wish to run GOCART with Actual or Climatological Aerosols? (Enter: A (Default) or C)
          A

     Enter the GOCART Emission Files to use: MERRA2 (Default), PIESA, CMIP, NR, MERRA2-DD or OPS:
          MERRA2

     Enter the tag or directory (/filename) of the HISTORY.AGCM.rc.tmpl to use
     (To use HISTORY.AGCM.rc.tmpl from current build, Type:  Current         )
     -------------------------------------------------------------------------
     Hit ENTER to use Default Tag/Location: (/discover/nobackup/jkouatch/GEOS_OCEAN/yuri-s2s3-unstable-SLES12/GEOSodas/src/Applications/GEOSgcm_App)
 
     Enter Desired Location for the HOME Directory (to contain scripts and RC files)
     Hit ENTER to use Default Location:
     ----------------------------------
     Default:  /gpfsm/dnb32/jkouatch/GEOS_OCEAN/yuri-s2s3-unstable-SLES12/test_odas

     Enter Desired Location for the EXPERIMENT Directory (to contain model output and restart files)
     Hit ENTER to use Default Location:
     ----------------------------------
     Default:  /gpfsm/dnb32/jkouatch/GEOS_OCEAN/yuri-s2s3-unstable-SLES12/test_odas

     Enter Location for Build directory containing:  src/ Linux/ etc...
     Hit ENTER to use Default Location:
     ----------------------------------
     Default:  /discover/nobackup/jkouatch/GEOS_OCEAN/yuri-s2s3-unstable-SLES12/GEOSodas

     Current GROUPS: k3002 a930b s0942 s1043 m2val j1008
     Enter your GROUP ID for Current EXP: (Default: s1043)
     -----------------------------------

     sending incremental file list
     GEOSgcm.x

     sent 140,652,369 bytes  received 35 bytes  93,768,269.33 bytes/sec
     total size is 140,617,928  speedup is 1.00
 
     Creating gcm_run.j for Experiment: test_odas 
     Creating gcm_archive.j for Experiment: test_odas 
     Creating gcm_regress.j for Experiment: test_odas 
     Creating gcm_convert.j for Experiment: test_odas 
     Creating gcm_plot.tmpl for Experiment: test_odas 
     Creating gcm_quickplot.csh for Experiment: test_odas 
     Creating gcm_moveplot.j for Experiment: test_odas 
     Creating gcm_stats.j for Experiment: test_odas 
     Creating gcm_forecast.tmpl for Experiment: test_odas 
     Creating gcm_forecast.setup for Experiment: test_odas 
     Creating gcm_emip.setup for Experiment: test_odas 
     Creating CAP.rc.tmpl for Experiment: test_odas 
     Creating AGCM.rc.tmpl for Experiment: test_odas 
     Creating GAAS_GridComp.rc.tmpl for Experiment: test_odas 
     Creating ocean_das_config.tmpl for Experiment: test_odas 
     Creating HISTORY.rc.tmpl for Experiment: test_odas 
     Creating input.nml for Experiment: test_odas 
     Creating diag_table for Experiment: test_odas 
     Creating field_table for Experiment: test_odas 
     Creating plotocn.j for Experiment: test_odas 
     Creating __init__.py for Experiment: test_odas 
     Creating fvcore_layout.rc for Experiment: test_odas 
 
     /bin/mv: '/gpfsm/dnb32/jkouatch/GEOS_OCEAN/yuri-s2s3-unstable-SLES12/test_odas/GAAS_GridComp.rc.tmpl' and '/gpfsm/dnb32/jkouatch/GEOS_OCEAN/yuri-s2s3-unstable-SLES12/test_odas/GAAS_GridComp.rc.tmpl' are the same file
     Done!
     -----
 
     Build Directory: /discover/nobackup/jkouatch/GEOS_OCEAN/yuri-s2s3-unstable-SLES12/GEOSodas
     ----------------
 
 
     The following executable has been placed in your Experiment Directory:
     ----------------------------------------------------------------------
     /discover/nobackup/jkouatch/GEOS_OCEAN/yuri-s2s3-unstable-SLES12/GEOSodas/Linux/bin/GEOSgcm.x
 
 
     You must now copy your Initial Conditions into: 
     ----------------------------------------------- 
     /gpfsm/dnb32/jkouatch/GEOS_OCEAN/yuri-s2s3-unstable-SLES12/test_odas

     Copying Sandbox Source Code (including non-committed files) into /gpfsm/dnb32/jkouatch/GEOS_OCEAN/yuri-s2s3-unstable-SLES12/test_odas/src/GEOSagcm/src ...
 
     Enter CVS Tag to COMMIT this Experiment (Default: test_odas__jkouatch)
     Enter q or quit to QUIT or SKIP the CVS COMMIT:
          q


## Running the Code

