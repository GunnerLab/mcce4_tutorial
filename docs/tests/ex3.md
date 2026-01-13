---
title: 3 - Eₘ Calculation
parent: Quick Start Tutorial
nav_order: 3
layout: default
permalink: /docs/tests/ex3/
---

# Exercise #3: Eₘ Calculation (redox titration over a defined Eₕ range)

In this exercise, we will run our first real protien file using **MCCE4-Alpha**!

---

## 1. Prepare the Calculation

Enter the working directory for this exercise:
```bash
cd mcce_workflows
mkdir ex3; cd ex3
```

Download the PDB file for 1AKK:
```bash
getpdb 1akk
```

A successful download should display the following message:
```bash
 [ INFO ] Download completed: 1akk.pdb
```

{: .important }
> **We strongly recommend** to run `p_info` to inspect an unfamiliar PDB file and verify if it is compatible with MCCE4.
> ```bash
> p_info 4lzt.pdb > p_info.log
> ```

## 2. Perform Eₘ using `run_mcce4`

The easiest way to run a mcce4 simulation is with `run_mcce4`. 
It is preset to run a full simulation (ending with a titration) and return the pKas of ionizable residues into one of its output files called "pK.out" upon successful completion.

```bash
run_mcce4 1akk.pdb -type eh -initial 0 -interval 60 -n 15
```

The occupancy table is in file ```fort.38```.
The net charge is in file ```sum_crg.out```.
Eh is in file ```pK.out```

## 3. Interpret Eₘ results

The pKa/Em report is in file pK.out.

```
cat pK.out
```

You will see the calculated Eₕ for heme is 247 mV

{: .note }
> **Optional Step:** To analyze the ionization energy of heme at the midpoint:
> ```
> mfe.py HEM+A0105_
> ```
>
> A more detailed explanation of mfe.py program can be found here [TODO: Add link]
