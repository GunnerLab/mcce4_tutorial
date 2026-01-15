---
title: pKa Analysis
nav_order: 2
parent: Guide
permalink: /docs/guide/pKa_analysis/
layout: default
---

# pKa Analysis

The pKa analysis program simulates tiration for the side chains of a protein. The goal of this step is to determine how the ionizations states and conformations of side chains vary with pH. 

pKa analysis produces 3 output files: 

1) **fort.38**: reports the average occupancy of each side-chain conformer over the course of the titration. From the titration curve of lysine in lysozyme, the pKₐ can be determined by identifying the midpoint of the curve, where the lysine is 50% ionized and 50% non-ionized. Projecting this midpoint onto the pH axis yields the pKₐ value.

 <img width="580" height="341" alt="Screenshot 2026-01-12 at 5 29 35 PM" src="https://github.com/user-attachments/assets/761c920c-448d-46a4-82a6-3dd34eb3c5a2" />

 2) **sum_crg.out**: Reports the net charge of each residue as a function of pH. Again, drawing a line from the midpoint of the titration curve to the pH axis, one can determine the pKa of the ionized residue.


<img width="580" height="341" alt="Screenshot 2026-01-09 at 12 53 01 PM" src="https://github.com/user-attachments/assets/2b9b538c-0231-4274-a49c-e075383ef24a" />

3) **pK.out**: Contains the calculate pKa's calies for titratabel side chains
