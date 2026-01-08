---
title: Microstate Analysis
parent: Guide
nav_order: #
permalink: /docs/guide/MCCE_Microstate_Analysis/
layout: default
---

# Microstate Analysis in Lysozyme

## Background

__What Is a Protonation Microstate?__

MCCE uses Monte Carlo (MC) sampling to generate a Boltzmann distribution of cofactor, residue side-chain, and ligand positions and protonation states. A __microstate__ is one specific combination of these positions and protonation states

__Key distinctions:__

__Microstate:__
One exact assignment of protonation, tautomer, and side-chain conformer states.

__Protonation microstate:__
A reduced representation retaining only charge states (−1, 0, +1, etc.), collapsing many conformational microstates into a single protonation pattern.

__Tautomers:__
Protonation microstates with the same net charge but different proton locations. Proteins, therefore, exist not in a single protonation configuration, but in an ensemble of protonation microstates whose distribution depends on pH, ligands, redox state, and local electrostatics 

__Why Protonation Microstates Matter?__

1. Presence of __low-probability__ but functionally relevant higher energy states
2. Coupling between protonation events at distant residues
3. Distinction between lowest-energy and highest-probability states

__Microstate analysis enables:__

1. Provide the possible __charge state__ of each ionizable residue in a given set of microstates
2. Mapping of __proton transfer__ pathways
3. Quantification of __long-range electrostatic coupling__

__How MCCE Generates Protonation Microstates?__

__Degrees of Freedom__
1. The protein __backbone is fixed__
2. Each titratable residue is assigned multiple conformers
3. Different proton positions (e.g., His tautomers)
4. Optional rotamers and explicit waters
5. Each conformer has a precomputed energy
6. A microstate selects one conformer per residue.
  
__Monte Carlo Sampling__: MCCE uses grand-canonical __Monte Carlo (GCMC)__ sampling:
1. __Randomly__ select residues and trial __conformers__
2. Accept or reject moves using the __Metropolis–Hastings__ criterion
3. Millions of trial microstates are explored; only accepted microstates contribute to the __Boltzmann ensemble__. 
 
__Storage of Microstates (ms_out Files)__: Because storing every microstate explicitly is infeasible, MCCE uses a __ticker-tape__ representation.
  
__Constructing Protonation Microstates__
The ms_protonation tool performs the following reduction:
1. __Fixed residues:__ Residues with a single conformer are assigned a constant charge
2. __Free residues:__ Charge states extracted from conformer identity
3. __Charge vectors:__ Each microstate is mapped to a vector of charges
4. __Aggregation:__ Conformational microstates with identical charge vectors are grouped
5. __Weighting:__ Each protonation microstate is weighted by its MC acceptance count
6. The result is a unique protonation microstate ensemble, each with: __Net charge__, __Probability__, and __Underlying conformational degeneracy__
 
__Weighted Correlation Analysis of Protonation States:__

Protonation of residues is often not independent. Electrostatic coupling means that protonation at one site can stabilize or destabilize protonation at another. __Positive correlation__ is residues protonate together, __Negative correlation__ is protonation of one disfavors the other, and __Near zero__ mean independent behavior


