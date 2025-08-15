---
title: Quick Start
nav_order: 4
parent: Installation
permalink: /docs/installation/quick_start/
layout: default
---
# MCCE Quick Start

Once the path to MCCE4-Alpha/bin is established, MCCE can be run. First, create a directory for the desired protein. We do not recommend mixing different MCCE runs in one directory.

```
mkdir 4LZT
cd 4LZT
```

Copy/link your chosen protein into the protein directory. If the desired protein exists on RCSB.org, you can use the tool "getpdb" to download a copy directly from there. For this example, we'll use 4LZT:

```
getpdb 4LZT
```

Now we can run MCCE. The easiest way is with "run_mcce4", which runs through the first four steps and calculates pKas for each residue of the PDB file, saving them to "pK.out" upon successful completion of the fourth step.

```
run_mcce4 4lzt.pdb
```

Other files are created by MCCE in the process of creating "pK.out". 
Learn about [these output files here.](https://gunnerlab.github.io/mcce4_tutorial/docs/mcce/mechanism) 
Learn more about [the four individual steps that comprise run_mcce4 here.](https://mccewiki.levich.net/books/mcce-tutorial-4lzt/page/calculate-pkas-of-lysozyme-mcce-steps-1-4)

[How do I customize a run?](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/submit_shell/)

[How do I run MCCE on multiple PDB files at once?](https://mccewiki.levich.net/books/p-batch-tutorial/page/how-do-i-run-multiple-proteins-at-once-p-batch-and-pro-batch)
