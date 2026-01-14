---
title: 5 - Hydrogen-Bond Microstates
parent: Quick Start Tutorial
nav_order: 5
layout: default
permalink: /docs/tests/ex5/
---

# Exercise #5: Obtaining H-bonding pairs and microstates data

## 0. Pre-requisite:
Ensure you have the conda enviorment for ```mc4``` activated.
```
conda activate mc4
```

Ensure you have installed __MCCE4-Tools__. If not, please follow [these steps](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/MCCE4_Tools).

## Tool: ms_hbnets
This tools processes the msout file for H-bonding microstates and H-bonding pairs, outputing their count and occupancies in their respective files, e.g. hb_pairs_pH7eH0.csv and hb_states_pH7eH0.csv.  

H-bonding microstates
: The Monte Carlo sampling microstates that contain any of the structural Hydrogen-bonding pairs

H-bonding pairs
: The pairs of Hydrogen- donor and acceptor conformers

### Help on the tool: `ms_hbnets -h`
```
usage: ms_hbnets
       ms_hbnets -ph 5
       ms_hbnets -n_states 30000

Gather the H-bonding conformer pairs and states occupancies
        from the microstates file given a mcce dir, pH & Eh.

options:
  -h, --help          show this help message and exit
  -mcce_dir MCCE_DIR  MCCE run directory; Default: .
  -ph PH              Titration pH; Default: 7
  -eh EH              Titration Eh; Default: 0
  -n_states N_STATES  Number of hb states to return, possibly; Default: 25000
  -v, --verbose       To ouput more details; Default: False
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
`ms_hbnets` uses the output of `detect_hbonds`, which works on a pdb (step2_out.pdb by default), and therefore, returns all _structural_ H-bonding donor and acceptors pairs; its main output is 'step2_out_hah.txt'.
```
detect_hbonds
```

## 5. Run `ms_hbnets`:
Since we are using the default options, the tool will look for the 'step2_out_hah.txt' file in order to process it for the current pH.
```
ms_hbnets   # backbone atoms are included by default, add flag --no_bk to exclude them
```
{: .note }
> The outputs of `ms_hbnets` are pH-dependent as some conformers may not be free at all pH points.

## Main outputs of `ms_hbnets`:
Three csv files that retain the 'pHeH' string of the msout file name in use.

### expanded_hah_pH7eH0.csv
This file is both a reduced and an expanded version of the 'step2_out_hah.txt' file.

### Reduced
H-bonds of backbone conformer pairs, or of backbone and always fixed conformers are removed, as well as several columns from the master file are filtered out (though the pairs xyz coordinates are retained).

### Expanded
Extra columns flag whether a conformer is free or not, which leads to a mapping of conformer indices to H-bonds matrix indices ('Mi', 'Mj'). These two columns, also present in the pairs file are used to update the expanded file with the pairs data.
Other columns provide a way to reconcile/verify the correctness of a H-bonding network; for instance, columns 'dina' ("donor in acceptor list") or 'aind' ("acceptor in donor list") with values of 1 mean that in a graph these conformers would have at least two nodes.

### hb_states_pH7eH0.csv
This file lists each H-bonding microstate count and occupancy.
 - Column names: 'state_id', 'ms_count', 'ms_occ'.
 - Column types: string, integer, float.
 - Example (shortened):
 ```
 state_id,ms_count,ms_occ
 "(HIS+1A0015_006,THR01A0089_003),(SER01A0050_003,ASP-1A0048_010),(THRBKA0051_000,SER01A0060_005)",1827,0.0015225
 ```
So, 'state_id' is a string of conformer identifier pairs (tuples).

### hb_pairs_pH7eH0.csv
This file lists the effective count and occupancy of each structural H-bonding pairs found in the H-bond microstate ensemble.

 - Columns names: 'Mi','Mj', 'donor', 'acceptor', 'res_d', 'res_a', 'ms_count', 'ms_occ'.
 - Column types: integer, integer, string, string, string, string , integer, float.
 - Example (shortened):
 ```
 Mi,Mj,donor,acceptor,res_d,res_a,ms_count,ms_occ
 22,25,SER01A0060_005,THR01A0069_003,S_A60,T_A69,1169166,0.974305
 33,69,SER01A0100_003,LYSBKA0096_000,S_A100,K_A96,1008940,0.8407833333333333
 ```

{: .important }
> __ms_hbnets is 'W.I.P' (work in progress)__
> The 'Mi','Mj' columns provide a 'key' to, for example, retrieve the coordinates of donors and acceptors, if the positions were needed in a graph (network) analysis, which is not yet included in the tool.

__TODO:__ Contribution from Jose to show how to obtain a graph using the hb_pairs file.
