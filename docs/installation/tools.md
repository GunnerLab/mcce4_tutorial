---
title: MCCE4-Tools
parent: Installation
nav_order: 4
permalink: /docs/installation/tools/
layout: default
---


# Installation MCCE4-Tools 🔧
Our companion repository __MCCE4-Tools__ provides post-simulation scripts and their codebase to process and analyze MCCE4 outputs and thus, do not require compilation of executable files or PBE solvers but an approriate conda environment. 

[🧰 __MCCE4-Tools GitHub__](https://github.com/GunnerLab/MCCE4-Tools){: .btn .btn-blue }

## ✅ Step 1. Clone the repository to a desired place on your computer (referred to as "clone_dir"):
   * Git clone MCCE4-Tools to a desired place on your computer (copy & pasted this command and press Enter):
     ```
     git clone https://github.com/GunnerLab/MCCE4-Tools.git; cd MCCE4-Tools;
     ```

## ✅ Step 2. CHANGE 'clone_dir' to your path!
Add the clone's path to your .bashrc (.bash_profile) file, save it, then "dot" or source the file:

 ```export PATH="clone_dir/MCCE4-Tools/mcce4_tools:$PATH"```


## ✅ Step 3. Verify availability
To verify the successful installation and check the availability of the ```ms_protonation``` program, run the following commands
  
 ```
 which MCCE4-Tools
 which ms_protonation
```

# Tools Info
Many tools have a succint description in tools.info. You can list them all with:
```
 cat MCCE4-Alpha/MCCE_bin/tools.info
```

For more information about a given tool or executable python file, check the wiki, or use the "-h" flag, e.g.:
```
 step3.py -h
```

---
