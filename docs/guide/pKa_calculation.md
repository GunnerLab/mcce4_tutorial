---
title: pKa Calculation
parent: Guide
nav_order: 2
permalink: /docs/guide/pKa_calculation/
layout: default
---

# Calculate pKas of Residues in Lysozyme

## Background

__Lysozyme__ is a small enzyme that dissolves bacterial cell walls, thus killing bacteria. It was discovered as the first antibiotic to inhibit bacterial growth in food — __before penicillin__.

__Structure:__ [Crystal structure of hen egg white lysozyme (PDB ID: 4LZT)](https://www.rcsb.org/structure/4LZT)

The experimental pKₐ values of all lysozyme residues are provided at the bottom of this tutorial.  
Here, we will focus on __two residues in the active site__ with __perturbed pKₐ values__:
- __GLU 35__ — acts as a proton donor.
- __ASP 52__ — stabilizes the charged intermediate.

For lysozyme to attack the glucose molecule of the substrate:  
- __GLU 35__ needs a __high pKₐ__ to remain protonated and donate a proton to the glycosidic oxygen.
- __ASP 52__ needs a __low pKₐ__ to remain deprotonated and stabilize the reaction intermediate.

>__Reference:__ [Jens Erik Nielsen and J. Andrew McCammon, *Protein Sci.* __2003__ Sep; 12(9): 1894–1901](https://doi.org/10.1110/ps.03114903)

---
## Prepare the Calculation
MCCE expects to run in a **single directory** containing only one calculation, with four sequential steps. We recommend creating a new directory for each calculation that you run. 
```
 mkdir pka_test
 cd pka_test
```
Download the PDB file for 4LZT:
```
 getpdb 4lzt
```
If this is your first time running this protein, we reccomend running **p_info**
```
p_info 4lzt.pdb
```

---
## Run the calculation 

Since we are using the base parameters for this calculation, we can use the easiest command to run through steps 1 - 4.
- Adding "nohup" before the command and "&" after allows the calculations to run uninterrupted in the background of the terminal.

```
nohup run_mcce4 4lzt.pdb &
```
The calculation takes a few minutes. “nohup.out” will update as the calculation runs, take a look for confirmation of completed steps.

```
cat nohup.out
```

For more information on what’s happening under the hood of “run_mcce4” [click here](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/Under_the_hood/)! 

## Results
The pKa report is in file pK.out, to see this entire file: 

```
 cat pK.out
```
For a simple view of the pK.out file, you can sort it alphabetically and focus on the first four columns. 
```
sort pK.out | cut -c 1-43
```
From the result, GLU 35 (pKa = 5.13) has a higher pKa than ASP 52 (pKa = 3.33).

To analyze the ionization energy of an ionizable residue at the mid point pH=5.13 with pairwise cutoff 0.1:
```
 mfe.py ASP-A0052_ -c 0.1
```

To analyze the ionization energy of this residue pH 7 with pairwise cutoff 0.1:
```
 mfe.py ASP-A0052_  -p 7 -c 0.1
```

A more detailed explanation of mfe.py program can be found here [TODO: Add link]

## Benchmark pKas for Lysozyme
There are 20 experimentally measured pKas in hen white lysozyme.

- [Kuramitsu, S. and K. Hamaguchi, J Biochem (Tokyo), 1980. 87(4): p. 1215-9](https://www.jstage.jst.go.jp/article/biochemistry1922/87/3/87_3_771/_article/-char/ja/)
- [Takahashi, T., H. Nakamura, and A. Wada, Biopolymers, 1992. 32: p. 897-909](https://doi.org/10.1002/bip.360320802)

These pKa values have been used to benchmark MCCE and other programs that calculate pKas. For example:
- [Sham, Y. Y., I. Muegge, and A. Warshel. 1999. Simulating proton trans- locations in proteins: probing proton transfer pathways in the Rhodobacter sphaeroides reaction center. Proteins. 36:484–500](https://pubmed.ncbi.nlm.nih.gov/10450091/)
- [You, T. J., and D. Bashford. 1995. Conformation and hydrogen ion titration of proteins: a continuum electrostatic model with conformational flexi- bility. Biophys. J. 69:1721–1733](https://pmc.ncbi.nlm.nih.gov/articles/PMC1236406/)
- [Antosiewicz, J., J. A. McCammon, and M. K. Gilson. 1996. The determi- nants of pKa’s in proteins. Biochemistry. 35:7819–7833](https://pubmed.ncbi.nlm.nih.gov/8672483/)

### pKas of residues in Lysozyme

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


## What if
If you want to run calculations with different calculations such as dielectric constant or you want to
- 	use an alternate MCCE executable,
-  set the protein dielectric constant to 8, and
-  retain explicit water molecules
you could check this in [Customizing Runs](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/submit_shell/)

### If you want the run with explicit waters
In case of the structure contains crystallographic waters and you want to retain them in the calculation, include the appropriate option so the default is already having water in Step 1: 
[Explicit waters](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/Under_the_hood/)

- Note: For lysozyme 4LZT, this option is not required because the structure is dry
  
