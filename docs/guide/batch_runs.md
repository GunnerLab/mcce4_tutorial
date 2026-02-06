---
title: Multiple PDB Runs
nav_order: 7
parent: Guide
permalink: /docs/guide/batch_runs/
layout: default
---
# MCCE Multiple PDB runs

`pro_batch` is a program included in MCCE, under the folder MCCE_bin. It accepts a directory containing ".pdb" files, and runs MCCE with identical settings on each protein file.

By default, default_script.sh is created by pro_batch at run time, if it does not exist. The default scripts calls for steps 1-4 of MCCE to be run at level 1, assumes a dielectric constant of 8 with NGPB as the Poisson Bolztmann solver, and sets entropy control on. pro_batch can also accept custom shell scripts with the "-custom" flag. [Learn about creating and editing shell scripts here](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/submit_shell/).

```
pro_batch -h
```

```
usage: pro_batch [-h] [-custom CUSTOM] [--sbatch] [--check] input_path

Batch launch MCCE runs.

positional arguments:
  input_path      Path to a directory containing PDB files.

options:
  -h, --help      show this help message and exit
  -custom CUSTOM  Path to custom bash script.
  --sbatch        Run all jobs separately using the Slurm scheduler (sbatch).
  --check         Update book.txt status and exit.
```

Let's look at an example of how pro_batch can be used. First, create a directory, and fill it with protein files. Consider using **getpdb** to download PDB files directly from RCSB.org (getpdb included in MCCE4-Alpha/MCCE_bin). For this example, assume a directory called "source_files" containing a couple PDB files:

```
ls source_files
```

Output:
```
1ans.pdb  4lzt.pdb  4pti.pdb  9rat.pdb
```

Now, let's use pro_batch. 

```
pro_batch source_files
```

Output:
```
Found 4 new or missing runs: 1ANS, 4LZT, 4PTI, 9RAT

Launching 4 runs...
  -> Starting 4lzt...
Submitted batch job 10674
  -> Starting 4pti...
Submitted batch job 10675
  -> Starting 9rat...
Submitted batch job 10676
  -> Starting 1ans...
Submitted batch job 10677

Done. Monitor status in book.txt.
```

{: .note }
>If a file name ```submit_mcce4.sh``` does not already exist in the working directory,
> ```pro_batch``` will first pull it down from your MCCE4-Alpha clone's ```schedulers``` folder with the message:
>```Created default submit_mcce4.sh. Edit it then re-run.```

Let's take a look at the ```book.txt``` file:
```
Last Updated: 2026-02-05 20:03:23
PDB          Status   Last_Step
--------------------------------------
1ANS         r        Pending
4LZT         r        Pending
4PTI         r        Pending
9RAT         r        Pending

--------------------------------------
Legend:
 r : Ready or Running
 c : Completed (pK.out found)
 e : Error (Check run.log in directory)
```

To monitor the status of your batch runs, you can look at the generated ```book.txt``` file. However, first we will need to update it!
```
pro_batch pdbs --check
```

Output:
```Book status refreshed in book.txt.```

Now we see that ```1ANS``` successfully completed, but the rest of proteins are still in the ```Step3``` stage:
```
Last Updated: 2026-02-05 20:04:20
PDB          Status   Last_Step
--------------------------------------
1ANS         c        Completed
4LZT         r        In Step3
4PTI         r        In Step3
9RAT         r        In Step3

--------------------------------------
Legend:
 r : Ready or Running
 c : Completed (pK.out found)
 e : Error (Check run.log in directory)
```

# mcce_stat
Reviewing multiple protein runs can be cumbersome. To aid the user, "mcce_stat" is included. The directories created by pro_batch will contain the output files of each MCCE run. mcce_stat checks of these directories for "signal" files to check how each run is progressing, as of mcce_stat's runtime. 

```
mcce_stat

To see when PDB was run, reference mcce_timing.log in running directory. (pro_batch)

Completion: 7.50%
Directory step1         step2         step3     step4  Status
1ans      Exists        Exists                               
4pti      Exists
```

When all four steps are completed, the pKas of the selected protein will be available to see in "pK.out".

# book.txt

Before beginning the MCCE run, the program creates a file named "book.txt". book.txt is a text file containing a list names of proteins that can be edited to "turn off" proteins. This is what our new book.txt file looks like:

```
4pti
1ans
```

If we wanted to turn off both of these, we could edit book.txt to: 

```
4pti   x
1ans   c
```

Where "x" means exclusion, and "c" means completed. Attempting to run pro_batch now results in the following message:

```
pro_batch source_files/

book.txt found! Protein files identified in book.txt: 

4pti   x
1ans   c

No runnable proteins found. Remove 'c' or 'x' from lines in book.txt to make them runnable, add protein files to run, or delete book.txt to re-do all runs.

Aborting MCCE...
```

By marking book.txt, the user can choose to run any desired subset of proteins from the source directory's protein set.

# --sbatch

Part of using pro_batch is memory management. The default script begins with the following header, and is called when the --sbatch flag is active:

```
#!/bin/bash
#SBATCH --job-name=mcce4_run
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=12G                 # Adjust memory if needed
```

Be careful about how much memory each task is allowed to use so that it stats within your resources. Commands like "lscpu" can help gauge your computer's capacity. Of course, if additional resources are available, the "--mem" line can be edited to add the amount of memory dedicated. It is also possible to add processes to step 3 with the "-p" flag, such that each process independantly processes conformers. This can massively speed up step 3 if the resources are there to sustain it.

# Default Run Instructions

By default, pro_batch creates **pro_script.sh** to execute within each protein directory. To change these, we recommend editing pro_script.sh, including the name, and running pro_batch again using the **-custom** tag. These are the default instructions for a run:

```
# MCCE Simulation
STEP1="step1.py \$input_pdb -d 4 --dry"
STEP2="step2.py -l 1 -d 4"
STEP3="step3.py -d 4"
STEP4="step4.py --xts"
```

We anticipate the default settings for pro_batch will meet most users' needs, but the full power of pro_batch is realized with the **-custom** tag, which allows a custom shell script to be executed in each protein directory. [Learn about the submit_shell here.](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/submit_shell)
