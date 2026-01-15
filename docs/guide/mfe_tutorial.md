---
title: MFE tutorial 
nav_order: 4
parent: Guide
permalink: /docs/guide/mfe_tutorial/
layout: default
---

# MFE tutorial 

## What does MFE do? 

MFE (mean field energy) Calculates the mean field ionization energy on an ionizable residue at a specific pH/eH. MFE provides the energy interctions of ionized residues and its neighbors. 

## Files needed 
Files needed to run this tool is **fort.38, head3.lst, pK.out, and sum_crg.out** 

To create these files please look at [pKa calculation tutorial](https://gunnerlab.github.io/mcce4_tutorial/docs/tests/ex2/)

## Usage 

If you have succesfully installed the  [MCCE-Tools](https://github.com/GunnerLab/MCCE4-Tools) you should be able to call the tool from any directory. 

```
mfe.py –p 7 -c 0.05 LYS+A0001_

```

the -p flags defines at what pH the analysis is done. -c is at the energy cutoff that defines the energy minimum for residues interaction. 

## Output 

Output for the analysis should look like this for LysA1 in lysozyme : 


```
Residue LYS+A0001_ pKa/Em=9.494
=================================
Terms          pH     meV    Kcal
---------------------------------
vdw0        -0.03   -1.96   -0.05
vdw1        -0.00   -0.26   -0.01
tors         0.00    0.00    0.00
ebkb         0.07    4.17    0.10
dsol         0.59   34.09    0.80
offset       0.29   17.02    0.40
pH&pK0      -3.40 -197.34   -4.64
Eh&Em0       0.00    0.00    0.00
-TS          0.00    0.00    0.00
residues     0.15    8.68    0.20
*********************************
TOTAL       -2.34 -135.60   -3.19  sum_crg
*********************************
NTRA0001_    0.13    7.51    0.18    0.44
ARGA0005_    0.06    3.32    0.08    1.00
GLUA0007_   -0.23  -13.40   -0.31   -1.00
ARGA0014_    0.26   15.23    0.36    1.00
ASPA0087_   -0.22  -12.72   -0.30   -1.00
ARGA0128_    0.07    3.79    0.09    1.00
CTRA0129_   -0.06   -3.32   -0.08   -1.00
```

This output gives you the a varierity of the interactions of energy terms in differen units (pH, meV, kCal).

## Definitions of terms 

1) Vdw0: vdW interactions within the conformer (a side chain or ligand) + interaction with implicit solvent

2) Vdw1: vdw interaction of this conformer with the protein backbone

3) tors: torsion oenergy of the conformer

4) dsol: Loss of solvation energy compared with this conformer in solution. It should be positive as it is a loss in energy.

5) offset: energy term that can be freely modified

6) pH&pK0:  pka in solution. 0 for ref (neutral protonation state)

7) Eh&Em0: electrochemical midpoint

8) -TS: entropy energu

9) residues:???

10) total:????

At the end of the file you can see the chose ionized residue interactions with its neighboring residues 
