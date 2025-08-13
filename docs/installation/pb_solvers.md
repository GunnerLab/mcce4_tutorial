---
title: PB Solvers
parent: Installation
nav_order: 2
permalink: /docs/installation/pb-solvers/
layout: default
---

## Supported PBE Solvers used in the energies calculation step (step 3) of MCCE4:
  
🚀  __NextGenPB (NGPB) from the Rocchia Lab (IIT)__ is now our default PBE solver.  
The former default PBE solver __Delphi from the Honig Lab__ and __Zap TK from OpenEye Scientific__ are also available.

---
### 🔹 NextGenPB (NGPB) - Rocchia Lab (IIT)
Developed by [Vincenzo di Florio](https://github.com/vdiflorio) at the [Rocchia Lab](https://github.com/concept-lab), IIT Genova, Italy.  

### 🔹 Delphi 
Previous standard PB solver used in MCCE4. Well-established for electrostatic calculations.  

### 🔹 Zap TK - OpenEye Scientific 
To use **Zap TK**, you must obtain an **OpenEye license**:  
🔗 [Request a License](https://www.eyesopen.com/contact)  

#### Zap Installation:
  * Follow the OpenEye Toolkit Setup Instructions: 🔗 [License Setup](https://docs.eyesopen.com/toolkits/python/quickstart-python/license.html)

  * Add the toolkit to your mca environment:
    ```
     conda activate mca
     conda install -c openeye openeye-toolkits
    ```

  * Test if your license and installation are working with:
    ```
      oecheminfo.py
    ```

  * When using Zap, we highly recommend setting the salt concentration to 0.05 for best results, i.e:
    ```
     step3.py -s zap -salt 0.05
    ```
---

