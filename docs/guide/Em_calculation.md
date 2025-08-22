---
title: Em Calculation
parent: Guide
nav_order: 3
permalink: /docs/guide/Em_calculation/
layout: default
---

# Calculate Em of heme in Cytochrome

## Background

__Cytochrome c__ is a small protein that transports electrons in mitochondria to facilitate the synthesis of ATP. Its redox potential plays an important role in its function. The regulation of the cytochrome c redox potential can be explained by __continuum electrostatic analysis__.

__Structure:__ [Cytochrome c from *E. caballus* (PDB ID:1AKK)](https://www.rcsb.org/structure/1AKK)
The experimental Em of Cytochrome c is __260 mV__.

> __Reference:__ [Junjun Mao, Karin Hauser, and M. R. Gunner, _How Cytochromes with Different Folds Control Heme Redox Potentials_, __Biochemistry__ 2003, 42(33), 9829–9840](https://pubmed.ncbi.nlm.nih.gov/12924932/)

---
## Prepare the Calculation
After MCCE is installed and your execution path is configured (see [Installation section](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/)), prepare a working directory for the calculation.  
MCCE generates intermediate and result files in the __current directory__, so it is best to create one dedicated directory for each structure.
```
 mkdir 1akk; cd 1akk
```

Then download pdb file 1AKK to the working directory:
```
 getpdb 1akk
 [ INFO ] Download completed: 1akk.pdb
```

## Run 4 steps of MCCE
## Step 1. Convert PDB file into MCCE PDB
This step checks for inconsistencies between PDB file and MCCE topology files. Missing sidechain atoms will be added back. Residues that are not in the MCCE lexicon will be flagged. Explicit waters with greater than 5% surface exposure will be stripped off.

The heme in cytochrome C has two ligands HIS18 and MET80. They behave differently than HIS and MET so we have to rename them. step1.py can handle HIS, MET, and CYS if they are the ligands to heme. If your heme ligand is not one of these three residues, please let us know.

```
 step1.py 1akk.pdb
```

The reason we specify the location is to force mcce to use our own renaming rules instead of the default one.
This command generates step1_out.pdb which is required of step 2.

## Step 2. Make side chain conformers
This step makes alternative side chain locations and ionization states.

```
 step2.py
```

This command generates step2_out.pdb which is required of step 3.

## Step 3. Make energy table
This step calculates conformer self energy and pairwise interaction table.

```
 step3.py
```
This command generates opp files under energies/ folder and file head3.lst which are required of step 4.


## Step 4. Simulate a titration with Monte Carlo sampling
This setp simulates a titration and write out the conformation and ionization states of each side chain at various conditions.

```
 step4.py -i 0 -d 60 -t eh
```

The occupancy table is in file fort.38.
The net charge is in file sum_crg.out
Eh is in file pK.out

## Results
The pKa report is in file pK.out.

```
 cat pK.out
```
You will see the calculated Eh for heme is 247 mV

To analyze the ionization energy of heme at the midpoint:
```
 mfe.py HEM+A0105_
```

A more detailed explanation of mfe.py program can be found here [TODO: Add link]
