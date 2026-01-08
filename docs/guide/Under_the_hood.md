---
title: Under the hood - run_mcce4
parent: Guide
nav_order: 10
permalink: /docs/guide/Under_the_hood/
layout: default
---
## Under the hood - run_mcce4
Let's take a look at what is actually happening when you use **"run_mcce4"**!

run_mcce4 uses the following parameters: 
```
step1.py {input_pdb} -d 4 –-dry
step2.py -l 1 -d 4
step3.py -d 4
step4.py --xts -i 7 -n 1
```

### Step 1 - Convert PDB to MCCE PDB 
Checks for inconsistencies between the PDB file and MCCE topology files, converts PDB file into a suitable input PDB for MCCE 
```
step1.py {input_pdb} -d 4 –-dry
```
- -d 4: protein dielectric constant for PBE solvers; default: 4.0.
- --dry: Delete all water molecules; default: False.

### Step 2 – Make side chain conformers 
This step makes alternative side chain locations and ionization states 

```
step2.py -l 1 -d 4
```
- -l 1: conformer level 1: quick, 2: medium, 3: comprehensive; default: 1.

### Step3.py – Make energy table 
Calculates conformers self-energy and pairwise interaction table. 

```
step3.py -d 4
```

### Step4.py – Simulate a titration with Monte Carlo sampling 
This step simulates a titration and writes out the conformation and ionization states of each side chain at various conditions. 
```
step4.py --xts -i 7 -n 1
```
- --xts: Enable entropy correction, default is false
- -i 7: Initial pH/Eh of titration; default : 0.0
- -n 1: Number of steps of titration; default 15 
---
In order to see the all of the available parameters for each step you can run "-h" after each command

   For example:
  ```
  step1.py -h
  ```
would output the following 

```
step1.py -h
usage: step1.py [-h] [--norun] [--noter] [-e /path/to/mcce] [-u Key=Value] [-d epsilon] [-load_runprm prm_file] [--dry] pdb

Run mcce step 1, premcce to format PDB file to MCCE PDB format.

positional arguments:
  pdb

options:
  -h, --help            show this help message and exit
  --norun               Create run.prm but do not run step 1; default: False.
  --noter               Do not label terminal residues (for making ftpl); default: False.
  -e /path/to/mcce      mcce executable location; default: mcce.
  -u Key=Value          User customized variables; default: .
  -d epsilon            protein dielectric constant for PBE solvers; default: 4.0.
  -load_runprm prm_file
                        Load additional run.prm file, overwrite default values.
  --dry                 Delete all water molecules; default: False.
```

 To see more on how to customize these steps see [Customizing runs](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/submit_shell/) ! 
