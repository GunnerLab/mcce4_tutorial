---
title: MCCE4-Alpha Automated Installation
parent: Installation
nav_order: 1
permalink: /docs/installation/MCCE4_Alpha_auto/
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
Script `./MCCE_bin/quick_install.sh` automates the conda environment creation, export the clone references to the PATH variable in your `.bashrc` file, and download a generic NGPB image. Run:
```
bash ./MCCE_bin/quick_install.sh
```
   
The quick installation is completed by following the instructions displayed on the screen.

## 3. Installation Test 1: Is the generic image of NGPB running?
We test that **NextGenPB** is correctly set up by running a basic electrostatic potential calculation using a real protein input. 

1. Prepare the Inputs:

   Enter the test directory in your MCCE4-Alpha clone:
   ```bash
   CLONE=$(dirname $(dirname "$(readlink -f "$(which mcce)")")); echo "$CLONE"
   cd $CLONE/ngpb_test
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

2. Run NGPB:

   Run the simulation using Apptainer:
   ```bash
   apptainer exec --pwd /App --bind .:/App ../bin/NextGenPB_MCCE4.sif ngpb --prmfile options.prm
   ```

   This command runs NextGenPB inside the Apptainer container, binding your current directory (`.) to `/App` within the container.

3. Output and Results:
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
   ...
   ```

   These outputs confirm that NextGenPB is functioning correctly and that your configuration is valid.

   The timing report and energy terms provide a quick verification of the solver’s performance.

__TODO:__ Indicate what to do if NGPB does not run: post issue or do the compiled installation?

## 4. Installation Test 2
Activate a dedicated environment. Choose either Option 1 or 2 below to create the environment:
  1. Option 1: To use the conda environment created by the quick install script:
  ```
   conda activate mc4
  ```

  2. Option 2: Alternate way with pyenv (conda is not abolutely necessary):
  ```
   pyenv virtualenv 3.10 mc4
   pyenv activate mc4
   pip install -r ../requirements.txt
  ```

  3. Check that a tool is functional; Its usage message should display:
  ```
    p_info
    p_info -h
  ```

---

✅ Great! You have successfully installed and ready to run simulations now with __MCCE4-Alpha__!

➡️ Now, please proceed to the [MCCE4-Tools Installation](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/MCCE4_Tools) page to install our companion repository, __MCCE4-Tools__, which provides post-simulation analysis utilities -> no compilation of exectables required, just a simple repository clone and PATH setup.

{: .note }
To access the __MCCE4-Tools__ Github codebase repository please click here! [🧰 __MCCE4-Tools GitHub__](https://github.com/GunnerLab/MCCE4-Tools){: .btn .btn-blue }

