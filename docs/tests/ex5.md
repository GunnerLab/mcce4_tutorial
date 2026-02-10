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
  --load_states       Include to load the H-bonding states instead of the H-bonding pairs (default)
  -n_states N_STATES  Number of H-bonding states to return, possibly (no effect without --load_states); Default: 25000
  --run_checks        Perform checks on main outputs; Default: False
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
> The outputs of `ms_hbnets` are pH-dependent as some conformers may not be free at all pH points. this is why the output files describe below retain the "pHeH" string found in the corresponding 'msout file' selected by the `-ph` and `-eh` command line options.

## Main outputs of `ms_hbnets`:
Six files that retain the 'pHeH' string of the msout file name in use to indicate they are pH-dependent.  
__Note__: All 'hb_' files are sorted descendingly by count.

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
This file lists the identifier, average energy, count and occupancy of each H-bonding microstate saved according to the `n_states` option value (default: 25,000):
 - Column names: 'state_id', 'averE', 'count', 'occ'
 - Column types: string, float, integer, float
 - Example (shortened):
 ```
  # Data for the 22,847 saved hb_states whose sum count represents 5.57% of the state space (83,550/1,500,000)
  state_id,averE,count,occ
  "(HIS+1A0015_006,THR01A0089_003),(SER01A0050_003,ASP-1A0048_010),(THRBKA0051_000,SER01A0060_005)[...]",-0.0,40,2.2e-05
 ```
So, 'state_id' is a string of conformer identifier pairs (tuples) and indicates which H-bonding pairs define the microstate (the next hb state will have a different set of pairs).

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
This file lists the count and occupancy of each H-bonding pairs found in the hb_pairs file grouped by _residue_ pairs. As this file is possibly smaller than the conformer-based file above, it is more suitable for importing in Cytoscape, for example.
 - Columns names: 'res_d', 'res_a', 'count', 'occ'
 - Column types: string, string, integer, float
 - Example (shortened):
 ```
res_d,res_a,count,occ
R_A45,N_A44,1784729.0,0.991516
 ```

## Method:
### Default behavior: Get the H-bonding pairs data
`ms_hbnets` reads each accepted state in the 'msout file' and increments the count of each H-bonding pairs in the reduced file,'hah_pHeH.txt', by the microstate count, then calculates the occupancy of each pair given the entire (conformer) microstate space using the sum of all counts, which is the size of the microstate space.

### Alternate behavior: Get the H-bonding states data (using `--load_states`)
Each accepted state in the 'msout file' is read for its count and energy, and all the H-bonding pairs found in a microstate constitute a H-bonding state, whose identifier/key is a string of comma-separated tuples of donor and acceptor pairs, e.g.: "(d1, a1),(d2,a2),[...]". The microstate count and energy are accumulated on unique H-bonding states. The sum of the H-bonding states count yields the size of the H-bonding space and this value is used to calculate the occupancy and average energy of each H-bonding state.  
The number of H-bonding states returned in this file is determined in accordance with the value for the `-n_states` command line option (default: 25,000). In the vast majority of cases, this is an upper bound: only in the rare case where all H-bonding microstates were unique would the returned number of saved H-bonding microstates match that value.

### Determining whether the `-n_states` option value is sufficient
The hb_states_pHeH.csv file starts with a commented line, e.g.:
```
# Data for the 22,847 saved hb_states whose sum count represents 5.57% of the state space (83,550/1,500,000)
```
Naturally, one may wonder whether such a low percentage of the entire state space represented by the saved H-bonding states in this file is sufficient. One way to find out is to compare the hb_pairs_pHeH.csv file (obtained when `ms_hbnets` is run without the `--load_states` option) with the hb_states_pairs_pHeH.csv file. __The number of returned states is sufficient if these two conditions are met__:
  1. The pairs' order is the same (the files are sorted the same way)
  2. The occupancies agree _at least_ to the 1‰ level (3rd digit past the decimal point)

### Example with hb_states produced using `--load_states -n_states 20000`:
The first 3 columns are from the hb_pairs file (absolute occupancy); the last 3 from the hb_states_pairs file. Since the two conditions are not met, the number of saved states is too low, and for a state space of 1,500,000 in this case, the default number of states to save (25,000) is adequate (not shown).

| indices |   count |occupancy | indices     | count | occupancy |
| :---    |    ---: |     ---: | :---        |  ---: |      ---: |
| 284,144 | 1492652 | 0.995101 | __258,247__ | 65077 | 0.99__4__635  |
| 258,247 | 1491164 | 0.994109 | __300,247__ | 65077 | 0.994635  |
| 300,247 | 1491164 | 0.994109 | __301,247__ | 65077 | 0.994635  |
| 301,247 | 1491164 | 0.994109 | __284,144__ | 65068 | 0.994498  |
| 295,68  | 1483643 | 0.989095 | 295,68      | 64747 | 0.989592  |

{: .important }
> __ms_hbnets is 'W.I.P' (work in progress)__
> 
> The 'Mi','Mj' columns provide a 'key' to, for example, retrieve the coordinates of donors and acceptors, if the positions were needed in a graph (network) analysis, which is not yet included in the tool.

### TODO:
  - Add network analysis using hb_states file
  - Add contribution from Jose to show how to obtain a Cytoscape graph using the hb_pairs_res file.
