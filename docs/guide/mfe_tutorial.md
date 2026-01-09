---
title: MFE tutorial 
parent: Guide
nav_order: 2
permalink: /docs/guide/mfe_tutorial/
layout: default
---

# MFE tutorial 

## What does MFE do? 

MFE (mean field energy) Calculates the mean field ionization energy on an ionizable residue at a specific pH/eH. MFE provieds 

## Files needed 
Files needed to run this tool is fort.38, head3.lst, pK.out, and sum_crg.out 

To create these files please look at pKa calculation tutorial (ADD LINK)

## Usage 

If you have succesfully installed the  MCCE-Tools (add link for mcce tools install) you should be able to call the tool from any directory. 

```
mfe.py –p 7 -c 0.05 LYS+A0001_

```

the -p flags defines at what pH the analysis is done. -C is at the energy cutoff(ask for a better explination)

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


Should I add the definition of each term?
