---
title: Installation Steps
parent: Installation
nav_order: 1
permalink: /docs/installation/installation_options/
layout: default
---

MCCE4 default PBE solver is [NextGenPB (NGPB)](https://github.com/concept-lab/NextGenPB).

There are two ways you can install MCCE4-Alpha, which differ on whether a script is used: 
 * Option A: 
   - Keep the provided `mcce` and `delphi` (alternate PBE solver) executables compiled for Linux OS;
   - Use the semi-automated setup using provided script that download a generic NGPB image.
 * Option B: Manual setup that includes: the compilation of `mcce` with or without that of `delphi`, and the creation of a NGPB image optimized for your platform.

# Option A: Quick Installation with Scripts
## 1. Clone the repository to a desired place on your computer (referred to as "clone_dir"):
  * Git clone MCCE4-Alpha to a desired place on your computer (copy & pasted this command and press Enter):
  ```
   git clone https://github.com/GunnerLab/MCCE4-Alpha.git; cd MCCE4-Alpha;
  ```

## 2. Run the quick installation script

  * Script: `./MCCE_bin/quick_install.sh`
This script automates the conda environment creation, shows how to setup references in your `.bashrc` file, and download a generic NGPB image. Run:
```
 bash ./MCCE_bin/quick_install.sh
```
The quick installation is completed by following the instructions displayed on the screen.


# Option B: Installation with Executable Compilation and Creation of a NGPB Image Optimized for your System:
__Note:__ This option is necessary if you cannot run a simulation with an installation made with Option A.
## 1. Clone the repository to a desired place on your computer (referred to as "clone_dir"):
  * Git clone MCCE4-Alpha to a desired place on your computer & cd into it:
  ```
   git clone https://github.com/GunnerLab/MCCE4-Alpha.git; cd MCCE4-Alpha;
  ```
 
  * Add the clone's bin paths to your `.bashrc` file then save it.
  ```
   export PATH="clone_dir/MCCE4-Alpha/bin:$PATH"
   export PATH="clone_dir/MCCE4-Alpha/MCCE_bin:$PATH"
  ```

  * Then apply the changes to your PATH variable by sourcing your `.bashrc` file, depending on your system.

  * Check a tool's command correct path location (tools do not require compiling):
  ```
    which p_info
  ```
  The command should return ~/clone_dir/MCCE4-Alpha/MCCE_bin/p_info

## 2. Executables and NGPB Container Image Compilation
MCCE4 contains C and C++ libraries that must be compiled prior to use. These consist of two executable files and a container image for the PBE solver, NGPB
- `mcce`                  : Main simulation executable
- `delphi`                : Legacy PBE solver (support not guaranteed on all systems)
- `NextGenPB_MCCE4.sif`   : Container image for NGPB, the default MCCE4 PBE solver

Currently, the MCCE4-Alpha Repository provides both the mcce and delphi compiled executables for Linux OS but the NGPB container requires sudo access to compile. Therefore, we also provide a Linux OS–compiled NGPB container (NextGenPB_MCCE4_LinuxCompiled.sif) at the Dropbox link below. Please place this in your MCCE4/bin as NextGenPB_MCCE4.sif

👉 [Download NextGenPB_MCCE4_LinuxCompiled.sif](https://www.dropbox.com/scl/fi/fb2d2rrwwv3efkpshhlia/NextGenPB_MCCE4_LinuxCompiled.sif?rlkey=z8xl1cblp3t8vlw8jz3ft2gn1&e=1&st=2as1wv7z&dl=1)

**⚠️ Warning: We cannot guarantee that the DelPhi PBE solver executable (delphi) will run on your system. This is one of the reasons NextGenPB is now the default PBE solver in MCCE4.**

### Compile Executables and NGPB Container Image
If the provided executables and NGPB container do not work for your system, they must be compiled via the MakeFile.
To proceed with compiling, please do the following:

**⚠️ Warning: Ensure you have sudo access as it is necessary for the installation of the NGPB container (~15 min+)**.

1.  Clean up previous versions, if any:
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

## 3. Test NGPB run

We will verify that **NextGenPB** is correctly set up by running a basic electrostatic potential calculation using a real protein input.
{: .fs-6 }

---

### Step 1 – Prepare the Inputs

Enter the test directory in MCCE4 `PATH`:
```bash
cd $MCCE_HOME/test
```

Inside the `test/` , you will find a options.prm file and a .pqr file of a small protein.

Example `options.prm` file:

```
[input]
filetype = pqr
filename = 1CCM.pqr
[../]

[mesh]
mesh_shape = 0
perfil1    = 0.95
perfil2    = 0.2
scale      = 2.0
[../]

[model]
bc_type                       = 1          # Boundary condition type
molecular_dielectric_constant = 2          # Dielectric constant inside the molecule
solvent_dielectric_constant   = 80         # Dielectric constant of the solvent (e.g., water)
ionic_strength                = 0.145      # Ionic strength (mol/L)
T                             = 298.15     # Temperature in Kelvin
calc_energy                   = 2
calc_coulombic                = 1
[../]
```

### Step 2 – Run NGPB

Run the simulation using Apptainer:

```bash
apptainer exec --pwd /App --bind .:/App NextGenPB_MCCE4.sif ngpb --prmfile options.prm
```

This command runs NextGenPB inside the Apptainer container, binding your current directory (`.) to `/App` within the container.

---

### Step 3 – Output and Results

At the end of the execution, you will see a log similar to this:

```bash
================ [ Electrostatic Energy ] =================
  Net charge [e]:                                 7.327471962526033e-15
  Flux charge [e]:                                -4.859124220152702e-11
  Polarization energy [kT]:                       -384.6169807703798
  Direct ionic energy [kT]:                       -0.2516508616874018
  Coulombic energy [kT]:                          -10097.24155852403
  Sum of electrostatic energy contributions [kT]: -10482.11019015609
===========================================================
compute energy
Elapsed time : 141.198ms

Timing Report:
...
```

These outputs confirm that NextGenPB is functioning correctly and that your configuration is valid.

The timing report and energy terms provide a quick verification of the solver’s performance.



---
### __NOTE:__ To use the Openeye Zap solver, see the "PBE Solvers" section.

## 4. Test Installation
  * Create and activate a conda environment using MCCE4-Alpha environment file `mc4.yml`. Choose either Command 1 or 2 below to create the environment:
    1. Command 1: To use the default environment name of 'mc4':
    ```
     conda env create -f mc4.yml
     conda activate mc4
    ```
    2. Command 2: If you want something else, e.g. 'new_env' to be the environment name instead of 'mc4':
    ```
     conda env create -f mc4.yml -n new_env
     conda activate new_env
    ```
  * Alternate way with pyenv (conda is not abolutely necessary):
    ```
      pyenv virtualenv 3.10 mc4
      pyenv activate mc4
      pip install -r ../requirements.txt
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
