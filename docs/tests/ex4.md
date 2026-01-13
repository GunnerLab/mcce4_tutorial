---
title: 4 - Charge Microstate Analysis
parent: Quick Start Tutorial
nav_order: 4
layout: default
permalink: /docs/tests/ex4/
---

# Exercise #4: Single-pH Charge microstate (MS) analysis on 4LZT

In this exercise, we will run a full simulation on 4LZT and process its microstates ensemble to produce 'charge miscrostates' for further analysis.

---
## 0. Pre-requisite:
You have installed MCCE4-Tools. If not, please follow [these steps](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/MCCE4_Tools).


## 1. Prepare the directory

Enter the working directory for this exercise:
```bash
cd mcce_workflows
mkdir ex4; cd ex4
```

Download the PDB file for 4LZT:
```bash
 getpdb 4lzt
```

A successful download should display the following message:
```bash
 [ INFO ] Download completed: 4lzt.pdb
```

{: .important }
> **We strongly recommend** to run `p_info` to inspect an unfamiliar PDB file and verify if it is compatible with MCCE4.
> ```bash
> p_info 4lzt.pdb > p_info.log
> ```


## 2. Run `run_mcce4`

The easiest way to run a mcce4 simulation is with `run_mcce4`. 
It is preset to run a full simulation (ending with a titration) and return the pKas of ionizable residues into one of its output files called "pK.out" upon successful completion.

```bash
run_mcce4 4lzt.pdb -initial 7 -interval 1 -n 1 --ms
```

__Confirm the output files__

When the run completes, confirm file 'head3.lst' and directory 'ms_out' exist. That directory should have a single .txt file with a name starting with 'pH7'; this file often referred to as __'the msout file'__. These are __required inputs__ for the microstate analysis program.

## 3. Run `ms_protonation` (a tool in MCCE4-Tools)

__Required command line arguement:__
The filepath of a parameter file with extension '.crgms'.  
Obtain a parameter file copy in this directory by running these commands:
```bash
CLONE=$(dirname $(dirname "$(python3 -c "import os, sys; print(os.path.realpath(sys.argv[1]))" "$(which ms_protonation)")")); 
echo "$CLONE"; 
cp $CLONE/cli_parameter_files/params.crgms . 
```

For more help, run the following command: `ms_protonation -h`


__Run the full command:__  
Once your parameter (.crgms) file is ready, run the following command to execute the program.

```
ms_protonation params.crgms
```

It will take a few minutes to completion.

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
If produces, the figure of the correlation matrix, 'corr.png' will indicate any correlation between residues.
