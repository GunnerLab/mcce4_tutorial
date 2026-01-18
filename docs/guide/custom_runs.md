---
title: Customizing Runs
nav_order: 4
parent: Guide
permalink: /docs/guide/custom_runs/
layout: default
---
# MCCE4 Customing Runs

By default, each step of MCCE has certain parameters set to it. For example, step 3 defaults to the Gunner Lab's in-house Poisson-Boltzmann solver, NGPB, though Delphi and ZAP are available to those with the respective OpenEye licenses. But what if the user wants non-default settings?

## Option A: ```run_mcce4```
Let's take a look at what is actually happening when you use **"run_mcce4"**!

__```run_mcce4``` uses the following parameters by default:__ 
```
step1.py {input_pdb} -d 4 –-dry
step2.py -l 1 -d 4
step3.py -d 4
step4.py --xts -i 7 -n 1
```

### Step 1 - Convert PDB to MCCE PDB 
Checks for inconsistencies between the PDB file and MCCE topology files, converts PDB file into a suitable input PDB for MCCE 
```
step1.py {input_pdb} -d 4 –-dry
```

- ```-d```   : Protein dielectric constant for PBE solvers; default: 4.0.
- ```--dry```: Delete all water molecules; default: False.

### Step 2 – Make side chain conformers 
This step makes alternative side chain locations and ionization states 
```
step2.py -l 1 -d 4
```

- ```-l``` : Conformer Generation Level -> 1: conformer level 1: quick, 2: medium, 3: comprehensive; default: 1.
- ```-d``` : Protein dielectric constant for PBE solvers; default: 4.0.

### Step3.py – Make energy table 
Calculates conformers self-energy and pairwise interaction table. 
```
step3.py -d 4
```

- ```-d``` : Protein dielectric constant for PBE solvers; default: 4.0.

### Step4.py – Simulate a titration with Monte Carlo sampling 
This step simulates a titration and writes out the conformation and ionization states of each side chain at various conditions. 
```
step4.py --xts -i 7 -n 1
```

- --xts: Enable entropy correction; default: false
- -i   : Initial pH/Eh of titration; default : 0.0
- -n   : Number of steps of titration; default 15

---
In order to see the all of the available parameters for ```run_mcce4``` you can use the help option: 
```
run_mcce4.py -h
```

The output should look like:
```
usage: run_mcce4 [-h] [--custom CUSTOM] [--sbatch] [-d epsilon] [--wet] [-l level] [-s solver] [-p processes] [-tmp tmp_folder] [-ftpl ftpl_folder] [-salt salt_concentration] [-vdw_relax vdw_R_relaxation] [--fly]
                 [--skip_pb] [--old_vdw] [--refresh] [--debug] [-type {ph,eh}] [-initial initial ph/eh] [-interval interval] [-n steps] [--ms]
                 pdb

Run MCCE4 either via the legacy default 4-step pipeline (no flags),
a flag-driven pipeline (if flags are provided), or with a custom SLURM shell script.

Rules:
- --custom may only be combined with the positional PDB and optional --sbatch.
- --sbatch can only be used together with --custom.
- If any other flags are present with --custom, the command will error.

Examples:
  # Legacy default MCCE4 simulation:
  run_mcce4 myprotein.pdb

  # Flag-driven:
  run_mcce4 myprotein.pdb -d 6 --wet
  run_mcce4 myprotein.pdb -d 6 -l 2 -s delphi -p 5 -tmp scrap --fly -salt 0.2 -type ph -initial 0 -interval 1 -n 15

  # Custom script:
  run_mcce4 myprotein.pdb --custom submit_mcce4.sh
  run_mcce4 myprotein.pdb --custom submit_mcce4.sh --sbatch


positional arguments:
  pdb                   Input PDB file (linked to 'prot.pdb')

options:
  -h, --help            show this help message and exit
  --custom CUSTOM       Optional shell script to run instead of default/flag pipelines
  --sbatch              Use sbatch to submit the custom shell script (non-blocking)
  -d epsilon            Global inner dielectric for Steps 1–3 (default: 4)
  --wet                 Step1: run WET (omit --dry) (default is DRY)
  -l level              Step2: conformer level (default: 1)
  -s solver             Step3: PBE solver (default: ngpb)
  -p processes          Step3: Number of CPUs to use
  -tmp tmp_folder       Step3: Temporary PBE data directory
  -ftpl ftpl_folder     Step3: ftpl folder; default is 'param/' next to mcce executable.
  -salt salt_concentration
                        Step3: salt concentration in mol/L (default: 0.15).
  -vdw_relax vdw_R_relaxation
                        Step3: relax vdw R by ±value (default: 0).
  --fly                 Step3: do-the-fly rxn0 calculation
  --skip_pb             Step3: run vdw/torsion only; skip PB.
  --old_vdw             Step3: use old vdw function calculations.
  --refresh             Step3: recreate *.opp and head3.lst from step2_out.pdb and *.raw.
  --debug               Step3: print debug info and keep PB solver tmp.
  -type {ph,eh}         Step4: titration type (maps to '-t'). Default: ph
  -initial initial ph/eh
                        Step4: initial pH/Eh (maps to '-i'). Default: 0.0
  -interval interval    Step4: titration interval pH or mV (maps to '-d'). Default: 1.0
  -n steps              Step4: number of titration steps (maps to '-n'). Default: 15
  --ms                  Step4: enable microstate output (maps to '--ms').
```

