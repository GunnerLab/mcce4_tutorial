---
title: Installation
nav_order: 2
has_children: true
permalink: /docs/installation/
layout: default
---

<p align="center">
  <img src="{{ '/docs/images/mcce_logo1.png' | relative_url }}" alt="MCCE Logo" style="max-width: 100%; height: auto;">
</p>

# Installation
Welcome to the installation guide for MCCE4-Alpha, a simulation tool for electrostatic simulations of biomolecular structures, along with its partner post-simulation tools repository MCCE4-Tools.  
This guide will walk you through the entire setup for Linux OS.

## MCCE4-Alpha Installation
There are two installation options for MCCE4-Alpha, an automated install and a compiled install.

[__Option A: Automated Installation with Script__](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/MCCE4_Alpha_auto)
   - Keep the provided `mcce` and `delphi` (alternate PBE solver) executables compiled for Linux OS;
   - Use the semi-automated setup using provided script that download a generic NGPB image and setup your environment.
__Note:__ 'Option A' should be tried first.  

[__Option B: Manual Installation with Executable Compilation and Creation of a NGPB Image Optimized for your System__](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/MCCE4_Alpha_compiled)
   - Compile `mcce` with or without that of `delphi`, and the creation of a NGPB image optimized for your platform.

{: .important }
> This option is necessary if you cannot run a simulation with an installation made with __Option A__.

## MCCE4-Tools Installation:
[Instructions page](https://gunnerlab.github.io/mcce4_tutorial/docs/installation/MCCE4_Tools)


---
## Help us improve our MCCE4 Installation Guide
Please, report any issues you encounter, ask questions or request a feature [here](https://github.com/GunnerLab/MCCE4-Alpha/issues). Thank you.
