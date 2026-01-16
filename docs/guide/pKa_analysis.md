---
title: pKa Analysis
nav_order: 
parent: Guide
permalink: /docs/guide/pKa_analysis/
layout: default
---

# pKa Analysis

The pKa analysis program simulates tiration for the side chains of a protein. The goal of this step is to determine how the ionizations states and conformations of side chains vary with pH. 

pKa analysis produces 3 output files: 

1) **fort.38**: reports the average occupancy of each side-chain conformer over the course of the titration. From the titration curve of lysine in lysozyme, the pKₐ can be determined by identifying the midpoint of the curve, where the lysine is 50% ionized and 50% non-ionized. Projecting this midpoint onto the pH axis yields the pKₐ value.

<img width="500" height="648" alt="Screenshot 2026-01-16 at 10 56 39 AM" src="https://github.com/user-attachments/assets/6e5dd344-a54e-4855-a2b0-b5d7ff3f80cf" />


 2) **sum_crg.out**: Reports the net charge of each residue as a function of pH. Again, drawing a line from the midpoint of the titration curve to the pH axis, one can determine the pKa of the ionized residue.

<img width="500" height="576" alt="Screenshot 2026-01-16 at 10 56 05 AM" src="https://github.com/user-attachments/assets/7c7e59eb-74ee-4856-b6f1-1984fd7ab010" />



3) **pK.out**: Contains the calculated pKₐ values for titratable side chains.
