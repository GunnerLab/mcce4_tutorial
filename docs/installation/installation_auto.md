---
title: Automated Installation
parent: Installation
nav_order: 1
permalink: /docs/installation/installation_auto/
layout: default
---

# Installation Option A: Automated Installation

## Quick Installation with Scripts
   - Keep the provided `mcce` and `delphi` (alternate PBE solver) executables compiled for Linux OS;
   - Use the semi-automated setup using provided script that download a generic NGPB image.

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

## 3. Test NGPB run
We will verify that **NextGenPB** is correctly set up by running a basic electrostatic potential calculation using a real protein input. 

1. Step 1 – Prepare the Inputs:

   Enter the test directory in MCCE4-Alpha `PATH`:
   ```bash
   cd MCCE4-Alpha/ngpb_test
   ```

   Inside the `ngpb_test/` , you will find a options.prm file and a .pqr file of a small protein.
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

2. Step 2 – Run NGPB:

   Run the simulation using Apptainer:
   ```bash
   apptainer exec --pwd /App --bind .:/App ../bin/NextGenPB_MCCE4.sif ngpb --prmfile options.prm
   ```

   This command runs NextGenPB inside the Apptainer container, binding your current directory (`.) to `/App` within the container.

3. Step 3 – Output and Results:
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
    
    
