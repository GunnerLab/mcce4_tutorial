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
Ensure you have the conda enviorment for ```mc4``` activated.
```
conda activate mc4
```

Ensure you have installed __MCCE4-Tools__. If not, please follow [these steps](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/MCCE4_Tools).

## Verify availability
To verify the successful installation and check the availability of the ```ms_protonation``` program, run the following commands
  
 ```
 which MCCE4-Tools
 which ms_protonation
```

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


## 2. Run the Customized MCCE calculation 
Because we require the microstate file, we must use the following custom command to run MCCE4. 

```bash
run_mcce4 4lzt.pdb -initial 7 -interval 1 -n 1 --ms
```
--ms, enable microstate output, which is not written as an output file in the default MCCE4 run.

__Confirm the output files__

When the run completes, confirm that the file ```head3.lst``` and the directory ```/ms_out/pH7.00eH0.00ms.txt``` exist. That directory should have a single .txt file with a name starting with 'pH7'; this file is often referred to as __'the msout file'__. These are __required inputs__ for the microstate analysis program.

```
ls /ms_out
```

## 3. Run `ms_protonation` (a tool in MCCE4-Tools)
___Input Files (Required)__

The following input files must be present in the working directory to run the program ```ms_protonation```.

- ```head3.lst``` from __MCCE output__

- ```ms_out/pH7.00eH0.00ms.txt``` from __MCCE output Microstate file__ 

- ```params.crgms``` __Parameter file__ (To know more about the ```params.crgms``` file, please [see here](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/MCCE_Microstate_Analysis/)).

__How to get the ```params.crgms``` file?__ 

__Required command line argument:__
The filepath of a parameter file with extension '.crgms'.  
Obtain a parameter file copy in this directory by running these commands:
```bash
CLONE=$(dirname $(dirname "$(python3 -c "import os, sys; print(os.path.realpath(sys.argv[1]))" "$(which ms_protonation)")"));
echo "$CLONE";
cp $CLONE/mcce4_tools/tool_param/params.crgms ./new_params.crgms
```

For more help, run the following command: ```ms_protonation -h```


__Run the full command:__  
Once your parameter (.crgms) file is ready, run the following command to execute the program.

```
ms_protonation params.crgms
```

It will take a few minutes to complete.

__Output directories__
The output directory name would be ```crgms_corr_pH7.00eH0.00```. Run the following command to see the output files.
```
cd crgms_corr_pH7.00eH0.00/
ls -l
```

__Data outputs:__ The following outputs will be in the output directory (To know more about the output files, please [see here](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/MCCE_Microstate_Analysis/)).


```
all_crg_count_resoi.csv
all_res_crg_status.csv
corr.png
crg_count_res_of_interest.csv
crgms_logcount.png
enthalpy_dist.png
fixed_res_of_interest.csv
```


If it produces, the correlation matrix figure, 'corr.png', will indicate correlations among residues.
