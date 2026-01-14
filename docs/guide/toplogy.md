---
title: Creating Topology Files for MCCE4
nav_order: 8
parent: Guide
layout: default
description: "Step-by-step tutorial for creating MCCE topology (.tpl/.ftpl) files, including desolvation energy extraction and a full LHG worked example."
permalink: /docs/topology/
---

# Tutorial: Creating Topology Files for MCCE
---

__What Are MCCE Topology Files?__
MCCE4 topology files (`.tpl` or `.ftpl`) define the **force-field and chemical parameters** used by __MCCE4__ during Monte Carlo sampling.

They include:
- Heavy-atom connectivity  
- Hydrogen placement rules  
- Rotamer generation rules  
- Protonation and redox states  
- Atomic partial charges  
- Reaction field (desolvation) energies  
- Solution pKa values (`pKa, sol`)  
- Solution midpoint potentials (`Em, sol`)  

<p align="center">
  <img src="{{ '/docs/images/mcce_toplogy_01.png' | relative_url }}" alt="MCCE Logo" style="max-width: 100%; height: auto;">
</p>

Without a topology file, __MCCE4__ **cannot treat ligands or non-standard residues**.

The default system topology files are located in: 
```
/home/user/MCCE4-Alpha/param
```

Official documentation:  
<https://sites.google.com/site/mccewiki/topology-files>

---

## Step 1 — Run ```p_info``` to identify unsupported non-standard residues or ligands
Enter the working directory for this exercise:
```bash
cd mcce_workflows
mkdir ex_topology; cd ex_topology
```

### Download the PDB file for :
```bash
 getpdb 3aox
```

### Run ```p_info``
```
pinfo 3aox.pdb
```

Expected output should look like:
```

Number of amino acids in the protein: 307
This is a moderate sized protein. It will take hours to run.

Number of ligands in the protein: 2

We do not have topology files for these ligands:
NOTPL: ['EMH', 'EDO']

For more detailed analysis of the protein, look in p_info.log.
```

{: .important }
> **By default MCCE4** will create a file called ```new.tpl``` which contains *ghost* toplogies for unsupported molecules.
> This file contains the guessed connectivity based on inter-atom distances.

## Step 2: Design a basic .ftpl (an MCCE topology file) for an unsupported molecule

__What do you need?__
1. An ideal pdb structure for a molecule (RCSB, LigandExpo, etc)
2. Associated charge sets for given formal charges and conformation states of the molecule (PARSE, AMBER, NBO, etc). Examples of charge generators are:
   - [APBS](https://server.poissonboltzmann.org/pdb2pqr)
   - [Atomic Charge Calculator III](https://acc.biodata.ceitec.cz/)
   - [PDB Charges](https://pdbcharges.biodata.ceitec.cz/)
   - [OpenEye QuackPack TK](https://docs.eyesopen.com/applications/quacpac/index.html)
     - If you set up an openeye license, __MCCE4-Alpha__ has python scripts to generate chargesets using ```oe_assigncharges_QuacpakTK.py``` and
       ```oe_readOEBcharges.py```

For this tutorial we will be designing a topology file for the kinase inhibitor [EMH](https://www.rcsb.org/ligand/EMH)  using chargesets generated with __Schrödinger__ suite software.

{: .important }
> __RCSB__ will no longer support .pdb format so .cif files will be needed to convert to .pdb format for use in __MCCE4__
> 
> You may use __MCCE4__ .cif to .pdb converter using PyMol for the conversion:
>```bash
> cif2pdb_PyMOL EMH.cif
>```

__Create a ```user_param``` directory__
```bash
mkdir user_param
```

{: .important }
> __MCCE4__ uses toplogies files located in **user_param** for a working directory in addition to and supersede existing to the default system's topology files.

__Convert your .pdb file into .ftpl__ 
```
pdb2ftpl.py -p EMH.pdb > EMH_OG.ftpl
```


---

