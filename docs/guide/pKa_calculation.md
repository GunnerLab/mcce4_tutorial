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

> **Reference:** Jens Erik Nielsen and J. Andrew McCammon, *Protein Sci.* **2003** Sep; 12(9): 1894–1901.

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

### Remove Explicit Waters
MCCE can run with or without waters, but calculations are much faster without explicit waters.
```
grep -v HOH 4LZT.pdb > 4LZT_noHOH.pdb
```

### Run the 4 Steps of MCCE
MCCE expects to run **in a single directory** containing only one calculation.
There are **4 sequential steps**. Each step’s output feeds into the next.
You can re-run later steps without starting over.


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

If you want to know the help information and other options of this command:
```
step2.py -h
```

### Step 3. Make energy table¶
This step calculates conformer self energy and pairwise interaction table.

```
step3.py
```
This command generates opp files under energies/ folder and file head3.lst which are required of step 4.

If you want to know the help information and other options of this command:
```
step3.py -h
```

### Step 4. Simulate a titration with Monte Carlo sampling¶
This step simulates a titration and writes out the conformation and ionization states of each side chain at various conditions.

```
step4.py --xts
```

If you want to know the help information and other options of this command:
```
step4.py -h
```

- fort.38. is the primary output file. It gives the average occupancy of each conformer.
- sum_crg.out give the net charge on all residues. Residues where all conformers have a zero charge are not reported in this file.
- pK.out calculates the best single pKa for each residue given the change in charge with pH in sum_crg.out.



