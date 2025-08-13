---
title: Setting the Stage
parent: Installation
nav_order: 1
permalink: /docs/installation/setting-the-stage/
---
# Setting the Stage

## Installation Option A: Quick Installation with Scripts
### Scripts:
These scripts automate many steps & download a generic NGPB image:
  * `MCCE_bin/quick_install.sh` (Linux, bash shell)
  * `MCCE_bin/quick_install_zsh.sh` (MacOS)

### 1. Clone the repository to a desired place on your computer (referred to as "clone_dir"):
```
 git clone https://github.com/GunnerLab/MCCE4-Alpha.git
```

### 2. Run the "Quick Install" script:
#### i. Go to your MCCE4-Alpha clone:
```
 cd ~/clone_dir/MCCE4-Alpha
```
#### ii. Run the approriate script:
* On MacOS, run:
```
 sh ./MCCE_bin/quick_install_zsh.sh
```

* On Linux, run:
```
 bash ./MCCE_bin/quick_install.sh
```

  Note: Creating 'install.log' is not required but is recommended as you could copy its contents if your created an "installation issue", which could help us fix an unexpected problem.

### 3. Follow the instructions displayed by the script to test your installation.
---

## Installation Option B: Installation with NGPB Optimized Image Creation:

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
  The command should return \your [clone_dir\]/MCCE4-Alpha/MCCE_bin/p_info


### 2. Compile Executables 
MCCE4 contains C and C++ libraries that must be compiled prior to use. Two compilations are required: for the mcce executable and for NGPB, the default PBE solver.  
  * NOTE: We also provide the DelPhi PBE solver executable file (`delphi`), without guarantying its runnability on all systems.

  * To compile mcce and ngpb:
    - Ensure you have sudo access as it is necessary for the installation of the NGPB container (~15min+)
    - `cd` into your MCCE4-Alpha clone directory
    - Run these commands:
      1. First and fast compilation command:
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
  
  * NOTE: To use the Openeye Zap solver, see "Zap Installation" below.

### 3. Test Installation
  * Create and activate a conda environment using MCCE4-Alpha environment file `mca.yml`. Choose either Command 1 or 2 below to create the environment:
    1. Command 1: To use the default environment name of 'mca':
       ```
        conda env create -f mca.yml
       ```
    2. Command 2: If you want something else, e.g. 'new_env' to be the environment name instead of 'mca':
       ```
        conda env create -f mca.yml -n new_env
       ```

  * Activate the mca env:
    ```
     conda activate mca
    ```

  * Check that a tool is functional; Its usage message should display:
    ```
     p_info
    ```
  * Display a command's help, e.g:
    ```
     step1.py -h
    ```