## Option B: Modular MCCE Steps 
You may run each of the four core [__MCCE4 mechanism__](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/mechanism) steps modularly.
To alter the options of each step1, simply use the flag options built in each step!

To see more of our options, let's look at the output of ```step1.py -h```: 

```
usage: step1.py [-h] [--norun] [--noter] [-e /path/to/mcce] [-u Key=Value] [-d epsilon] [-load_runprm prm_file] [--dry] pdb

Run mcce step 1, premcce to format PDB file to MCCE PDB format.

positional arguments:
  pdb

options:
  -h, --help            show this help message and exit
  --norun               Create run.prm but do not run step 1; default: False.
  --noter               Do not label terminal residues (for making ftpl); default: False.
  -e /path/to/mcce      mcce executable location; default: mcce.
  -u Key=Value          User customized variables; default: .
  -d epsilon            protein dielectric constant for PBE solvers; default: 4.0.
  -load_runprm prm_file
                        Load additional run.prm file, overwrite default values.
  --dry                 Delete all water molecules; default: False.
```

{: .note }
>So, if we wanted to start step 1 with an alternate version of MCCE, a dielectric constant of 8, and keeping explicit water molecules, we could >change the command to:
>
>```
>step1.py {input_file.pdb} -e /home/other_mcce -d 8
>```

## Option C: submit_mcce4.sh
Given it can be tedious to submit these modular instructions over and over, a slurm scheduler file named ```submit_mcce4.sh```.
To utilize this file, copy ```MCCE4-Alpha/schedulers/submit_mcce4.sh``` to the local directory and customizing it to fit the desired job. 

__Let's look a portion of the script:__

```
# Input and Output:
input_pdb="prot.pdb"    # (INPDB)

# Set MCCE4 Parameters
MCCE_HOME="/home/MCCE4-Alpha"
USER_PARAM="./user_param"
EXTRA="./user_param/extra.tpl"
TMP="/tmp"
CPUS=1

# Step control flags
step1="t"               # STEP1: pre-run, pdb-> mcce pdb  (DO_PREMCCE)
step2="t"               # STEP2: make rotamers            (DO_ROTAMERS)
step3="t"               # STEP3: Energy calculations      (DO_ENERGY)
step4="t"               # STEP4: Monte Carlo Sampling     (DO_MONTE)
step_clean="t"          # Clean PBE data                  (BACKUP CLEAN) : Set to f if step3 --debug option is used

# Optional step controls
stepM="f"               # Generate Partial Membranes                    : If true, user MUST satisfy condidtions of stepM.sh, which can be be obtained on MCCE4/inhouse/stepM.sh
stepA="f"               # Run a custom script between step1 and step2   : If true, user MUST satisfy condidtions of their custom script
stepB="f"               # Run a custom script between step2 and step3   : If true, user MUST satisfy condidtions of their custom script
stepC="f"               # Run a custom script between step3 and step4   : If true, user MUST satisfy condidtions of their custom script

# MCCE Simulation
STEP1="step1.py -d 4 --dry"
STEP2="step2.py -d 4 -l 1"
STEP3="step3.py -d 4 -s delphi -p $CPUS -t \$TMP"
STEP4="step4.py --xts -i 7 -n 1"

# Optional MCCE script locations
STEPM="/path/to/stepM.sh"         # Optional StepM: Bash script
STEPA="/path/to/stepA_script.py"  # Optional StepA: Python script to run between step1 and step2.
STEPB="/path/to/stepB_script.py"  # Optional StepB: Python script to run between step2 and step3.
STEPC="/path/to/stepC_script.py"  # Optional StepC: Python script to run between step3 and step4.

# NOTE: User is responsible to precheck if custom scripts work properly and efficiently
```

- **Make sure that "MCCE_HOME" is set to a version of MCCE that exists in your home directory!**
- "Set MCCE4 Parameters" controls which version of MCCE is being used.
- "Step control flags" allow for steps to be turned on and off between runs.
- "Optional step controls" allow for custom scripts to be run between steps, and partial membrane generation
- "MCCE Simulation" is where each step is customized- use "step[1-4].py -h" to see options for each step
- Lastly, the paths to any custom scripts are referenced

Then, submit_mcce4.sh can be executed:

```
chmod +x submit_mcce4.sh # make this file an executable
./submit_mcce4.sh # execute the submit shell
```

{: .important }
> We recommend using custom submit shells in conjunction with the __MCCE4-Alpha__ batch run program (pro_batch).
> [Learn about this functionality here.](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/batch_runs/)
> This program can accept a -custom flag and a local shell script to execute across multiple input structures.
