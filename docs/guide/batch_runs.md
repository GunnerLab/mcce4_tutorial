---
title: Multiple PDB Runs
nav_order: 7
parent: Guide
permalink: /docs/guide/batch_runs/
layout: default
---
# MCCE Multiple PDB runs

The `pro_batch` utility tool, located in `MCCE_bin`, automates `MCCE4` runs across multiple PDB files. It creates individual directories for each protein and applies identical settings across the entire batch.

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

By default, pro_batch creates a `submit_mcce4.sh` script if one is not already present in your working directory. The default configuration includes:

- Conformer-Generatiion Level: Level 1
- Dielectric Constant: 8
- PB Solver: NGPB
- Entropy Control: On

`pro_batch` can also accept custom shell scripts with the `"-custom"` flag. 
[Learn about creating and editing shell scripts here](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/submit_shell/).


# 1. Basic Usage Example
To run a batch, first organize your PDB files into a single directory. You can use `getpdb` (included in` MCCE4-Alpha/MCCE_bin`) to download files directly from RCSB.
For this example, assume a directory called `source_files` containing a couple PDB files:

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

{: .note }
>If it is your first time running `pro_batch` in a directory, `pro_batch` will pull the default script `submit_mcce4.sh` from your `MCCE4-Alpha\schedulers` folder with the message:
>`Created default submit_mcce4.sh. Edit it then re-run.`

Once the script exists, running the command again will launch the jobs:
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

# 2. Monitoring Your Runs
`MCCE4` provides two primary tools to track your progress:  `book.txt` and `mcce_stat`.

1. Using `book.txt`

Let's take a look at the `book.txt` file:
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

Refreshing `book.txt`
- The `book.txt` file acts as a master ledger. However, it does not update automatically. To refresh the status of your runs, use the `--check` flag:

```
pro_batch source_files --check
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

2. Using `mcce_stat`

For a more detailed view of which specific steps (1–4) are finished for each protein, run `mcce_stat` from your working directory:
```
mcce_stat
```

Output:
```
To see when PDB was run, reference mcce_timing.log in running directory. (pro_batch)

Completion: 7.50%
Directory step1         step2         step3     step4  Status
1ans      Exists        Exists                               
4pti      Exists
```

When all four steps are completed, the pKas of the selected protein will be available to see in "pK.out".


# 3. Advanced Configuration: --sbatch and --custom

1. Resource Management
Part of using pro_batch is memory management. When using the `--sbatch` flag, pro_batch utilizes the SLURM scheduler. You can manage memory and CPU usage by editing the header of your submit_mcce4.sh file:

```
# Parameter/Options for SLURM (Simple Linux Utility for Resource Management)
#SBATCH --job-name=submit_mcce4
#SBATCH --output=submit_mcce4.log
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=12G                 # Adjust memory if needed
#SBATCH --export=ALL
```

{: .note }
>If additional resources are available, the "--mem" line can be edited to add the amount of memory dedicated. 
>Tip: Use the lscpu command to gauge your system's capacity. For large proteins, you can add the `-p` flag to `Step 3` in your script to process conformers in parallel, significantly reducing > >run time.

2. Using Custom Scripts
If you require specific parameters (like different pH ranges or dielectric constants), use the `-custom` flag to point to your own shell script as an alternative to `submit_mcce4.sh`:

```
pro_batch source_files -custom my_analysis_settings.sh
```

We anticipate the default settings for pro_batch will meet most users' needs, but the full power of `pro_batch` is realized with the `-custom` flag, which allows a custom shell script to be executed in each protein directory. [Learn about the submit_shell here.](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/submit_shell)
