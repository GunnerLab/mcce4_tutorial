---
title: 4 - Microstate Analysis
parent: Quick Start Tutorial
nav_order: 4
layout: default
permalink: /docs/tests/ex4/
---

# Exercise #4: Single-pH 4LZT microstate (MS) analysis

In this exercise, we will a microstate analysis on a protien file using **MCCE4-Alpha**!

---

## Step 1 – Prepare the Calculation

First, enter the working directory for this exercise:

```bash
cd ~/mcce4_tests/ex4
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


## Step 2 – Run `run_mcce4`

The easiest way to run a mcce4 simulation is with `run_mcce4`. 
It runs through the first four steps and calculates pKas for each residue of the PDB file, saving them to "pK.out" upon successful completion of the fourth step.

```bash
run_mcce4 4lzt.pdb -initial 7 -interval 1 -n 1 --ms
```

__Confirm the output files__

When the run completes, confirm ```head3.lst``` and ```ms_out/pH7.00eH0.00ms.txt``` files are exist in the running directory. These are __required inputs__ for the microstate analysis program.

## Step 3 – Run `ms_protonation`

__Prerequisites__
•	```MCCE4-Tools``` installed
•	```ms_protonation``` available in your PATH

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
