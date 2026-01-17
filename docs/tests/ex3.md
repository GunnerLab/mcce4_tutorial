---
title: 3 - Eₘ Calculation
parent: Quick Start Tutorial
nav_order: 3
layout: default
permalink: /docs/tests/ex3/
---

# Exercise #3: Eₘ Calculation (redox titration over a defined Eₕ range)
In this exercise, we will perform a __Eₘ calculation__ using a protein file using **MCCE4** .

## Background
__Cytochrome c__ is a small protein that transports electrons in mitochondria to facilitate the
synthesis of ATP. Its redox potential plays an important role in its function. The regulation of the
cytochrome c redox potential can be explained by continuum electrostatic analysis.

We are using [PDB ID: 1AKK](https://www.rcsb.org/structure/1AKK) The experimental Em of Cytochrome c is 260 mV. 
 
> __Reference:__ [Junjun Mao, Karin Hauser, and M. R. Gunner, How Cytochromes with Different Folds Control Heme Redox Potentials, Biochemistry 2003, 42(33), 9829–9840](https://pubmed.ncbi.nlm.nih.gov/12924932/)

---
## 0. Pre-requisite:
Ensure you have the conda enviorment for ```mc4``` activated.
```
conda activate mc4
```

## 1. Prepare the Calculation
Enter the working directory for this exercise:
```bash
cd mcce_workflows
mkdir ex3; cd ex3
```

Download the PDB file for 1AKK:
```bash
getpdb 1akk
```

A successful download should display the following message:
```bash
 [ INFO ] Download completed: 1akk.pdb
```

{: .important }
> **We strongly recommend** to run `p_info` to inspect an unfamiliar PDB file and verify if it is compatible with MCCE4.
> ```bash
> p_info 1akk.pdb
> ```

## 2. Perform Eₘ using `run_mcce4`
The easiest way to run a mcce4 simulation is with `run_mcce4`. 
It is preset to run a full simulation (ending with a titration) and return the pKas of ionizable residues into one of its output files called "pK.out" upon successful completion.

```bash
run_mcce4 1akk.pdb -type eh -initial 0 -interval 60 -n 15
```

The occupancy table is in file ```fort.38```.
The net charge is in file ```sum_crg.out```.
Eh is in file ```pK.out```

## 3. Interpret Eₘ results
The pKa/Em report is in file pK.out.

```
cat pK.out
```

You will see the calculated Eₕ for heme is __247 mV__

{: .note }
> **Optional Step:** To analyze the ionization energy of heme at the midpoint:
> ```
> mfe.py HEM+A0105_
> ```
>
> A more detailed explanation of mfe.py program can be found here [MFE Tutorial](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/mfe_tutorial/)


### For your information

__Heme acids:__ The heme in cytochrome C has two ligands HIS18 and MET80.
They behave differently than HIS and MET so we must rename them. step1.py can handle HIS, MET, and CYS if they are the ligands to heme.


__HEM and HIS__ 
are treated differently in cytochrome c, one axial ligand is histidine (His18) while the other is methionine (Met80). The Coordinates Fe via its imidazole nitrogen which does not change oxidation state and doesn’t accept or donate electrons. However, it does influence Eₘ indirectly by modifying the ligand field,altering the electrostatic stabilization of Fe³⁺ vs Fe²⁺ and Possibly undergoing protonation/deprotonation, which is coupled to redox chemistry
Thus, His is treated differently because it contributes electrostatically, not electronically.
