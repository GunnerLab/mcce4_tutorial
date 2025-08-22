---
title: Installation Options
parent: Installation
nav_order: 1
permalink: /docs/installation/installation_options/
layout: default
---

# Installation Options

## Installation Option A: Quick Installation with Scripts
### Scripts:
These scripts automate many steps & download a generic NGPB image:
  * `MCCE_bin/quick_install.sh` (Linux, bash shell)
  * `MCCE_bin/quick_install_zsh.sh` (MacOS)
#### Use the -h switch to get help on usage.

### 1. Clone the repository to a desired place on your computer (referred to as "clone_dir"):
```
 git clone https://github.com/GunnerLab/MCCE4-Alpha.git
```

### 2. Run the "Quick Install" script:
  i. Go to your MCCE4-Alpha clone:
  ```
  cd ~/clone_dir/MCCE4-Alpha
  ```

  ii. Run the approriate script:
    * On Linux/WSL, run:
    ```
    bash ./MCCE_bin/quick_install.sh
    ```

    * On MacOS, run:
    ```
    sh ./MCCE_bin/quick_install_zsh.sh
    ```

  Note: Creating 'install.log' is not required but is recommended as you could copy its contents if your created an "installation issue", which could help us fix an unexpected problem.

### 3. Follow the instructions displayed by the script to test your installation.
---

## Installation Option B: Installation with Creation of a NGPB Image Optimized for your System:

### 1. Clone the repository to a desired place on your computer (referred to as "clone_dir"):
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

### 2. Compile Executables 
MCCE4 contains C and C++ libraries that must be compiled prior to use. The first compilation command will create two executable files: `mcce` simulation executable and the legacy `delphi` PBE solver.  
  * Warning: We cannot guaranty that the DelPhi PBE solver executable file (`delphi`), will run on your system. This is one of the reasons NextGenPDB is now MCCE4 default PBE solver.

  * Ensure you have sudo access as it is necessary for the installation of the NGPB container (~15min+).
  * `cd` into your MCCE4-Alpha clone directory:
    ```
     cd ~/clone_dir/MCCE4-Alpha
    ```
  * Then run these commands:

    1. First and fast compilation command to compile mcce (and legacy Delphi):
    ```
      make clean
    ```

    2. Second and slow compilation (& container setup) command:
      - IMPORTANT: In case this is not the first time the command is run, make sure to delete its target file, i.e.:
        ```
          rm bin/NextGenPB_MCCE4.sif
        ```
      The screen output of this long compilation is extensive and not recoverable if not directed to a file, so there are two way to run the command:
      - Run `make` command, without logging:
        ```
          make
        ```
      - Run `make` command, with redirection to a log file:
        ```
          make > make.log 2>&1
        ```
  
  * NOTE: To use the Openeye Zap solver, see the "PBE Solvers" section.

### 3. Test Installation
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
