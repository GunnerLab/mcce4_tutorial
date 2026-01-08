---
title: Manual Installation
parent: Installation
nav_order: 2
permalink: /docs/installation/installation_manual/
layout: default
---

# Installation Option B: Manual Installation

## Executable Compilation and Creation of a NGPB Image Optimized for your System:
   - Compile `mcce` with or without that of `delphi`, and the creation of a NGPB image optimized for your platform.

{: .important }
> This option is necessary if you cannot run a simulation with an installation made with Option A: Automated Installation with scripts.

## ✅ Step 1. Clone the repository to a desired place on your computer (referred to as "clone_dir"):
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

---
## ✅ Step 2. Executables and NGPB Container Image Compilation
MCCE4 contains C and C++ libraries that must be compiled prior to use. These consist of two executable files and a container image for the PBE solver, NGPB
- `mcce`                  : Main simulation executable
- `delphi`                : Legacy PBE solver (support not guaranteed on all systems)
- `NextGenPB_MCCE4.sif`   : Container image for NGPB, the default MCCE4 PBE solver

{: .note }
> We cannot guarantee that the DelPhi PBE solver executable (delphi) will run on your system.
> For this reason, [NextGenPB (NGPB)](https://github.com/concept-lab/NextGenPB) is now the default PBE solver in MCCE4.

{: .highlight }
> Currently, the MCCE4-Alpha Repository provides both the mcce and delphi compiled executables for Linux OS but the NGPB container requires sudo access to compile. Therefore, we also provide a Linux OS–> compiled NGPB container (NextGenPB_MCCE4_LinuxCompiled.sif) at the Dropbox link below. 
>
> 👉 [Download NextGenPB_MCCE4_LinuxCompiled.sif](https://www.dropbox.com/scl/fi/n458dyugo86l073ps5csl/NextGenPB_MCCE4_LinuxCompiled.sif?rlkey=dh1fyvanjqz3twe68pud9xyks&st=oxd085pc&dl=1)
> 
> **Please place this in your MCCE4-Alpha/bin as NextGenPB_MCCE4.sif**

## Compile Executables and NGPB Container Image
If the provided executables and NGPB container do not work for your system, they must be compiled via the MakeFile.
To proceed with compiling, please do the following:

{: .important }
> Requires sudo access for the installation of the NGPB container (~15 min+).

1. Clean up previous versions, if any:
   ```
   make clean                  # remove bin/mcce and bin/delphi if present
   rm bin/NextGenPB_MCCE4.sif  # remove existing container image
   ```
  
2. Re-create the three objects: The screen output of this long compilation is extensive and not recoverable if not directed to a file, so there are
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

{: .note }
> To use the Openeye Zap solver, please see section [PBE Solvers](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/pbe_solvers).

---
## ✅ Step 3. Test NGPB run
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
## ✅ Step 4. Test Installation
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

✅ Great! You have succusffully installed and ready to run simulations now with __MCCE4-Alpha__!

➡️ Now, please proceed to [MCCE4-Tools](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/tools/) to also install our companion repository [MCCE4-Tools](https://github.com/GunnerLab/MCCE4-Tools) for post-simulation analysis tools.

