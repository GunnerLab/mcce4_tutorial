---
title: 5 - Hydrogen-Bond Microstates
parent: Quick Start Tutorial
nav_order: 5
layout: default
permalink: /docs/tests/ex5/
---

# Exercise #5: Obtaining H-bonding pairs and microstates data
In this exercise, we will run a custom simulation on 4LZT and process its microstates ensemble to produce hydrogen-bond networks using **MCCE4**.

---
## 0. Pre-requisite:
Ensure you have the conda environment for ```mc4``` activated.
```
conda activate mc4
```

Ensure you have installed __MCCE4-Tools__. If not, please follow [these steps](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/MCCE4_Tools).

## Tool: ms_hbnets
This tool processes a file in the ms_out subfolder of a simulation (a.k.a. 'the msout file'), for H-bonding microstates and H-bonding pairs, outputting their count and occupancies in their respective files, e.g. hb_pairs_pHeH.csv and hb_states_pHeH.csv.  

  * __Definitions__:

   - __H-bonding pairs__: The pairs of Hydrogen donor and acceptor conformers
   - __H-bonding microstates__: The Monte Carlo sampling microstates that contain any of the structural Hydrogen-bonding pairs

### Help on the tool: `ms_hbnets -h`
```
usage: ms_hbnets
       ms_hbnets --load_states    # to get hb states instead of pairs
       ms_hbnets -ph 5
       ms_hbnets -n_states 30000
       ms_hbnets --run_checks     # + -mcce_dir, -ph, -eh if needed; all other options: ignored

Gather the H-bonding conformer pairs and states occupancies
from the microstates file given a mcce dir, pH & Eh.

options:
  -h, --help          show this help message and exit
  -mcce_dir MCCE_DIR  MCCE run directory; Default: .
  -ph PH              Titration pH; Default: 7
  -eh EH              Titration Eh; Default: 0
  -n_states N_STATES  Number of hb states to return, possibly; Default: 25000
  -v, --verbose       To output more details; Default: False
```

## 1. Prepare the directory:
Enter the working directory for this exercise:
```bash
 cd mcce_workflows
 mkdir ex5; cd ex5
```

## 2. Get the pdb file & run p_info:
```
 getpdb 4lzt
 p_info 4lzt.pdb
```

## 3. Run a full simulation at pH7:
```
 run_mcce4 4lzt.pdb -initial 7 -n 1 --ms
```

## 4. Run `detect_hbonds` (another tool in MCCE4-Tools):
`ms_hbnets` uses the output of `detect_hbonds`, which works on a pdb ('step2_out.pdb' by default), and therefore, returns all _structural_ H-bonding donor and acceptors pairs; its main output is 'step2_out_hah.txt'.
```
 detect_hbonds  # backbone atoms are included by default, add flag --no_bk to exclude them
```

## 5. Run `ms_hbnets`:
Since we are using the default options, the tool will look for the 'step2_out_hah.txt' file in order to process it for the current pH.
```
 ms_hbnets
```

{: .note }
> The outputs of `ms_hbnets` are pH-dependent as some conformers may not be free at all pH points.

## Main outputs of `ms_hbnets`:
Six files that retain the 'pHeH' string of the msout file name in use to indicate they are pH-dependent.

### hah_pHeH.txt
This file is the reduced version of the output of `detect_hbonds` where these pairs are filtered out: 
 - pairs of backbone conformers
 - pairs with one or more 'always off' conformers
 - pairs with one or more conformers with 0 occupancy

### expanded_hah_pHeH.csv
This file is both a reduced and an expanded version of the 'step2_out_hah.txt' file.

  * Reduced:
  The reduced file hah_pHeH.txt is the input to the function that creates the expanded file.

  * Expanded:
  Extra columns flag whether a conformer is free or not, which leads to a mapping of conformer indices to H-bonds matrix indices ('Mi', 'Mj'). These two columns, also present in the pairs file can be used to retrieve the coordinates of the donors and acceptors atoms, for example.

### hb_states_pHeH.csv
This file lists each H-bonding microstate energy, count and occupancy.
 - Column names: 'state_id', 'averE', 'count', 'occ'
 - Column types: string, float, integer, float
 - Example (shortened):
 ```
  # Data for the 26,014 saved hb_states whose sum count represents 5.25% of the state space (1,800,000)
  ix,state_id,averE,count,occ
  5923,"(HIS+1A0015_006,THR01A0089_003),(SER01A0050_003,ASP-1A0048_010),(THRBKA0051_000,SER01A0060_005)[...]",-0.0,40,2.2e-05
 ```
So, 'state_id' is a string of conformer identifier pairs (tuples).

### hb_states_pairs_pHeH.csv
This file lists the effective count and occ of the pairs in all state_id of the above hb_states file.
 - Column names: 'Mi', 'Mj', 'donor', 'acceptor', 'count', 'occ'
 - Column types: integer, integer, string, string, integer, float
 - Example (shortened):
 ```
 # States hb_pairs; last 2 columns: state count, occ
 Mi,Mj,donor,acceptor,count,occ
 219,375,LYS+1A0096_002,HISBKA0015_000,94469,0.999048
 384,81,ARGBKA0045_000,ASN01A0044_001,93724,0.991170
 124,382,GLN01A0057_001,ALABKA0042_000,93558,0.989414
 124,388,GLN01A0057_001,ASPBKA0052_000,93558,0.989414
 ```

### hb_pairs_pHeH.csv
This file lists the count and occupancy of each H-bonding _conformer_ pairs found in the entire state space.
 - Columns names: 'Mi', 'Mj', 'donor', 'acceptor', 'count', 'occ'
 - Column types: integer, integer, string, string, integer, float
 - Example (shortened):
 ```
 Mi,Mj,donor,acceptor,count,occ
 22,25,SER01A0060_005,THR01A0069_003,1169166,0.974305
 ```

### hb_pairs_res_pHeH.csv
This file lists the count and occupancy of each H-bonding _residue_ pairs found in the hb_pairs file. As this file is likely smaller than the conformer-based file above, it is more suitable for importing in Cytoscape, for example.
 - Columns names: 'res_d', 'res_a', 'count', 'occ'
 - Column types: string, string, integer, float
 - Example (shortened):
 ```
res_d,res_a,count,occ
R_A45,N_A44,1784729.0,0.991516
 ```

{: .important }
> __ms_hbnets is 'W.I.P' (work in progress)__
> 
> The 'Mi','Mj' columns provide a 'key' to, for example, retrieve the coordinates of donors and acceptors, if the positions were needed in a graph (network) analysis, which is not yet included in the tool.

### TODO:
  - Add network analysis using hb_states file
  - Add contribution from Jose to show how to obtain a Cytoscape graph using the hb_pairs_res file.
