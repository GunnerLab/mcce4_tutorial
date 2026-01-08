---
title: Under the hood - run_mcce4
parent: guide
nav_order: 10
permalink: /docs/guide/Under_the_hood/
layout: default
---

Let's take a look at what is actually happening when you use "run_mcce4"!

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

