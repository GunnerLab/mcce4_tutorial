---
title: 2 - pKₐ Calculation
parent: Quick Start Tutorial
nav_order: 2
layout: default
permalink: /docs/tests/ex2/
---

# Exercise #2: pKₐ Calculation (pH titration + pKₐ fitting)
In this exercise, we will run your first **MCCE4** simulation to perform a pKₐ calculation using a real protein file!

---
## Background
__Lysozyme__ is a small enzyme that dissolves bacterial cell walls, thus killing bacteria. It was discovered as the first antibiotic to inhibit bacterial growth in food — __before penicillin__. We are using [PDB ID: 4LZT](https://www.rcsb.org/structure/4LZT)

The experimental pKₐ values of all lysozyme residues are provided at the bottom of this tutorial.  
Here, we will focus on __two residues in the active site__ with __perturbed pKₐ values__:
- __GLU 35__ — acts as a proton donor.
- __ASP 52__ — stabilizes the charged intermediate.

For lysozyme to attack the glucose molecule of the substrate:  
- __GLU 35__ needs a __high pKₐ__ to remain protonated and donate a proton to the glycosidic oxygen.
- __ASP 52__ needs a __low pKₐ__ to remain deprotonated and stabilize the reaction intermediate.

__Reference:__ [Jens Erik Nielsen and J. Andrew McCammon, *Protein Sci.* __2003__ Sep; 12(9): 1894–1901](https://doi.org/10.1110/ps.03114903)

---

## 0. Pre-requisite:
Ensure you have the conda environment for ```mc4``` activated.
```
conda activate mc4
```

## 1. Prepare the Calculation
Enter the working directory for this exercise:

```bash
cd mcce_workflows
mkdir ex2; cd ex2
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
> p_info 4lzt.pdb
> ```

## 2. Perform pKₐ calculation using `run_mcce4`
The easiest way to run a mcce4 simulation is with the `run_mcce4` script. 
It is preset to run a full simulation (ending with a titration) and return the pKas of ionizable residues into one of its output files called "pK.out" upon successful completion.

```bash
run_mcce4 4lzt.pdb
```
The occupancy table is in file `fort.38`. The net charge is in file `sum_crg.out`. The pKₐs are in file `pK.out`

See [Under the hood - MCCE4](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/Under_the_hood/) for more details on what's exactly happening when running this command! FIX LINK with new location

## 3. Interpret pKₐ results 
The pKₐ report is in file **pK.out**, which contains the calculated pKₐ values for titratable side chains.
- __pKa/Em__  
  pH of the pKa.
- __n (slope)__  
  Slope of titration curve (extrapolated from `fort.38`) and the Henderson-Hasselbalch equation.
- __1000×chi2__  
  1000 times the chi-squared value. Higher the number, the less accurate the result.

```bash
(head -n 1 pK.out && tail -n +2 pK.out | sort -k1.1,1.14) 
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
From the result we can see that GLU 35 (pKa = 5.6) has a higher pKa than ASP 52 (pKa = 2.4).

**So how do we calculate these pKa values?** 

Let's take a look at the two other output files produced:

1) **fort.38**: Is a table of the occupancy of each side-chain conformer over the course of the titration. From the titration curve of this lysine in 4lzt, the pKₐ can be determined by identifying the midpoint of the curve where the occupancy is 0.5. This is where the lysine is 50% ionized and 50% non-ionized. Projecting this midpoint onto the pH axis yields the pKₐ value.

<img width="500" height="648" alt="Screenshot 2026-01-16 at 10 56 39 AM" src="https://github.com/user-attachments/assets/6e5dd344-a54e-4855-a2b0-b5d7ff3f80cf" />

2) **sum_crg.out**: Reports the net charge of each residue as a function of pH. Again, drawing a line from the midpoint of the titration curve to the pH axis, one can determine the pKa of the ionized residue.
    
<img width="500" height="576" alt="Screenshot 2026-01-16 at 10 56 05 AM" src="https://github.com/user-attachments/assets/7c7e59eb-74ee-4856-b6f1-1984fd7ab010" />

---

To analyze the ionization energy of an ionizable residue at it's mid point with pairwise cutoff 0.1:
```
 mfe.py ASP-A0052_ -c 0.1
```
To analyze the ionization energy of this residue pH 7 with pairwise cutoff 0.1:
```
 mfe.py ASP-A0052_  -p 7 -c 0.1
```
> ### To learn more about how pKa's shift, see the [mean field energy tutorial](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/mfe_tutorial/)!

---

## Benchmark pKas for Lysozyme
There are 20 experimentally measured pKas in hen white lysozyme.

- [Kuramitsu, S. and K. Hamaguchi, J Biochem (Tokyo), 1980. 87(4): p. 1215-9](https://www.jstage.jst.go.jp/article/biochemistry1922/87/3/87_3_771/_article/-char/ja/)
- [Takahashi, T., H. Nakamura, and A. Wada, Biopolymers, 1992. 32: p. 897-909](https://doi.org/10.1002/bip.360320802)

These pKa values have been used to benchmark MCCE and other programs that calculate pKas. For example:
- [Sham, Y. Y., I. Muegge, and A. Warshel. 1999. Simulating proton trans- locations in proteins: probing proton transfer pathways in the Rhodobacter sphaeroides reaction center. Proteins. 36:484–500](https://pubmed.ncbi.nlm.nih.gov/10450091/)
- [You, T. J., and D. Bashford. 1995. Conformation and hydrogen ion titration of proteins: a continuum electrostatic model with conformational flexi- bility. Biophys. J. 69:1721–1733](https://pmc.ncbi.nlm.nih.gov/articles/PMC1236406/)
- [Antosiewicz, J., J. A. McCammon, and M. K. Gilson. 1996. The determi- nants of pKa’s in proteins. Biochemistry. 35:7819–7833](https://pubmed.ncbi.nlm.nih.gov/8672483/)
  
### Experimental pKas of residues in Lysozyme

| Residue  | pKₐ  |
|----------|------|
| LYS 1    | 10.8 |
| GLU 7    |  2.85|
| LYS 13   | 10.5 |
| HIS 15   |  5.36|
| ASP 18   |  2.66|
| TYR 20   | 10.3 |
| TYR 23   |  9.8 |
| LYS 33   | 10.4 |
| GLU 35   |  6.2 |
| ASP 48   |  1.6 |
| ASP 52   |  3.68|
| ASP 66   |  0.9 |
| ASP 87   |  2.07|
| LYS 96   | 10.8 |
| LYS 97   | 10.3 |
| ASP 101  |  4.08|
| LYS 116  | 10.2 |
| ASP 119  |  3.2 |
| CTR      |  2.75|

---
## What if
If you want to run calculations with different parameters such as: 
- 	using an alternate MCCE executable
-  setting the protein dielectric constant to 8
-  retaining explicit water molecules
you could check this out in [Customizing Runs](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/submit_shell/)!


