---
title: pKa Calculation
parent: Guide
nav_order: 1
permalink: /docs/guide/pKa_calculation/
layout: default
---

# Calculate pKas of Lysozyme

## Background

**Lysozyme** is a small enzyme that dissolves bacterial cell walls, thus killing bacteria. It was discovered as the first antibiotic to inhibit bacterial growth in food—**before penicillin**.

**Structure:** [Crystal structure of hen egg white lysozyme (PDB ID: 4LZT)](https://www.rcsb.org/structure/4LZT)

The experimental pKₐ values of all lysozyme residues are provided at the bottom of this tutorial.  
Here, we will focus on **two residues in the active site** with **perturbed pKₐ values**:

- **GLU 35** — acts as a proton donor.
- **ASP 52** — stabilizes the charged intermediate.

For lysozyme to attack the glucose molecule of the substrate:  

- **GLU 35** needs a **high pKₐ** to remain protonated and donate a proton to the glycosidic oxygen.
- **ASP 52** needs a **low pKₐ** to remain deprotonated and stabilize the reaction intermediate.

> **Reference:** [Jens Erik Nielsen and J. Andrew McCammon, *Protein Sci.* **2003** Sep; 12(9): 1894–1901](https://doi.org/10.1110/ps.03114903)

---

## Prepare the Calculation

After MCCE is installed and the `PATH` environment variable is configured (see installation guide), prepare a working directory for the calculation:

```
mkdir 4lzt
cd 4lzt
```

Download the PDB file for 4LZT:
```
getpdb 4lzt
```

Output should look like:
```
Saving as 4LZT.pdb ...
Download completed.
```

### Explicit Waters
We suggest removing explicit waters from the input pdb file. The protonation states and pKas with or without are similar, but calculations are much faster without explicit waters.
```
grep -v HOH 4LZT.pdb > 4LZT_noHOH.pdb
```
---

## Run the 4 Steps of MCCE
MCCE expects to run **in a single directory** containing only one calculation.
There are **4 sequential steps**. Each step’s output feeds into the next.
You can re-run later steps without starting over.

For each step command, you may use the help information to see parameter options:
```
step#.py -h
```

### Step 1 — Convert PDB to MCCE PDB
Checks for inconsistencies between the PDB file and MCCE topology files:
- Adds missing sidechain atoms.
- Flags unknown residues.
- Removes waters with >5% surface exposure.
- Separates chain termini as NTR and CTR (treated as protonatable).

```
step1.py 4LZT.pdb
```
This command generates step1_out.pdb which is required for step 2.


### Step 2. Make side chain conformers¶
This step makes alternative side chain locations and ionization states.

```
step2.py
```
This command generates step2_out.pdb which is required of step 3.


### Step 3. Make energy table¶
This step calculates conformer self energy and pairwise interaction table.

```
step3.py
```
This command generates opp files under energies/ folder and file head3.lst which are required of step 4.


### Step 4. Simulate a titration with Monte Carlo sampling¶
This step simulates a titration and writes out the conformation and ionization states of each side chain at various conditions.

```
step4.py --xts
```

- fort.38. is the primary output file. It gives the average occupancy of each conformer.
- sum_crg.out give the net charge on all residues. Residues where all conformers have a zero charge are not reported in this file.
- pK.out calculates the best single pKa for each residue given the change in charge with pH in sum_crg.out.


### Results
The pKa report is in file pK.out.

```
cat pK.out
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

A more detailed explanation of mfe.py program can be found here [To be linked]

### Benchmark pKas for Lysozyme
There are 20 experimentally measured pKas in hen white lysozyme.

- [Bartik, K., C. Redfield, and C.M. Dobson, Biophys J, 1994. 66(4): p. 1180-4](10.1016/S0006-3495(94)80900-2)
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

