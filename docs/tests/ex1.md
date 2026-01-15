---
title: 1 - Pre-check Protein Files
parent: Quick Start Tutorial
nav_order: 1
layout: default
permalink: /docs/tests/ex1/
---

# Exercise #1:  Sanity check using `p_info`

In this first exercise, we will introduce how to use basic pre-check tool called `p_info` on a real protein structure to confirm its compatibility with __MCCE4__.
This exercise serves as a __sanity check__ before running any pKₐ, Em, or microstate calculations.

{: .warning }
> __We highly recommend to always__ run `p_info` on a new protein structure, since it allows you to:
> - Implore if the protein file contents are compatible for an MCCE4 simulation
> - Build intuition about what MCCE4 “sees” in your structure
> - Identify potential problems before performing expensive calculations
> - Recognize unsupported cofactors and nonstandard residue names
> - Decide whether new topology files or residue renaming are required

### What does `p_info` do?

`p_info` performs an textual analysis of the input PDB file and reports it's compatibility with MCCE4. 
By running `p_info`, you can quickly identify common issues that often prevent MCCE calculations from running correctly, such as formatting problems, missing atoms, or unsupported residues.

### Specifically, `p_info` helps you:

✅ Verify that the PDB format is readable and compatible for an MCCE4 simulation

🔍 Identify recognized vs. unrecognized residues

🧪 Detect missing atoms, unusual naming conventions, or nonstandard residues

⚡ Check for charge-bearing residues relevant to pKₐ and Em calculations

💧 Summarize waters, ligands, and cofactors present in the structure

---

## 0. Pre-requisite:
Ensure you have the conda environment for ```mc4``` activated.
```
conda activate mc4
```

## 1. Prepare the Inputs
Enter the working directory for the first exercise:
```bash
cd mcce_workflows
mkdir ex1; cd ex1
```

Download the PDB file for 1B2V:
```bash
getpdb 1b2v
```

A successful download should display the following message:
```
 [ INFO ] Download completed: 1b2v.pdb
```

## 2. Run `p_info`

{: .highlight }
> `p_info` provides a count of amino acids by type and chain, ligand counts, and whether topology files are present for relevant ligands.
> `p_info` will run step 1 and create a file called "run1.log" as part of its functionality. 

```bash
p_info 1b2v.pdb
```

## 3. Examine the screen output:
On a first run, the screen should display the following:
```
p_info requires 1b2v.pdb,its associated run1.log, and step1_out.pdbin the current directory.

Running step1.py...

Number of amino acids in the protein: 173
This is a small sized protein. It will run quickly.

Number of ligands in the protein: 4

We have topology files for these ligands:
TPLFOUND: {'PAA', '_CA', 'PDD', 'HEM'}

For more detailed analysis of the protein, look in p_info.log.
```
