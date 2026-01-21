---
title: 3 - Eₘ Calculation
parent: Quick Start Tutorial
nav_order: 3
layout: default
permalink: /docs/tests/ex3/
---

# Exercise #3: Eₘ Calculation
In this exercise, we will perform a __Eₘ calculation__ (redox titration over a defined Eₕ range) using a protein file using **MCCE4** .

---
## Background
__Cytochrome c__ is a small protein that transports electrons in mitochondria to facilitate the synthesis of ATP. Its redox potential plays an important role in its function. The regulation of the cytochrome c redox potential can be explained by continuum electrostatic analysis.
In this example tutorial we will use the cytochrom c stucture from [PDB ID: 1AKK](https://www.rcsb.org/structure/1AKK). The experimental Eₘ of Cytochrome c is __260 mV__. 
 
 __Reference:__ [Junjun Mao, Karin Hauser, and M. R. Gunner, How Cytochromes with Different Folds Control Heme Redox Potentials, Biochemistry 2003, 42(33), 9829–9840](https://pubmed.ncbi.nlm.nih.gov/12924932/)

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
> **We strongly recommend** to run `p_info` to inspect an unfamiliar PDB file and verify if it is compatiblity with __MCCE4__ prior to performing a simulation.
> ```bash
> p_info 1akk.pdb
> ```

## 2. Perform Eₘ using `run_mcce4`
The easiest way to run a __MCCE4__ simulation is with `run_mcce4`.
It is preset to run a full simulation (ending with a titration) and return the Eₘ of titratable residues into one of its output files called `pK.out` upon successful completion.

```bash
run_mcce4 1akk.pdb -type eh -initial 0 -interval 60 -n 15
```

- The conformer occupancies are in file `fort.38`.
- The net charge is in file `sum_crg.out`.
- The calculated Eₘs are in file `pK.out`

{: .note }
See [here](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/custom_runs/) for more details on what's exactly happening when running this `run_mcce4` or customizing runs! 


## 3. Interpret Eₘ results
A trimmed version of the pKₐ/Eₘ report is in file `pK.out`, which contains the calculated pKₐ/Eₘ values for titratable side chains. You can see the full report in pK_extended.out. 
- __pKa/Em__ : Calculated __MCCE4__ pKₐ/Eₘ values
- __n (slope)__ : Slope of titration curve (extrapolated from `fort.38`) and the Henderson-Hasselbalch equation.
- __1000×chi2__ : 1000 times the chi-squared value. Higher the number, the less accurate the result.

```
cat pK.out
```

You will see the calculated Eₘ for heme is __247 mV__

{: .text-center }
<img width="800" height="400" alt="HEM Em curve" src="https://raw.githubusercontent.com/Hajaribrahim/EmPlots/refs/heads/main/Figure_1.png" />

The graph shows the electron occupancy of HEM as a function of redox potential (Eₕ). The HEM group is mostly unoccupied at low potentials, becomes partially reduced around 180–360 mV, and fully reduced at high potentials. The sigmoidal curve indicates the midpoint potential (Eₘ) where HEM is 50% occupied.

## Understanding Redox Titration Curves and Output Files

### Example 1: Redox-active center (HEME)
The heme group is the primary redox-active site and shows a clear sigmoidal titration curve. Its electron occupancy transitions from 0 to 1 as Eₕ increases, allowing MCCE to determine a well-defined midpoint potential (Eₘ).
This behavior is reflected in:
- __A finite Eₘ value in__ `pK.out`
- __A smooth transition in electron occupancy in__ `sum_crg.out`.
- The slope parameter (n ≈ 1) indicates a single-electron transfer.

### Example 2: Acidic residue (Asp or Glu)
Some acidic residues appear in `pK.out` with values such as:

> ```bash
> Em > 840
> ```

- This indicates that the residue does not undergo a redox-linked transition within the sampled Eₕ range. Its protonation state remains effectively constant, so a meaningful midpoint potential cannot be determined.

### Example 3: Basic residue (Lys or Arg)
Similarly, basic residues may appear as:

> ```bash
> Em < 0
> ```

- This means the residue remains fully protonated across all redox conditions. These residues are not redox-active and do not respond directly to changes in Eₕ.

### Example 4: Redox–proton coupled residue

Some residues do not have a defined Eₘ in pK.out, yet their protonation state changes as the heme is oxidized or reduced. 
This behavior can be observed in `sum_crg.out`.
- Such residues are redox–proton coupled: they do not undergo electron transfer themselves, but their protonation is energetically linked to the redox state of the heme.


{: .note }
> **Optional Step:** To analyze the ionization energy of heme at the midpoint:
> ```
> mfe.py HEM+A0105_
> ```
>
> A more detailed explanation of mfe.py program can be found here [MFE Tutorial](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/mfe_tutorial/)


## Benchmark Eₘ (Heme electrochemistry)

- [Zheng, Z.; Gunner, M. R. Analysis of the electrochemistry of hemes with Ems spanning 800 mV. Proteins 2009, 75, 719-734. DOI: 10.1002/prot.22282](https://pmc.ncbi.nlm.nih.gov/articles/PMC2727069/)

- [Mao, J.; Hauser, K.; Gunner, M. R. How cytochromes with different folds control heme redox potentials. Biochemistry 2003, 42(33), 9829-9840](https://pubmed.ncbi.nlm.nih.gov/12924932/)

### For your information
__Heme acids:__ The heme in cytochrome C has two ligands HIS18 and MET80.
They behave differently than HIS and MET so we must rename them. step1.py can handle HIS, MET, and CYS if they are the ligands to heme.


__HEM and HIS__ 
are treated differently in cytochrome c, one axial ligand is histidine (His18) while the other is methionine (Met80). The Coordinates Fe via its imidazole nitrogen which does not change oxidation state and doesn’t accept or donate electrons. However, it does influence Eₘ indirectly by modifying the ligand field,altering the electrostatic stabilization of Fe³⁺ vs Fe²⁺ and Possibly undergoing protonation/deprotonation, which is coupled to redox chemistry
Thus, His is treated differently because it contributes electrostatically, not electronically.
