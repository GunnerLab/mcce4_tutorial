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
getpdb 2ycc
```

A successful download should display the following message:
```
 [ INFO ] Download completed: 2ycc.pdb
```

## 2. Run `p_info`

{: .highlight }
> `p_info` provides a count of amino acids by type and chain, ligand counts, and whether topology files are present for relevant ligands.
> `p_info` will run step 1 and create a file called "run1.log" as part of its functionality. 

```bash
p_info 2ycc.pdb
```

## 3. Examine the screen output:
On a first run, the screen should display the following:
```
p_info requires 2ycc.pdb, its associated run1.log, and step1_out.pdb in the current directory.

Running step1.py...

Number of amino acids in the protein: 107
This is a small sized protein. It will run quickly.

Number of ligands in the protein: 4

We have topology files for these ligands:
TPLFOUND: {'PDD', 'HEM', 'PAA'}

We do not have topology files for these ligands:
NOTPL: ['M3L']

For ligands with no topology files, here are your options:
        (1) Continue as is: These ligands will be treated as hydrophobic groups, with zero charge and zero vdw in new.tpl.
        (2) Repeat step1 with ligands removed: Remove ligands from input pdb and redo step1.
        (3) Repeat step1 after creating topology files for ligands. For instructions on creating topology files, go to:
            https://gunnerlab.github.io/mcce4_tutorial/docs/topology/

For more detailed analysis of the protein, look in p_info.log.
```
{: .highlight }
> `p_info` provides amino acid counts and ligand counts.
> `p_info` also provides information on which ligands have topology files and which don't.
> Certain ligands may look different or new ligands may appear in these lists as MCCE4 can modify or break up ligands to better process them.
> In this example, HEM is a ligand in 2ycc.pdb. However, it is broken into 2 ligands, PDD and PAA.
> Information is also provided on how to deal with ligands with no topology files.
> For more information on topology files, go to: [Create MCCE4 Topology Files](https://gunnerlab.github.io/mcce4_tutorial/docs/topology/).

## 4. Examine the p_info log file
In order to get a more in-depth analysis of the protein, look in the p-info log file.
Display the p_info log file:
```
cat p_info.log
```
The screen should display the following:
```
p_info requires 2ycc.pdb, its associated run1.log, and step1_out.pdb in the current directory.

Running step1.py...

p_info by Jared Suchomel, with contributions by Marilyn Gunner, Cat Chenal, and Junjun Mao! It's still in progress!

### For the input 2ycc.pdb:

Amino Acid Counts (of 107 Total):
Ionizable           Polar               Hydrophobic
---------------------------------------------------
LYS: 15             THR: 9              GLY: 12
GLU: 7              ASN: 7              LEU: 8
HIS: 4              TYR: 5              ALA: 7
ASP: 4              SER: 4              PHE: 4
ARG: 3              CYS: 2              PRO: 4
                    GLN: 2              ILE: 4
                                        VAL: 3
                                        MET: 2
                                        TRP: 1
---------------------------------------------------
Total: 33         Total: 29           Total: 45

Total           PDB Count     MCCE Count     Difference
Amino Acids     107           107            0
Ligands         3             4              1
Waters          61            6              55

### The PDB file has 1 chain(s).

This is a small sized protein. It will run quickly.

Waters and ions stripped if 5% of their Surface Area is exposed to Solvent.

SAS exposure limit edited in 'run.prm' parameter (H2O_SASCUTOFF).

Groups that can be deleted listed in 00always_needed.tpl.

NOTE: By default, waters are retained for an 'explicit solvent' run. Use the '--dry' flag for an 'implicit solvent' run.

These residues have been modified:

### TERMINI:

      Labeling "THR A  -5" as NTR
      Labeling "GLU A 103" as CTR

0 missing atoms added. This number DOES NOT include atoms relabeled as NTR, or CTR. See run1.log for full list.
53 atoms changed.

### LIGANDS:


      Ligand counts for chain A, in step1_out.pdb:
          PAA: 1
          M3L: 1
          PDD: 1
          HEM: 1

      Distance below bond threshold, renaming amino acid CYS A 14   to ligand CYL
      Distance below bond threshold, renaming amino acid CYS A 17   to ligand CYL
      Distance below bond threshold, renaming amino acid HIS A 18   to ligand HIL
      Distance below bond threshold, renaming amino acid MET A 80   to ligand MEL

      Groups are renamed or deleted relative to the input protein.

The rules for changes, and examples:

      FE   HEC A 104 -> FE   HEM A 104
       CAA HEM A 104 ->  CAA PAA A 104
       CAD HEM A 104 ->  CAD PDD A 104

A list of all atoms that are modified can be found in run1.log.

We have topology files for these ligands:
      TPLFOUND:  {'PDD', 'HEM', 'PAA'}

We do not have topology files for these ligands:
      NOTPL: ['M3L']

For ligands with no topology files, here are your options:
        (1) Continue as is: These ligands will be treated as hydrophobic groups, with zero charge and zero vdw in new.tpl.
        (2) Repeat step1 with ligands removed: Remove ligands from input pdb and redo step1.
        (3) Repeat step1 after creating topology files for ligands. For instructions on creating topology files, go to:
            https://gunnerlab.github.io/mcce4_tutorial/docs/topology/

p_info was run at local time Fri Jan 23 2026, 11:52AM

Thanks for using p_info! It's free!
```
