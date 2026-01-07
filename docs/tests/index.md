---
title: Test Cases
nav_order: 3
has_children: true
permalink: /docs/tests/
layout: default
---

<p align="center">
  <img src="{{ '/docs/images/mcce_logo1.png' | relative_url }}" alt="MCCE Logo" style="max-width: 100%; height: auto;">
</p>

# MCCE4-Alpha Test Cases

🎉 **Congratulations on successfully installing MCCE4-Alpha!**

Now that MCCE4-Alpha is up and running, we can ask what can we use it for?

- Perform **pKₐ and Em calculations**
- Calculate **protonation states via microstate sampling**
- What protonation microstates dominate under physiological conditions?


The best way to build confidence in your setup is to run a set of **well-defined test cases**. 

The tests in this section are designed to help you verify that your installation is working correctly, understand MCCE’s behavior on controlled systems, and gain intuition for interpreting its output.

Each excercise highlights a specific physical, chemical, or numerical aspect of the MCCE workflow, and can be run independently on most systems.

---

---

## Organization

Each excercise consists of a test case presented as a standalone page and includes:

- A clear **physical or biochemical motivation**
- Input structure details (PDB source, modifications, waters)
- MCCE parameter settings used
- Expected qualitative or quantitative behavior
- Notes on convergence, artifacts, or known limitations

Each test is designed to be **self-contained and reproducible**.

---

## Available Excercises

The four core excercises in this section are:

- **Excercise #1: Sanity check using `p_info`**  
  A fast verification test to confirm MCCE can parse the input structure, load topology/parameter files, and generate the expected bookkeeping outputs (e.g., residue/protein info summaries).  
  *Use this first to confirm your installation and paths are correct.*

- **Excercise #2: pKₐ example (pH titration + pKₐ fitting)**  
  A standard pH-titration workflow to compute pKₐ values for ionizable residues and validate protonation behavior across a pH range.

- **Excercise #3: Em example (redox titration over a defined Em range)**  
  A redox titration workflow to compute **Em** values for redox-active groups by scanning over an electrochemical potential window.  
  **Recommended Em scan range:** **-300 mV to +300 mV** (typical starting window; widen if transitions occur near the endpoints).

- **Excercise #4: Single-pH 4LZT microstate (MS) analysis**  
  A single-pH run on **4LZT** designed for **microstate sampling and coupling analysis** (not pKₐ fitting).  
  This is the go-to test for studying microstate populations, residue coupling, and protonation correlations at a fixed pH.

- **Excercise #5: Obtaining H-bonding pairs and H-bonding microstates data**  
  The microstate file at a given pH/Eh is processed for _H-bonding microstates_. The two main outputs are csv files that:
  - list the H-bonding microstates along with their count and occupancy (states file)
  - list the effective count and occupancy of each H-bonding pairs (pairs file)
  The pairs file can then be used for H-bond network analysis.

---

*This test suite is under active development and will expand as new physical scenarios, methodological features, and validation benchmarks are explored.*
---


*This test suite is under active development and will expand as new physical scenarios, methodological features, and validation benchmarks are explored.*
---