>__Reference:__ [Khaniya, Umesh and M. R. Gunner, *Phys.Chem.* B __2022__ Mar 28; 126(13): 2476-2485](https://doi.org/10.1021/acs.jpcb.2c00139)


---
## MCCE Calculation to get the Microstate file
MCCE expects to run in a **single directory** containing only one calculation, with four sequential steps. We recommend creating a new directory for each calculation that you run. 
```
 mkdir pms_analysis_test
 cd ms_analysis_test
```
Download the PDB file for 4LZT:
```
 getpdb 4lzt
```
Run p_info 
```
p_info 4lzt.pdb
```

---
## Run the Customized MCCE calculation 
Since we need the microstate file, we must use the custom command in step 4 to run the calculation. 
- Adding "nohup" before the command and "&" after allows the calculations to run uninterrupted in the background of the terminal.

```
run_mcce4 4lzt.pdb -initial 7 -interval 1 -n 1 --ms
```

--ms                  Enable microstate output; default: False.

The calculation takes a few minutes. “nohup.out” will update as the calculation runs; take a look for confirmation of completed steps.

```
cat nohup.out
```
__Confirm the output files__

When the run completes, confirm ```head3.lst``` and ```ms_out/pH7.00eH0.00ms.txt``` files are exist in the running directory. These are __required inputs__ for the microstate analysis program.


## Prepare the microstate Analysis

__Prerequisites__
•	```MCCE4-Tools``` installed
•	```ms_protonation``` available in your PATH

# Installation MCCE4-Tools


## ✅ Step 1. Clone the repository to a desired place on your computer (referred to as "clone_dir"):
   * Git clone MCCE4-Tools to a desired place on your computer (copy & pasted this command and press Enter):
     ```
     git clone https://github.com/GunnerLab/MCCE4-Tools.git; cd MCCE4-Tools;
     ```

## ✅ Step 2. CHANGE 'clone_dir' to your path!
Add the clone's path to your .bashrc (.bash_profile) file, save it, then "dot" or source the file:

 ```export PATH="clone_dir/MCCE4-Tools/mcce4_tools:$PATH"```


## ✅ Step 3. Verify availability
To verify the successful installation and check the availability of the ```ms_protonation``` program, run the following commands
  
 ```
 which MCCE4-Tools
 which ms_protonation
```

__Input Files (Required)__

The following files must be present in the working directory:

__Parameter file:__ ```params.crgms```

__MCCE output:__ ```head3.lst```

__MCCE Microstate output file in:__ ```ms_out/pH7.00eH0.00ms.txt```

For more help, run the following command
```ms_protonation -h```


# Run the microstate analysis program

Once your parameter (.crgms) file is ready, run the following command to execute the program.

```
ms_protonation params.crgms
```

It will take a few minutes to finish the run

__Output directories__
The output directory name also depends on the microstate file name. For example, if your microstate file name is ```ph7.00eh0.00.txt```, the output directory would be ```crgms_corr_ph7.00eh0.00```.
```
cd crgms_corr_ph7.00eh0.00
```

__Data outputs:__ Following outputs will be in the output directory

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

 
## Parameter File (*.crgms) Overview

The parameter file controls which residues are analyzed, how correlations are computed, and what outputs are generated.
Key Parameters
__Required__
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


<!--
Step 8: Understand the Outputs
8.1 Charge Microstate Distributions
Files such as:
crgms_logcount_vs_E.png
crgms_logcount_vs_lowestE.png
show:
How many unique protonation microstates exist
How microstates are distributed by energy
Separation between the lowest-energy and most-probable states
 
8.2 Correlation Heatmap
corr.png
This plot shows the weighted Pearson correlation between Glu35 and Asp52:
Positive correlation → protonation states rise and fall together
Negative correlation → one protonates while the other deprotonates
For lysozyme, Asp52 is typically protonated while Glu35 remains deprotonated near neutral pH, reflecting their catalytic roles.
 
8.3 CSV Data Tables
all_res_crg_status.csv
→ Protonation statistics for all residues
crg_count_res_of_interest.csv
→ Detailed microstate counts for Glu35 and Asp52
These files are ideal for:
Custom plotting
Statistical analysis
Cross-pH comparisons

Output files direcotry
mcce_dir
Directory containing the MCCE run, including the ms_out subdirectory.
Default: current working directory.
mcce_dir = 4lzt
output_dir
Subdirectory (within mcce_dir) where all analysis outputs are written.
Default: crgms_corr.
output_dir = crgms_corr

 
Step 9: Interpreting Results (Lysozyme)
Key observations you should see:
Only a small number of protonation microstates have significant probability
Glu35 and Asp52 show strong coupling
Lowest-energy microstates are not always the most populated
Entropy plays a significant role in microstate selection
These features are consistent with lysozyme’s known catalytic mechanism and validate the microstate approach.
 
Common Pitfalls
❌ Running ms_protonation without head3.lst
❌ Using an incorrect msout_file name
❌ Forgetting the trailing underscore (_) in residue IDs
❌ Over-restricting residue_kinds in large systems
-->

