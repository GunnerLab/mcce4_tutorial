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

__What do you need to create a new MCCE4 toplogy file?__
1. An ideal pdb structure for a molecule (RCSB, LigandExpo, etc)
2. Associated charge sets for given formal charges and conformation states of the molecule (PARSE, AMBER, NBO, etc). Examples of charge generators are:
   - [APBS](https://server.poissonboltzmann.org/pdb2pqr)
   - [Atomic Charge Calculator III](https://acc.biodata.ceitec.cz/)
   - [PDB Charges](https://pdbcharges.biodata.ceitec.cz/)
   - [OpenEye QuackPack TK](https://docs.eyesopen.com/applications/quacpac/index.html)
     - If you set up an openeye license, __MCCE4-Alpha__ has python scripts to generate chargesets using ```oe_assigncharges_QuacpakTK.py``` and
       ```oe_readOEBcharges.py```

{: .important }
> __RCSB__ will no longer support .pdb format so .cif files will be needed to convert to .pdb format for use in __MCCE4__
> 
> You may use __MCCE4__ .cif to .pdb converter using PyMol for the conversion:
>```bash
> cif2pdb_PyMOL EMH.cif
>```
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

## Step 2: Generate template for a basic .ftpl (an MCCE topology file) for an unsupported molecule
For this tutorial we will be designing a topology file for the kinase inhibitor [EMH](https://www.rcsb.org/ligand/EMH) using chargesets generated with __Schrödinger__ suite software.

__Create a ```user_param``` directory__
```bash
mkdir user_param
```

{: .important }
> __MCCE4__ uses topologies files located in **user_param** for a working directory in addition to and supersede existing to the default system's topology files.

__Convert your .pdb file into .ftpl__ 
For this tutorial, we will design conformers for two different protonatation states (01, +1) for the __EMH__ molecule. 
```
pdb2ftpl.py -p EMH.pdb -c 01 +1 > EMH.ftpl
```

This will design a template toplogy file for __EMH__ which consists of conformer types (CONFLIST), connectivity (CONNECT), unfilled atomic charges (CHARGE), default atom radii (RADIUS), and conformer parameters (CONFORMER) for each conformer. 

## Step 2: Fill charges for the template for the new .ftpl (an MCCE topology file) for the unsupported molecule
For this tutorial, we will fill in the charges and design parameters for each conformer for the [EMH] template toplogy file ```EMH.ftpl```

1. __Fill in "to_be_filled" charges__
Using your file editor of choice, fill in the "to_be_filled" sections with the appropriate atomistic charge for all conformer atoms.
Thus, changing: 
```
# ATOM charges
CHARGE, EMH01, " C4 ": to_be_filled
CHARGE, EMH01, " C5 ": to_be_filled
CHARGE, EMH01, " C6 ": to_be_filled
CHARGE, EMH01, " C7 ": to_be_filled
CHARGE, EMH01, " C8 ": to_be_filled
CHARGE, EMH01, " C10": to_be_filled
CHARGE, EMH01, " C13": to_be_filled
CHARGE, EMH01, " C15": to_be_filled
CHARGE, EMH01, " C17": to_be_filled
CHARGE, EMH01, " C21": to_be_filled
CHARGE, EMH01, " C22": to_be_filled
CHARGE, EMH01, " C24": to_be_filled
CHARGE, EMH01, " C26": to_be_filled
CHARGE, EMH01, " C28": to_be_filled
CHARGE, EMH01, " N19": to_be_filled
CHARGE, EMH01, " C18": to_be_filled
CHARGE, EMH01, " C3 ": to_be_filled
CHARGE, EMH01, " C1 ": to_be_filled
CHARGE, EMH01, " C2 ": to_be_filled
CHARGE, EMH01, " N9 ": to_be_filled
CHARGE, EMH01, " C14": to_be_filled
CHARGE, EMH01, " C16": to_be_filled
CHARGE, EMH01, " O20": to_be_filled
CHARGE, EMH01, " C12": to_be_filled
CHARGE, EMH01, " C11": to_be_filled
CHARGE, EMH01, " C23": to_be_filled
CHARGE, EMH01, " N25": to_be_filled
CHARGE, EMH01, " C29": to_be_filled
CHARGE, EMH01, " C27": to_be_filled
CHARGE, EMH01, " C30": to_be_filled
CHARGE, EMH01, " N31": to_be_filled
CHARGE, EMH01, " C32": to_be_filled
CHARGE, EMH01, " C34": to_be_filled
CHARGE, EMH01, " O36": to_be_filled
CHARGE, EMH01, " C35": to_be_filled
CHARGE, EMH01, " C33": to_be_filled
CHARGE, EMH01, " H4 ": to_be_filled
CHARGE, EMH01, " H6 ": to_be_filled
CHARGE, EMH01, " H13": to_be_filled
CHARGE, EMH01, " H21": to_be_filled
CHARGE, EMH01, "H21A": to_be_filled
CHARGE, EMH01, "H21B": to_be_filled
CHARGE, EMH01, " H22": to_be_filled
CHARGE, EMH01, "H22A": to_be_filled
CHARGE, EMH01, "H22B": to_be_filled
CHARGE, EMH01, " H24": to_be_filled
CHARGE, EMH01, "H24A": to_be_filled
CHARGE, EMH01, "H24B": to_be_filled
CHARGE, EMH01, " H26": to_be_filled
CHARGE, EMH01, " H28": to_be_filled
CHARGE, EMH01, "H28A": to_be_filled
CHARGE, EMH01, " H3 ": to_be_filled
CHARGE, EMH01, " H12": to_be_filled
CHARGE, EMH01, " H23": to_be_filled
CHARGE, EMH01, "H23A": to_be_filled
CHARGE, EMH01, " H29": to_be_filled
CHARGE, EMH01, "H29A": to_be_filled
CHARGE, EMH01, " H27": to_be_filled
CHARGE, EMH01, "H27A": to_be_filled
CHARGE, EMH01, " H30": to_be_filled
CHARGE, EMH01, "H30A": to_be_filled
CHARGE, EMH01, " H32": to_be_filled
CHARGE, EMH01, "H32A": to_be_filled
CHARGE, EMH01, " H34": to_be_filled
CHARGE, EMH01, "H34A": to_be_filled
CHARGE, EMH01, " H35": to_be_filled
CHARGE, EMH01, "H35A": to_be_filled
CHARGE, EMH01, " H33": to_be_filled
CHARGE, EMH01, "H33A": to_be_filled
CHARGE, EMH01, " H9 ": to_be_filled

CHARGE, EMH+1, " C4 ": to_be_filled
CHARGE, EMH+1, " C5 ": to_be_filled
CHARGE, EMH+1, " C6 ": to_be_filled
CHARGE, EMH+1, " C7 ": to_be_filled
CHARGE, EMH+1, " C8 ": to_be_filled
CHARGE, EMH+1, " C10": to_be_filled
CHARGE, EMH+1, " C13": to_be_filled
CHARGE, EMH+1, " C15": to_be_filled
CHARGE, EMH+1, " C17": to_be_filled
CHARGE, EMH+1, " C21": to_be_filled
CHARGE, EMH+1, " C22": to_be_filled
CHARGE, EMH+1, " C24": to_be_filled
CHARGE, EMH+1, " C26": to_be_filled
CHARGE, EMH+1, " C28": to_be_filled
CHARGE, EMH+1, " N19": to_be_filled
CHARGE, EMH+1, " C18": to_be_filled
CHARGE, EMH+1, " C3 ": to_be_filled
CHARGE, EMH+1, " C1 ": to_be_filled
CHARGE, EMH+1, " C2 ": to_be_filled
CHARGE, EMH+1, " N9 ": to_be_filled
CHARGE, EMH+1, " C14": to_be_filled
CHARGE, EMH+1, " C16": to_be_filled
CHARGE, EMH+1, " O20": to_be_filled
CHARGE, EMH+1, " C12": to_be_filled
CHARGE, EMH+1, " C11": to_be_filled
CHARGE, EMH+1, " C23": to_be_filled
CHARGE, EMH+1, " N25": to_be_filled
CHARGE, EMH+1, " C29": to_be_filled
CHARGE, EMH+1, " C27": to_be_filled
CHARGE, EMH+1, " C30": to_be_filled
CHARGE, EMH+1, " N31": to_be_filled
CHARGE, EMH+1, " C32": to_be_filled
CHARGE, EMH+1, " C34": to_be_filled
CHARGE, EMH+1, " O36": to_be_filled
CHARGE, EMH+1, " C35": to_be_filled
CHARGE, EMH+1, " C33": to_be_filled
CHARGE, EMH+1, " H4 ": to_be_filled
CHARGE, EMH+1, " H6 ": to_be_filled
CHARGE, EMH+1, " H13": to_be_filled
CHARGE, EMH+1, " H21": to_be_filled
CHARGE, EMH+1, "H21A": to_be_filled
CHARGE, EMH+1, "H21B": to_be_filled
CHARGE, EMH+1, " H22": to_be_filled
CHARGE, EMH+1, "H22A": to_be_filled
CHARGE, EMH+1, "H22B": to_be_filled
CHARGE, EMH+1, " H24": to_be_filled
CHARGE, EMH+1, "H24A": to_be_filled
CHARGE, EMH+1, "H24B": to_be_filled
CHARGE, EMH+1, " H26": to_be_filled
CHARGE, EMH+1, " H28": to_be_filled
CHARGE, EMH+1, "H28A": to_be_filled
CHARGE, EMH+1, " H3 ": to_be_filled
CHARGE, EMH+1, " H12": to_be_filled
CHARGE, EMH+1, " H23": to_be_filled
CHARGE, EMH+1, "H23A": to_be_filled
CHARGE, EMH+1, " H29": to_be_filled
CHARGE, EMH+1, "H29A": to_be_filled
CHARGE, EMH+1, " H27": to_be_filled
CHARGE, EMH+1, "H27A": to_be_filled
CHARGE, EMH+1, " H30": to_be_filled
CHARGE, EMH+1, "H30A": to_be_filled
CHARGE, EMH+1, " H32": to_be_filled
CHARGE, EMH+1, "H32A": to_be_filled
CHARGE, EMH+1, " H34": to_be_filled
CHARGE, EMH+1, "H34A": to_be_filled
CHARGE, EMH+1, " H35": to_be_filled
CHARGE, EMH+1, "H35A": to_be_filled
CHARGE, EMH+1, " H33": to_be_filled
CHARGE, EMH+1, "H33A": to_be_filled
CHARGE, EMH+1, " H9 ": to_be_filled
```

Your amended file should like:
```
# ATOM charges
CHARGE, EMH01, " C4 ": -0.084 # amended
CHARGE, EMH01, " C5 ": -0.009 # amended
CHARGE, EMH01, " C6 ": -0.112 # amended
CHARGE, EMH01, " C7 ": -0.226 # amended
CHARGE, EMH01, " C8 ":  0.133 # amended
CHARGE, EMH01, " C10":  0.185 # amended
CHARGE, EMH01, " C13": -0.077 # amended
CHARGE, EMH01, " C15": -0.157 # amended
CHARGE, EMH01, " C17":  0.043 # amended
CHARGE, EMH01, " C21": -0.089 # amended
CHARGE, EMH01, " C22": -0.089 # amended
CHARGE, EMH01, " C24": -0.087 # amended
CHARGE, EMH01, " C26":  0.190 # amended
CHARGE, EMH01, " C28": -0.090 # amended
CHARGE, EMH01, " N19": -0.369 # amended
CHARGE, EMH01, " C18":  0.236 # amended
CHARGE, EMH01, " C3 ": -0.100 # amended
CHARGE, EMH01, " C1 ":  0.061 # amended
CHARGE, EMH01, " C2 ": -0.024 # amended
CHARGE, EMH01, " N9 ": -0.565 # amended
CHARGE, EMH01, " C14": -0.034 # amended
CHARGE, EMH01, " C16":  0.620 # amended
CHARGE, EMH01, " O20": -0.549 # amended
CHARGE, EMH01, " C12": -0.164 # amended
CHARGE, EMH01, " C11": -0.081 # amended
CHARGE, EMH01, " C23": -0.033 # amended
CHARGE, EMH01, " N25": -0.754 # amended
CHARGE, EMH01, " C29":  0.240 # amended
CHARGE, EMH01, " C27": -0.090 # amended
CHARGE, EMH01, " C30":  0.240 # amended
CHARGE, EMH01, " N31": -0.720 # amended
CHARGE, EMH01, " C32":  0.128 # amended
CHARGE, EMH01, " C34":  0.126 # amended
CHARGE, EMH01, " O36": -0.416 # amended
CHARGE, EMH01, " C35":  0.126 # amended
CHARGE, EMH01, " C33":  0.128 # amended
CHARGE, EMH01, " H4 ":  0.166 # amended
CHARGE, EMH01, " H6 ":  0.147 # amended
CHARGE, EMH01, " H13":  0.162 # amended
CHARGE, EMH01, " H21":  0.047 # amended
CHARGE, EMH01, "H21A":  0.047 # amended
CHARGE, EMH01, "H21B":  0.047 # amended
CHARGE, EMH01, " H22":  0.047 # amended
CHARGE, EMH01, "H22A":  0.047 # amended
CHARGE, EMH01, "H22B":  0.047 # amended
CHARGE, EMH01, " H24":  0.037 # amended
CHARGE, EMH01, "H24A":  0.037 # amended
CHARGE, EMH01, "H24B":  0.037 # amended
CHARGE, EMH01, " H26":  0.018 # amended
CHARGE, EMH01, " H28":  0.055 # amended
CHARGE, EMH01, "H28A":  0.055 # amended
CHARGE, EMH01, " H3 ":  0.144 # amended
CHARGE, EMH01, " H12":  0.137 # amended
CHARGE, EMH01, " H23":  0.052 # amended
CHARGE, EMH01, "H23A":  0.052 # amended
CHARGE, EMH01, " H29":  0.033 # amended
CHARGE, EMH01, "H29A":  0.033 # amended
CHARGE, EMH01, " H27":  0.055 # amended
CHARGE, EMH01, "H27A":  0.055 # amended
CHARGE, EMH01, " H30":  0.033 # amended
CHARGE, EMH01, "H30A":  0.033 # amended
CHARGE, EMH01, " H32":  0.041 # amended
CHARGE, EMH01, "H32A":  0.041 # amended
CHARGE, EMH01, " H34":  0.054 # amended
CHARGE, EMH01, "H34A":  0.054 # amended
CHARGE, EMH01, " H35":  0.054 # amended
CHARGE, EMH01, "H35A":  0.054 # amended
CHARGE, EMH01, " H33":  0.041 # amended
CHARGE, EMH01, "H33A":  0.041 # amended
CHARGE, EMH01, " H9 ":  0.460 # amended

CHARGE, EMH+1, " C4 ": -0.084 # amended
CHARGE, EMH+1, " C5 ":  0.003 # amended
CHARGE, EMH+1, " C6 ": -0.107 # amended
CHARGE, EMH+1, " C7 ": -0.225 # amended
CHARGE, EMH+1, " C8 ":  0.123 # amended
CHARGE, EMH+1, " C10":  0.116 # amended
CHARGE, EMH+1, " C13": -0.075 # amended
CHARGE, EMH+1, " C15": -0.136 # amended
CHARGE, EMH+1, " C17":  0.042 # amended
CHARGE, EMH+1, " C21": -0.089 # amended
CHARGE, EMH+1, " C22": -0.089 # amended
CHARGE, EMH+1, " C24": -0.088 # amended
CHARGE, EMH+1, " C26":  0.139 # amended
CHARGE, EMH+1, " C28": -0.110 # amended
CHARGE, EMH+1, " N19": -0.349 # amended
CHARGE, EMH+1, " C18":  0.227 # amended
CHARGE, EMH+1, " C3 ": -0.099 # amended
CHARGE, EMH+1, " C1 ":  0.063 # amended
CHARGE, EMH+1, " C2 ": -0.027 # amended
CHARGE, EMH+1, " N9 ": -0.559 # amended
CHARGE, EMH+1, " C14": -0.022 # amended
CHARGE, EMH+1, " C16":  0.617 # amended
CHARGE, EMH+1, " O20": -0.534 # amended
CHARGE, EMH+1, " C12": -0.161 # amended
CHARGE, EMH+1, " C11": -0.066 # amended
CHARGE, EMH+1, " C23": -0.029 # amended
CHARGE, EMH+1, " N25": -0.687 # amended
CHARGE, EMH+1, " C29":  0.208 # amended
CHARGE, EMH+1, " C27": -0.110 # amended
CHARGE, EMH+1, " C30":  0.208 # amended
CHARGE, EMH+1, " N31": -0.686 # amended
CHARGE, EMH+1, " C32":  0.068 # amended
CHARGE, EMH+1, " C34":  0.097 # amended
CHARGE, EMH+1, " O36": -0.373 # amended
CHARGE, EMH+1, " C35":  0.097 # amended
CHARGE, EMH+1, " C33":  0.068 # amended
CHARGE, EMH+1, " H4 ":  0.166 # amended
CHARGE, EMH+1, " H6 ":  0.152 # amended
CHARGE, EMH+1, " H13":  0.172 # amended
CHARGE, EMH+1, " H21":  0.048 # amended
CHARGE, EMH+1, "H21A":  0.048 # amended
CHARGE, EMH+1, "H21B":  0.048 # amended
CHARGE, EMH+1, " H22":  0.048 # amended
CHARGE, EMH+1, "H22A":  0.048 # amended
CHARGE, EMH+1, "H22B":  0.048 # amended
CHARGE, EMH+1, " H24":  0.038 # amended
CHARGE, EMH+1, "H24A":  0.038 # amended
CHARGE, EMH+1, "H24B":  0.038 # amended
CHARGE, EMH+1, " H26":  0.101 # amended
CHARGE, EMH+1, " H28":  0.069 # amended
CHARGE, EMH+1, "H28A":  0.069 # amended
CHARGE, EMH+1, " H3 ":  0.149 # amended
CHARGE, EMH+1, " H12":  0.133 # amended
CHARGE, EMH+1, " H23":  0.048 # amended
CHARGE, EMH+1, "H23A":  0.048 # amended
CHARGE, EMH+1, " H29":  0.060 # amended
CHARGE, EMH+1, "H29A":  0.060 # amended
CHARGE, EMH+1, " H27":  0.069 # amended
CHARGE, EMH+1, "H27A":  0.069 # amended
CHARGE, EMH+1, " H30":  0.060 # amended
CHARGE, EMH+1, "H30A":  0.060 # amended
CHARGE, EMH+1, " H32":  0.113 # amended
CHARGE, EMH+1, "H32A":  0.113 # amended
CHARGE, EMH+1, " H34":  0.097 # amended
CHARGE, EMH+1, "H34A":  0.097 # amended
CHARGE, EMH+1, " H35":  0.097 # amended
CHARGE, EMH+1, "H35A":  0.097 # amended
CHARGE, EMH+1, " H33":  0.113 # amended
CHARGE, EMH+1, "H33A":  0.113 # amended
CHARGE, EMH+1, " H9 ":  0.462 # amended
CHARGE, EMH+1, " H71":  0.440 # amended
```

2. __Calibrate conformer parameters__

Depending on the type of molecule and it's conformational protonation states, we will need to calibrate a few things.
- Em0 (Electrochemical Midpoint) 
- pKa (pKa)
- ne  (# of electrons changed)
- nH  (# of protons changed)
- rxn02, rxn04 and/or rvn08 (solvation)

In this case, the only parameters we will need to calibrate are the rxn values and the nH value since.

First link the updated ```EMH.ftpl``` to your ```user_param``` directory:
```bash
cd user_param
ln -s ../EMH.ftpl .
cd ../
```

Next, to calibrate the rxn values, please run MCCE4 steps 1-3.
```
step1.py
step2.py
step3.py -d 4
```

Read the outputs of ```head3.lst``` column ```dsolv``` to retrieve the reaction field calibration value in solvent for a select dieletric constant.
```
iConf CONFORMER     FL  occ    crg   Em0  pKa0 ne nH    vdw0    vdw1    tors    epol   dsolv   extra    history
00001 EMH01_0000_001 f 0.00 -0.000     0  0.00  0  0 -15.976   0.000   0.000   0.000  -8.085   0.000 01O000M000 t
00002 EMH01_0000_002 f 0.00 -0.000     0  0.00  0  0 -15.973   0.000   0.000   0.000  -8.082   0.000 01O000M000 t
00003 EMH+1_0000_003 f 0.00  1.000     0  0.00  0  0  -8.948   0.000   0.000   0.000 -21.009   0.000 +1O000M000 t
```

Since we ran ```step3.py``` with a dielectric of 4, we can now update the ```EMH.ftpl```. For each conformer of EMH (EMH01 and EMH+1), use the most negative value as the calibration value for the ```EMH.ftpl``` file.

Using your file editor of choice, update sections rxn04 with the appropriate desolvation value. 
Additionally, we can change the ```nH``` parameter in ```EMH+1``` to be 1 to indicate the excess proton. 

```
# Conformer parameters that appear in head3.lst: ne, Em0, nH, pKa0, rxn
CONFORMER, EMH01:  Em0=0.0, pKa0=0.00, ne=0, nH=0, rxn02= 0, rxn04=  -8.085, rxn08= 0
CONFORMER, EMH+1:  Em0=0.0, pKa0=0.00, ne=0, nH=1, rxn02= 0, rxn04= -21.009, rxn08= 0
```

Rerun ```step3.py``` with the updated ```EMH.ftpl``` to ensure the calibration was successful. If it was, the ```dsolv``` parameter should now be close to or is ```0.000``` for each conformer respective conformer. A negative value should not be present as it indicates the calibration was off.
Thus, your new ```head3.lst``` should look like:
```
iConf CONFORMER     FL  occ    crg   Em0  pKa0 ne nH    vdw0    vdw1    tors    epol   dsolv   extra    history
00001 EMH01_0000_001 f 0.00 -0.000     0  0.00  0  0 -15.976   0.000   0.000   0.000   0.000   0.000 01O000M000 t
00002 EMH01_0000_002 f 0.00 -0.000     0  0.00  0  0 -15.973   0.000   0.000   0.000   0.003   0.000 01O000M000 t
00003 EMH+1_0000_003 f 0.00  1.000     0  0.00  0  1  -8.948   0.000   0.000   0.000   0.000   0.000 +1O000M000 t
```

Repeat, to update the rxn values for other dielectric constants if you wish.

Once this is done, congrats you have officially designed your first __MCCE__ topology file!

---

