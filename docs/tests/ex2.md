---
title: 2 - pKₐ Calculation
parent: Quick Start Tutorial
nav_order: 2
layout: default
permalink: /docs/tests/ex2/
---

# Exercise #2: pKₐ Calculation (pH titration + pKₐ fitting)

In this exercise, we will run our first real protien file using **MCCE4-Alpha**!

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

{: .important }
> **We strongly recommend** to run `p_info` to inspect an unfamiliar PDB file and verify if it is compatible with MCCE4.
> ```bash
> p_info 4lzt.pdb > p_info.log
> ```

## Step 2 – Run `run_mcce4`

The easiest way to run a mcce4 simulation is with `run_mcce4`. 
It runs through the first four steps and calculates pKas for each residue of the PDB file, saving them to "pK.out" upon successful completion of the fourth step.

```bash
run_mcce4 4lzt.pdb
```

The occupancy table is in file `fort.38`. The net charge is in file `sum_crg.out`. The pKₐs are in file `pK.out`

## Step3 - Interpret Results 

The pKₐ report is in file pK.out.

```bash
(head -n 1 pK.out && tail -n +2 pK.out | sort -k1.1,1.14) | cut -c1-45
```

```
 pH             pKa/Em  n(slope) 1000*chi2
ARG+A0005_       12.839     0.887     0.079
ARG+A0014_       13.576     0.951     0.017
ARG+A0021_       13.333     0.762     0.286
ARG+A0045_       12.880     0.872     0.880
ARG+A0061_       13.879     0.818     0.003
ARG+A0068_        >14.0
ARG+A0073_       12.951     0.890     0.247
ARG+A0112_       13.099     0.905     0.025
ARG+A0114_       13.279     0.957     0.014
ARG+A0125_       13.155     0.891     0.079
ARG+A0128_       13.548     0.921     0.005
ASP-A0018_        2.264     0.915     0.033
ASP-A0048_        1.468     0.980     0.080
ASP-A0052_        2.389     0.987     0.209
ASP-A0066_        2.849     0.938     0.011
ASP-A0087_        2.105     0.981     0.013
ASP-A0101_        4.227     0.995     0.022
ASP-A0119_        3.684     0.995     0.087
CTR-A0129_        2.586     0.893     0.059
GLU-A0007_        3.319     0.961     0.004
GLU-A0035_        5.569     0.987     0.069
HIS+A0015_        6.488     1.010     0.012
LYS+A0001_        9.554     0.997     0.036
LYS+A0013_       11.194     0.958     0.033
LYS+A0033_       10.671     0.959     0.037
LYS+A0096_       10.758     0.786     0.399
LYS+A0097_       10.863     0.880     0.030
LYS+A0116_        9.544     0.953     0.087
NTR+A0001_        7.418     0.995     0.036
TYR-A0020_       13.615     0.592     0.249
TYR-A0023_       10.679     0.881     0.025
TYR-A0053_        >14.0
```

Other files are created by MCCE in the process of creating "pK.out". 
Learn about [these output files here.](https://gunnerlab.github.io/mcce4_tutorial/docs/tests/ex2) 
Learn more about [the four individual steps that comprise run_mcce4 here.](https://mccewiki.levich.net/books/mcce-tutorial-4lzt/page/calculate-pkas-of-lysozyme-mcce-steps-1-4)


TO DO: Provide answers where missing; Gather in a FAQ section, maybe?

[How do I customize a run?](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/submit_shell)

[How do I run MCCE on multiple PDB files at once?](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/p_batch)

[How do I know if the run processed correctly?](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/output_files)

[How do I interpret MCCE's Output Files?](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/interpret_output)
