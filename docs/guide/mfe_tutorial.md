---
title: PSII MFE tutorial 
nav_order: 2
parent: Guide
permalink: /docs/guide/mfe_tutorial/
layout: default
---
## 0 - Goals of this tuorial 

This is a more advanced tutorial on using MFE. The goal of this tutorial is to understand if understand the ionization state of a ligated residues in Pea Photosystem II 

## 1 - Background

Photosystem II is a large protein supercomplex that initiates the photosynthetic electron transport chain in oxygenic photosynthetic organisms, including cyanobacteria and higher plants such as pea and spinach. At the core of this complex lies the oxygen-evolving complex (OEC), a metal cluster responsible for catalyzing the oxidation of water to molecular oxygen.

The OEC carries a high positive charge and therefore requires stabilization through coordination by negatively charged amino acid residues. In this tutorial, we use the MFE program to evaluate whether one such ligating residue, AspA170, energetically favors the negatively ionized state, thereby assessing its contribution to the stability of the metal center.

The purpose of the MFE calculation is to assess whether the ionized or neutral state of ASPA170 is energetically favored at a given pH. Positive MFE values indicate stabilization of the neutral form 

<figure>
  <img width="367" height="314" alt="image"
       src="https://github.com/user-attachments/assets/b6512b83-746f-4cb0-b355-104a8335c056" />
  <figcaption><b>
	  Figure 1.</b> Pea PSII and the OEC in the center.</figcaption>
</figure>
## 2 - Results 

Below is the mfe analysis for the the residue of interest and the commancd that was used:

```
mfe.py -p 7 -c 0.05 ASP-A0170_

Residue ASP-A0170_ pKa/Em=Titration of residue ASP-A0170_ out of range
=================================
Terms          pH     meV    Kcal
---------------------------------
vdw0        -0.00   -0.09   -0.00
vdw1        -0.30  -17.25   -0.41
tors        -0.25  -14.38   -0.34
ebkb         1.72   99.83    2.35
dsol        10.85  629.88   14.80
offset      -0.62  -36.17   -0.85
pH&pK0      -2.25 -130.60   -3.07
Eh&Em0       0.00    0.00    0.00
-TS          0.00    0.00    0.00
residues   -13.13 -761.88  -17.90
*********************************
TOTAL       -3.97 -230.66   -5.42  

```
In the results shown above, the van der Waals contribution slightly favors the ionized state of AspA170 in Photosystem II. More importantly, the total pairwise interaction term strongly stabilizes the ionized form. This outcome is chemically intuitive, as AspA170 must remain negatively charged to effectively coordinate the highly positively charged ions of the oxygen-evolving complex (OEC). The first line of the output show the the calculated pka value is out of range, this is not an error but that the prevoius pka analysis was ran at one pH and not across a set of values. 


We begin by examining the terms with the largest absolute values in the energy column (e.g., kcal/mol). These dominant contributions typically arise from two sources:
	1.	Residue–residue interactions
	2.	Desolvation energy (dsol)

This interplay between opposing energetic terms is the central purpose of an MFE (mean force energy) analysis. Rather than focusing on individual large energy values in isolation, MFE analysis aims to explain why a residue adopts a particular charge state or pKₐ under the conditions studied (even when the pKₐ itself is not explicitly reported). By decomposing the energetic contributions, MFE reveals how the local environment stabilizes or destabilizes specific ionization states and thereby shifts protonation equilibria.

In practice, large desolvation penalties are common for buried or functionally important residues. What allows these residues to remain charged is the presence of compensating local interactions—precisely the balance that MFE analysis is designed to quantify and interpret. From the total interaction energy, we can reasonably conclude that the ionized conformer of AspA170 is favored over the neutral form, as indicated by the negative value. This preference is beneficial for the system, since maintaining a negatively charged AspA170 helps stabilize the highly positively charged oxygen-evolving complex (OEC).

