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
__Cytochrome c__ is a small mitochondrial protein that functions as an electron carrier in the respiratory chain, enabling ATP synthesis. Its redox potential is a critical determinant of this function and is regulated by the protein’s electrostatic environment, which can be analyzed using continuum electrostatics methods. In this example tutorial we will use the cytochrom c stucture from [PDB ID: 1AKK](https://www.rcsb.org/structure/1AKK). 

The experimental Eₘ of Cytochrome c is typically around __260 mV__ at pH 7.0.
 
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

```
Eh             pKa/Em  n(slope) 1000*chi2
NTG+A0001_      <0.0
ASP-A0002_      >840.0
GLU-A0004_      >840.0
LYS+A0005_      <0.0
LYS+A0007_      <0.0
LYS+A0008_      <0.0
LYS+A0013_      <0.0
GLU-A0021_      >840.0
LYS+A0022_      <0.0
LYS+A0025_      <0.0
HIS+A0026_      <0.0
LYS+A0027_      <0.0
HIS+A0033_      <0.0
ARG+A0038_      <0.0
LYS+A0039_      <0.0
TYR-A0048_      >840.0
ASP-A0050_      >840.0
LYS+A0053_      <0.0
LYS+A0055_      <0.0
LYS+A0060_      <0.0
GLU-A0061_      >840.0
GLU-A0062_      >840.0
GLU-A0066_      >840.0
TYR-A0067_      >840.0
GLU-A0069_      >840.0
LYS+A0072_      <0.0
LYS+A0073_      <0.0
TYR-A0074_      >840.0
LYS+A0079_      <0.0
LYS+A0086_      <0.0
LYS+A0087_      <0.0
LYS+A0088_      <0.0
GLU-A0090_      >840.0
ARG+A0091_      <0.0
GLU-A0092_      >840.0
ASP-A0093_      >840.0
TYR-A0097_      >840.0
LYS+A0099_      <0.0
LYS+A0100_      <0.0
GLU-A0104_      >840.0
CTR-A0104_      >840.0
HEM+A0105_     231.204     1.023     0.005
PAA-A0105_      >840.0
PDD-A0105_      >840.0
```



From the results, __MCCE4__ predicts a heme redox potential of  __231.2 mV__ , indicating a stable redox behavior under the simulated conditions.
Only the heme group exhibits a well-defined midpoint redox potential (Eₘ) within the sampled electrochemical window, allowing reliable fitting of the titration curve and extraction of the slope and χ² values. Blank entries indicate residues whose redox or protonation states do not change within the sampled Eh range, preventing Nernst or Henderson–Hasselbalch fitting.

---


### Understanding Redox Titration Curves and Output Files



```sum_crg.out```

 - The  ```sum_crg.out``` file is used to generate redox titration curves (occupancy vs Eh). Only residues whose charge changes with Eh produce meaningful curves. In this system, the heme group (HEM+A0105_) shows a clear sigmoidal transition, allowing determination of Em, while all other residues remain flat and therefore do not have a defined redox midpoint.


### Redox-active center (HEME)
The heme group is the primary redox-active site and shows a clear sigmoidal titration curve. Its electron occupancy transitions from 0 to 1 as Eₕ increases, allowing MCCE to determine a well-defined midpoint potential (Eₘ).
This behavior is reflected in:
- __A finite Eₘ value in__ `pK.out`
- __A smooth transition in electron occupancy in__ `sum_crg.out`.


{: .text-center }
<img width="800" height="400" alt="HEM Em curve" src="https://raw.githubusercontent.com/Hajaribrahim/EmPlots/refs/heads/main/Figure_2.png" />

- The graph shows the electron occupancy of HEM as a function of redox potential (Eₕ). The HEM group is mostly unoccupied at low potentials, becomes partially reduced around 180–360 mV, and fully reduced at high potentials. The sigmoidal curve indicates the midpoint potential (Eₘ) where HEM is 50% occupied.

### Acidic residue (Asp or Glu)
Some acidic residues appear in `pK.out` with values such as:

> ```bash
> Em > 840
> ```

- This indicates that the residue does not undergo a redox-linked transition within the sampled Eₕ range. Its protonation state remains effectively constant, so a meaningful midpoint potential cannot be determined.

### Basic residue (Lys or Arg)
Similarly, basic residues may appear as:

> ```bash
> Em < 0
> ```

- This means the residue remains fully protonated across all redox conditions. These residues are not redox-active and do not respond directly to changes in Eₕ.
- Only the heme shows a true midpoint potential, while most amino acids either remain fully protonated/deprotonated or respond indirectly through redox-coupled protonation.


### Redox–proton coupled residue

Some residues do not have a defined Eₘ in pK.out, yet their protonation state changes as the heme is oxidized or reduced. 
This behavior can be observed in `sum_crg.out`.
- Such residues are redox–proton coupled: they do not undergo electron transfer themselves, but their protonation is energetically linked to the redox state of the heme.


{: .text-center }
<img width="800" height="400" alt="HEM Em curve" src="https://raw.githubusercontent.com/Hajaribrahim/EmPlots/refs/heads/main/coupling%20fig.png" />



{: .note }
> **Optional Step:** To analyze the ionization energy of heme at the midpoint:
> ```
> mfe.py HEM+A0105_
> ```
>
> A more detailed explanation of mfe.py program can be found here [MFE Tutorial](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/mfe_tutorial/)

---
## Benchmark Eₘ (Heme electrochemistry)

- [Zheng, Z.; Gunner, M. R. Analysis of the electrochemistry of hemes with Ems spanning 800 mV. Proteins 2009, 75, 719-734. DOI: 10.1002/prot.22282](https://pmc.ncbi.nlm.nih.gov/articles/PMC2727069/)

- [Mao, J.; Hauser, K.; Gunner, M. R. How cytochromes with different folds control heme redox potentials. Biochemistry 2003, 42(33), 9829-9840](https://pubmed.ncbi.nlm.nih.gov/12924932/)

--- 
### For your information
__Heme acids:__ in cytochrome C is coordinated by two ligands, HIS18 and MET80. Since they behave differently from standard HIS and MET residues, they must be renamed. step1.py can process HIS, MET, and CYS residues when they act as ligands to heme.

__HEM and HIS__ are treated differently in cytochrome c. One axial ligand is histidine (His18) and the other is methionine (Met80). Histidine coordinates the iron through its imidazole nitrogen, which does not change oxidation state and does not directly donate or accept electrons. However, it affects the redox potential (Eₘ) indirectly by altering the ligand field, influencing the electrostatic stabilization of Fe³⁺ versus Fe²⁺, and may undergo protonation or deprotonation coupled to redox changes. Therefore, His is considered primarily for its electrostatic contribution rather than electronic effects.


{: .note }
> Check out in customizing MCCE4 simulations here! [Customizing Runs](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/custom_runs/)!

