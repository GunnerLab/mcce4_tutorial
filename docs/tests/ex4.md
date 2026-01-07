---
title: Exercise Four
parent: Test Cases
nav_order: 5
layout: default
permalink: /docs/tests/ex4/
---

# Exercise #4: Single-pH 4LZT microstate (MS) analysis

In this exercise, we will a microstate analysis on a protien file using **MCCE4-Alpha**!

---

## Step 1 – Prepare the Calculation

First, enter the working directory for this exercise:

```bash
cd ~/mcce4_tests/ex2
```

Download the PDB file for 4LZT:
```bash
 getpdb 4lzt
```

A successful download should display the following message:
```bash
 [ INFO ] Download completed: 4lzt.pdb
```

{: .note }
> **Optional Step:** To learn more about an unfamiliar PDB file, run `p_info`.
> ```bash
> p_info 4lzt.pdb > p_info.log
> ```


## Step 2 – Run `run_mcce4`

The easiest way to run a mcce4 simulation is with `run_mcce4`. 
It runs through the first four steps and calculates pKas for each residue of the PDB file, saving them to "pK.out" upon successful completion of the fourth step.

```bash
run_mcce4 4lzt.pdb -initial 7 -interval 1 -n 1 --ms
```

## Step 3 – Run `ms_protonation`

