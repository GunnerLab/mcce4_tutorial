---
title: Microstate Analysis
parent: Guide
nav_order: 11
permalink: /docs/guide/MCCE_Microstate_Analysis/
layout: default
---

# Single pH Microstate Analysis in Lysozyme

## Background

__What Is MCCE Microstate?__

In MCCE, a microstate is a complete specification of a system’s state, defining the charge and position of all residues and ligands, including their conformations.

__What Is MCCE Protonation Microstate?__

A __protonation microstate__ specifies the protonation (charge) state of every acidic and basic residue. Multiple protonation microstates can share the same total charge but differ in the distribution of protons among residues (tautomers).

__Microstate analysis enables:__

- Provide the possible __charge state__ of each ionizable residue in a given set of microstates
- Quantification of __long-range electrostatic coupling__


---
 
## Parameter File (*.crgms) Overview

We ran Microstate analysis [here](https://gunnerlab.github.io/mcce4_tutorial/docs/tests/ex4/). using the default version of the ```params.crgms``` parameter file. You can control the analysis to which residues are analyzed, how correlations are computed, and what outputs are generated in ```params.crgms``` parameter file.

## Key Parameters

msout_file = ms_out/pH7eH0ms.txt

__Optional: list ionizable residues and exit__
list_head3_ionizables = false

__Residues included in correlation analysis__ or __residues of interest__

Explicit list of residues used for correlation analysis. Only residues listed here are included in the correlation matrix.
```
correl_resids = [GLUA0035, ASPA0052]
```
__Microstate File Selection__

Microstate file direcotry: ```cd ms_out/```

The filename typically encodes the pH and Eh values used in the MCCE run. For pH 7 and Eh 0, the file name ```pH7.00eH0.00ms.txt```
 
__Microstate Filtering Parameters__

Minimum occupancy threshold for including a unique protonation microstate. Microstates with probabilities below this value are discarded.
```min_occ = 0.000000001```

__n_top (optional)__
Limits the number of most-populated unique protonation microstates returned.
```n_top = 500```
 
__Residue Selection__

__residue_kinds:__ Filters which residue types are included when constructing protonation microstates. If omitted, commented out, or empty, all ionizable residues are included.

```residue_kinds = [ASP, PL9, LYS, GLU, HIS, TYR, NTR, CTR]```
 
__correl_resids__ or __residues of interest__
Explicit list of residues used for correlation analysis. Only residues listed here are included in the correlation matrix.


## Understand the Outputs

__Data outputs:__ The following outputs will be in the output directory

```
all_crg_count_resoi.csv
all_res_crg_status.csv
corr.png
crg_count_res_of_interest.csv
crgms_logcount_resoi.png
crgms_logcount_vs_E.png
crgms_logcount_vs_lowestE.png
enthalpy_dist.png
fixed_res_of_interest.csv
```

__Charge Microstate Distributions__
Files such as:
```
crgms_logcount_vs_E.png
crgms_logcount_vs_lowestE.png
```
__show:__
- How many unique protonation microstates exist
- How microstates are distributed by energy
- Separation between the lowest-energy and most-probable states
 
__Correlation Heatmap__
```
corr.png
```
This plot shows the weighted Pearson correlation between the two correlated residues:
- __Positive correlation__ → protonation states rise and fall together
- __Negative correlation__ → one protonates while the other deprotonates
For lysozyme, Asp52 is typically protonated, whereas Glu35 remains deprotonated at near-neutral pH, reflecting their catalytic roles.
 
__CSV Data Tables__
```
all_res_crg_status.csv
```
- Shows the protonation statistics for all residues

```
crg_count_res_of_interest.csv
```
- Shows the protonation statistics just for the chosen residues

__These files are ideal for:__
- Custom plotting
- Statistical analysis
- Cross-pH comparisons


## More about MCCE Microstate

__What is MCCE Microstate?__

A __microstate__ In MCCE, a microstate defines both residue and ligand charge and position. One exact assignment of protonation, tautomer, and side-chain conformer states.

__What Is a MCCE Protonation Microstate?__

__Protonation microstates__, which define the charge of every acidic and basic residue, will exist in many conformational states. The charge state identifies the net, total charge in the microstate.

__Tautomers:__

Protonation microstates with the same net charge but different proton locations. Proteins, therefore, exist not in a single protonation configuration, but in an ensemble of protonation microstates whose distribution depends on pH, ligands, redox state, and local electrostatics 

__Why Protonation Microstates Matter?__

- Presence of __low-probability__ but functionally relevant higher energy states
- Coupling between protonation events at distant residues
- Distinction between lowest-energy and highest-probability states

__Microstate analysis enables:__

- Provide the possible __charge state__ of each ionizable residue in a given set of microstates
- Mapping of __proton transfer__ pathways
- Quantification of __long-range electrostatic coupling__

__How MCCE Generates Protonation Microstates?__

__Degrees of Freedom__

- The protein __backbone is fixed__
- Each titratable residue is assigned multiple conformers
- Different proton positions (e.g., His tautomers)
- Optional rotamers and explicit waters
- Each conformer has a precomputed energy
- A microstate selects one conformer per residue.
  
__Monte Carlo Sampling__: MCCE uses grand-canonical __Monte Carlo (GCMC)__ sampling:

- __Randomly__ select residues and trial __conformers__
- __Accept or reject__ moves using the __Metropolis–Hastings__ criterion
- Millions of trial microstates are explored; only accepted microstates contribute to the __Boltzmann ensemble__. 
 
__Storage of Microstates (ms_out Files)__: Because storing every microstate explicitly is infeasible, MCCE uses a __ticker-tape__ representation.
  
__Constructing Protonation Microstates__
The ms_protonation tool performs the following reduction

- __Fixed residues:__ Residues with a single conformer are assigned a constant charge
- __Free residues:__ Charge states extracted from conformer identity
- __Charge vectors:__ Each microstate is mapped to a vector of charges
- __Aggregation:__ Conformational microstates with identical charge vectors are grouped
- __Weighting:__ Each protonation microstate is weighted by its MC acceptance count
- The result is a unique protonation microstate ensemble, each with: __Net charge__, __Probability__, and __Underlying conformational degeneracy__
 
__Weighted Correlation Analysis of Protonation States:__

Protonation of residues is often not independent. Electrostatic coupling means that protonation at one site can stabilize or destabilize protonation at another. __Positive correlation__ is residues protonate together, __Negative correlation__ is protonation of one disfavors the other, and __Near zero__ mean independent behavior


>__Reference:__ [Khaniya, Umesh and M. R. Gunner, *Phys.Chem.* B __2022__ Mar 28; 126(13): 2476-2485](https://doi.org/10.1021/acs.jpcb.2c00139)


 
## Common Pitfalls

❌ Running ms_protonation without __head3.lst__

❌ Over-restricting __residue_kinds__ in large systems

