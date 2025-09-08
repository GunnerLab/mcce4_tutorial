---
title: Installation Steps
parent: Installation
nav_order: 1
permalink: /docs/installation/installation_options/
layout: default
---

# Installation with Creation of a NGPB Image Optimized for your System:

## 1. Clone the repository to a desired place on your computer (referred to as "clone_dir"):
  * Git clone MCCE4-Alpha to a desired place on your computer:
  ```
   git clone https://github.com/GunnerLab/MCCE4-Alpha.git
  ```
 
  * Add the clone's bin paths to your `.bashrc` (`.bash_profile`) file then save it.
  ```
   export PATH="clone_dir/MCCE4-Alpha/bin:$PATH"
   export PATH="clone_dir/MCCE4-Alpha/MCCE_bin:$PATH"
  ```

  * Then apply the changes to your PATH variable by sourcing your `.bashrc` (`.bash_profile`) file, depending on your system.

  * Check a tool's command correct path location (tools do not require compiling):
  ```
    which p_info
  ```
  The command should return ~/clone_dir/MCCE4-Alpha/MCCE_bin/p_info




## 2. Executables and NGPB Container Image
MCCE4 contains C and C++ libraries that must be compiled prior to use. These consist of two executable files and a container image for the PBE solver, NGPB
- `mcce`                  : Main simulation executable
- `delphi`                : Legacy PBE solver (support not guaranteed on all systems)
- `NextGenPB_MCCE4.sif`   : Container image for NGPB, the default MCCE4 PBE solver

Currently, the MCCE4-Alpha Repository provides both the mcce and delphi compiled executables for Linux OS but the NGPB container requires sudo access to compile. Therefore, we also provide a Linux OS–compiled NGPB container (NextGenPB_MCCE4_LinuxCompiled.sif) at the Dropbox link below. Please place this in your MCCE4/bin as NextGenPB_MCCE4.sif

👉 [Download NextGenPB_MCCE4_LinuxCompiled.sif](https://www.dropbox.com/scl/fi/fb2d2rrwwv3efkpshhlia/NextGenPB_MCCE4_LinuxCompiled.sif?rlkey=z8xl1cblp3t8vlw8jz3ft2gn1&e=1&st=2as1wv7z&dl=1)

**⚠️ Warning: We cannot guarantee that the DelPhi PBE solver executable (delphi) will run on your system. This is one of the reasons NextGenPB is now the default PBE solver in MCCE4.**

### Compile Executables and NGPDB Container Image
If the provided executables and NGPB container do not work for your system, they must be compiled via the MakeFile.
To proceed with compiling, please do the following:

**⚠️ Warning: Ensure you have sudo access as it is necessary for the installation of the NGPB container (~15 min+)**.

1. `cd` into your MCCE4-Alpha clone directory:
   ```
   cd ~/clone_dir/MCCE4-Alpha
   ```
  
2. Clean up previous versions, if any:
   ```
   make clean                  # remove bin/mcce and bin/delphi if present
   rm bin/NextGenPB_MCCE4.sif  # remove existing container image
   ```
  
3. Re-create the three objects: The screen output of this long compilation is extensive and not recoverable if not directed to a file, so there are
   two way to run the command:

   a. Run `make all` command, without logging:
   ```
   make all
   ```
   Or compile each individually:
   ```
   make mcce
   make delphi
   make ngpb
   ```

   b. Run `make all` command, with redirection to a log file:
   ```
     make all > make.log 2>&1
   ```
   Or redirect logs for each target individually:
   ```
   make mcce   > make_mcce.log   2>&1
   make delphi > make_delphi.log 2>&1
   make ngpb   > make_ngpb.log   2>&1
   ```

### __NOTE:__ To use the Openeye Zap solver, see the "PBE Solvers" section.

## 3. Test Installation
  * Create and activate a conda environment using MCCE4-Alpha environment file `mc4.yml`. Choose either Command 1 or 2 below to create the environment:
    1. Command 1: To use the default environment name of 'mc4':
    ```
     conda env create -f mc4.yml
    ```
    2. Command 2: If you want something else, e.g. 'new_env' to be the environment name instead of 'mc4':
    ```
     conda env create -f mc4.yml -n new_env
    ```

  * Activate the newly created environment:
    ```
     conda activate mc4  # or new_env
    ```

  * Check that a tool is functional; Its usage message should display:
    ```
     p_info
    ```

  * Display a command's help, e.g:
    ```
     p_info -h
     step1.py -h
    ```
