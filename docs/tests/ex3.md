---
title: Exercise Three
parent: Test Cases
nav_order: 4
layout: default
permalink: /docs/tests/ex3/
---

# Exercise #3: Em Calculation (redox titration over a defined Em range)

In this exercise, we will run our first real protien file using **MCCE4-Alpha**!

---

## Step 1 – Prepare the Calculation

First, enter the working directory for this exercise:

```bash
cd ~/mcce4_tests/ex3
```

Download the PDB file for 1AKK:
```bash
getpdb 1akk
```

A successful download should display the following message:
```bash
 [ INFO ] Download completed: 1akk.pdb
```

{: .note }
> **Optional Step:** To learn more about an unfamiliar PDB file, run `p_info`.
> ```bash
> p_info 1akk.pdb > p_info.log
> ```

## Step 2 – Run `run_mcce4`

The easiest way to run a mcce4 simulation is with `run_mcce4`. 
It runs through the first four steps and calculates pKas for each residue of the PDB file, saving them to "pK.out" upon successful completion of the fourth step.

```bash
run_mcce4 1akk.pdb -type eh -initial 0 -interval 60 -n 15
```

The occupancy table is in file fort.38.
The net charge is in file sum_crg.out
Eh is in file pK.out
