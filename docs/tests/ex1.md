---
title: Exercise One
parent: Test Cases
nav_order: 2
layout: default
permalink: /docs/tests/ex1/
---

# Exercise #1:  Sanity check using `p_info`

In this first exercise, we will use a basic pre-check tool called `p_info` on a real protein structure to confirm its compatibility with __MCCE4__.

This exercise serves as a __sanity check__ before running any pKₐ, Em, or microstate calculations.

---

__What does `p_info` do?__

`p_info` performs a static analysis of the input PDB file and reports whether it is compatible with MCCE4. By running `p_info`, you can quickly identify common issues that often prevent MCCE calculations from running correctly, such as formatting problems, missing atoms, or unsupported residues.

Specifically, `p_info` helps you:

✅ Verify that the PDB format is readable by MCCE4

🔍 Identify recognized vs. unrecognized residues

🧪 Detect missing atoms, unusual naming conventions, or nonstandard residues

⚡ Check for charge-bearing residues relevant to pKₐ and Em calculations

💧 Summarize waters, ligands, and cofactors present in the structure


{: .highlight }
Running `p_info` first allows you to:
- Identify potential problems before performing expensive calculations
- Build intuition about what MCCE “sees” in your structure
- Recongize unsupported cofactors and nonstandard residue names
- Decide whether new topology files or residue renaming are required

---

## Step 1 – Prepare the Inputs

First, enter the working directory for the first exercise:

```bash
cd ~/mcce4_tests/ex1
```

Download the PDB file for 1B2V:
```bash
getpdb 1b2v
```

A successful download should display the following message:
```
 [ INFO ] Download completed: 1b2v.pdb
```

## Step 2 – Run `p_info`

{: .highlight }
> `p_info` provides a count of amino acids by type and chain, ligand counts, and whether topology files are present for relevant ligands.
> `p_info` will run step 1 and create a file called "run1.log" as part of its functionality. 

```bash
p_info 1b2v.pdb
```

Content should like as follows:

```
p_info requires 1b2v.pdb, its associated run1.log, and step1_out.pdb in the current directory.

Running step1.py...

p_info by Jared Suchomel, with contributions by Marilyn Gunner, Cat Chenal, and Junjun Mao! It's still in progress!

### For the input 1b2v.pdb:

Amino Acid Counts (of 173 Total):
Ionizable           Polar               Hydrophobic
---------------------------------------------------
ASP: 13             SER: 22             GLY: 26
HIS: 5              THR: 13             LEU: 16
GLU: 2              ASN: 11             ALA: 15
                    TYR: 10             VAL: 13
                    GLN: 9              PHE: 9
                                        ILE: 4
                                        PRO: 3
                                        TRP: 1
                                        MET: 1
---------------------------------------------------
Total: 20         Total: 65           Total: 88

Total           PDB Count     MCCE Count     Difference
Amino Acids     173           173            0
Ligands         2             4              2
Waters          176           5              171

### The PDB file has 1 chain(s).

Waters and ions stripped if 5% of their Surface Area is exposed to Solvent.

SAS exposure limit edited in 'run.prm' parameter (H2O_SASCUTOFF).

Groups that can be deleted listed in 00always_needed.tpl.

NOTE: By default, waters are retained for an 'explicit solvent' run. Use the '--dry' flag for an 'implicit solvent' run.

These residues have been modified:

### TERMINI:

      Labeling "ALA A   2" as NTR
      Labeling "ALA A 174" as CTR

0 missing atoms added. This number DOES NOT include atoms relabeled as NTR, or CTR. See run1.log for full list.
11 atoms changed.

### LIGANDS:


      Ligand counts for chain A, in step1_out.pdb:
          _CA: 1
          HEM: 1
          PDD: 1
          PAA: 1

      Distance below bond threshold, renaming amino acid HIS A 32   to ligand HIL

      Groups are renamed or deleted relative to the input protein.

The rules for changes, and examples:

      CA    CA A 199 -> CA   _CA A 199
       CAA HEM A 200 ->  CAA PAA A 200
       CAD HEM A 200 ->  CAD PDD A 200

A list of all atoms that are modified can be found in run1.log.

We have topology files for these ligands:
      TPLFOUND:  {'PAA', 'HEM', '_CA', 'PDD'}

p_info was run at local time Wed Jan 07 2026, 02:29PM

Thanks for using p_info! It's free!
```
