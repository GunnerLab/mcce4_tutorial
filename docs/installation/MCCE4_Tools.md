---
title: MCCE4-Tools
parent: Installation
nav_order: 3
permalink: /docs/installation/MCCE4_Tools
layout: default
---

# MCCE4-Tools Installation

  * Tools for processing MCCE4 simulation outputs.
  * A concise description is provided in the file `tools.info` in `MCCE4-Tools/mcce4_tools`.
  * Of note: The tool `glossary` provide a quick way to find out the actual key/values pertaining to a step. Usage: `glossary Step1`, `glossary Step2`, etc.

## Installation Steps:
  1. Navigate to a directory of your choice (referred to as 'clone_dir').
  2. Clone this repo, MCCE4-Tools & cd into it (copy and paste this command into your terminal):
  ```bash
   git clone https://github.com/GunnerLab/MCCE4-Tools.git; cd MCCE4-Tools;
  ```
  3. Add the clone's path to your `.bashrc` (`.bash_profile`) file, save it, then "dot" or "source" the file. Change 'clone_dir' in the pathname below to your current directory, which you may find using the bash command ```pwd```
  ```bash
   export PATH="clone_dir/MCCE4-Tools/mcce4_tools:$PATH"
  ```
  4. If all went well, all the command line tools are discoverable (but _not runable yet_). You can verify their location by running the `which` command, e.g.:
  ```bash 
   which getpdb
  ```
  5. To _run_ the tools:
  Ceate a conda environment with the provided `MCCE4-Tools/mct4.yml` file. Choose one of these two options to create the environment:
    * __Option 1__: Update your existing 'mc4' environment (which you have already created if you have installed MCCE4-Alpha):
    ```
     conda env update -n mc4 -f mct4.yml
    ```

    * __Option 2__: Create a NEW environment named 'mc4'. You do not have an 'mc4' environment if it's not listed in the output of the command `conda env list`.
    ```bash
     conda env create -f mct4.yml
    ```

  6. Test a tool
    * Activate your environment, e.g. `conda activate mc4`
    * Type `getpdb` and press Enter: the cli usage should display

  ## __NOTES__:
    * Although pymol is necessary for certain tools, it is not included in `mct4.yml` due to licensing; installation details for PyMOL 3.1 (Version 3.1.6.1) is [here](https://www.pymol.org/)


🎉 Congrats! Now that you've installed both __MCCE4-Alpha__ and 🛠️__MCCE4-Tools__ , please proceed to the [Quick Start Tutorial](https://gunnerlab.github.io/mcce4_tutorial/docs/tests/). Exercises 4 and 5 requires installation of  __MCCE4-Tools__.
