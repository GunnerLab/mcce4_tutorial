---
title: Quick Start
nav_order: 1
parent: Guide
permalink: /docs/guide/quick_start/
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

If MCCE is working properly, your pK.out file should look close to the following text file:

```
  pH             pKa/Em  n(slope) 1000*chi2       vdw0      vdw1      tors      ebkb      dsol    offset     pHpK0     EhEm0       -TS  residues      total
NTR+A0001_        6.682     0.973     0.029      -0.00     -0.05     -0.26      0.64      4.31     -0.95     -1.32      0.00      0.00     -1.30       1.08
LYS+A0001_        9.503     1.006     0.030      -0.03     -0.00      0.00      0.07      0.59      0.29     -0.90      0.00      0.00     -0.02      -0.00
ARG+A0005_       12.841     0.858     0.061      -0.05     -0.01      0.00     -0.74      0.79      0.00      0.34      0.00      0.23     -0.34       0.23
GLU-A0007_        3.320     0.965     0.063      -0.00      0.03     -0.20     -1.33      1.26     -0.22      1.43      0.00      0.28     -0.98       0.29
LYS+A0013_       11.207     0.952     0.042      -0.03     -0.01      0.00      0.20      0.81      0.29      0.81      0.00      0.00     -2.08       0.01
ARG+A0014_       13.576     0.937     0.038      -0.05     -0.02      0.00      0.05     -0.36      0.00      1.08      0.00      0.36     -0.82       0.23
HIS+A0015_        6.408     0.986     0.041      -0.03     -0.17      0.00      1.53      1.42     -0.73     -0.57      0.00      0.35     -0.20       1.58
ASP-A0018_        2.281     0.937     0.114      -0.01      0.04     -0.16     -2.08      1.18     -0.62      2.47      0.00      0.38     -0.81       0.39
TYR-A0020_       13.497     0.675     0.807       0.00      0.00      0.00      0.46      3.28     -0.37     -3.30      0.00      0.25     -0.21       0.13
ARG+A0021_       13.259     0.779     0.571      -0.05     -0.02     -0.00     -0.08      0.09      0.00      0.76      0.00      0.24     -0.70       0.24
TYR-A0023_       10.680     0.864     0.029       0.00      0.00      0.00     -1.22      2.22     -0.37     -0.48      0.00      0.00     -0.15       0.01
LYS+A0033_       10.700     0.970     0.073      -0.03     -0.01      0.00      0.07      0.70      0.29      0.30      0.00      0.00     -1.26       0.06
GLU-A0035_        5.586     0.970     0.054      -0.02      0.07     -0.02     -1.30      2.61     -0.22     -0.84      0.00      0.20     -0.28       0.19
ARG+A0045_       13.282     0.679     2.103      -0.05     -0.00      0.00      0.18     -0.25      0.00      0.78      0.00      0.44     -0.67       0.41
ASP-A0048_        1.567     0.966     0.024      -0.01      0.03     -0.25     -2.58      3.38     -0.62      3.18      0.00      0.12     -3.14       0.12
ASP-A0052_        2.448     0.958     0.034      -0.01      0.07     -0.09     -1.23      2.32     -0.62      2.30      0.00      0.52     -2.29       0.97
TYR-A0053_        >14.0                           0.00      0.00      0.00     -1.69      4.65     -0.37     -3.80      0.00      0.00      7.32       6.12
ARG+A0061_       13.749     0.803     0.003      -0.07     -0.02     -0.00      0.84      0.28      0.00      1.25      0.00      0.39     -2.26       0.40
ASP-A0066_        0.019     0.994     0.147      -0.01      0.08     -0.16     -5.86      7.51     -0.62      4.73      0.00      0.13     -7.46      -1.66
ARG+A0068_       13.767     0.585     1.272      -0.03     -0.01      0.00      0.34     -0.38      0.00      1.27      0.00      0.00    156.81     158.00
ARG+A0073_       13.069     0.876     0.424      -0.05     -0.01      0.00     -0.15     -0.06      0.00      0.57      0.00      0.40     -0.31       0.38
ASP-A0087_        2.120     0.971     0.100      -0.01      0.05     -0.21     -2.02      1.34     -0.62      2.63      0.00      0.32     -1.16       0.32
LYS+A0096_       10.696     0.782     0.462      -0.04     -0.03      0.00     -1.65      1.42      0.29      0.30      0.00      0.00     -0.27       0.02
LYS+A0097_       10.881     0.880     0.061      -0.03     -0.01      0.00     -0.28      0.16      0.29      0.48      0.00      0.00     -0.62      -0.01
ASP-A0101_        4.249     1.018     0.013      -0.00      0.03     -0.11      0.08      1.32     -0.62      0.50      0.00      0.57     -1.18       0.57
ARG+A0112_       12.855     0.897     0.004      -0.06     -0.02      0.00     -0.23      0.77      0.00      0.35      0.00      0.47     -0.74       0.54
ARG+A0114_       13.286     0.945     0.004      -0.07     -0.01      0.00     -0.07     -0.25      0.00      0.79      0.00      0.35     -0.39       0.36
LYS+A0116_        9.559     0.952     0.068      -0.04     -0.00      0.00      0.11      0.48      0.29     -0.84      0.00      0.00      0.01       0.02
ASP-A0119_        3.692     0.976     0.020      -0.01      0.05     -0.25     -1.15      2.15     -0.62      1.06      0.00      0.07     -1.23       0.07
ARG+A0125_       13.176     0.906     0.020      -0.06     -0.01      0.00      0.40     -0.12      0.00      0.68      0.00      0.38     -0.88       0.38
ARG+A0128_       13.608     0.929     0.053      -0.04     -0.00      0.00      0.07     -0.80      0.00      1.11      0.00      0.42     -0.35       0.42
CTR-A0129_        2.570     0.899     0.077       0.01      0.06     -0.11     -0.04      1.67      0.00      1.18      0.00      0.52     -2.77       0.54
```

Other files are created by MCCE in the process of creating "pK.out". 
Learn about [these output files here.](https://gunnerlab.github.io/mcce4_tutorial/docs/mcce/mechanism) 
Learn more about [the four individual steps that comprise run_mcce4 here.](https://mccewiki.levich.net/books/mcce-tutorial-4lzt/page/calculate-pkas-of-lysozyme-mcce-steps-1-4)

[How do I customize a run?](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/submit_shell)

[How do I run MCCE on multiple PDB files at once?](https://gunnerlab.github.io/mcce4_tutorial/docs/guide/p_batch)

How do I know if the run processed correctly?

How do I interpret MCCE's Output Files?
