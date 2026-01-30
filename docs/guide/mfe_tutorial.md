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

## 2 - Analysis

In the AspA170 results shown above, the total MFE energy term's (__TOTAL__) largest contributor is the total MFE pairwise residue-residue interaction term (__residues__) which is strongly stabilized in the ionized form in Photosystem II. This outcome is chemically intuitive, as AspA170 must remain negatively charged to effectively coordinate the highly positively charged ions of the oxygen-evolving complex (OEC).

{: .note }
> The first line of the output states __"the calculated pKa/Em=Titration value is out of range"__. This is not an error but simply stating the MFE pKa > analysis was ran at one pH and not across a set of titration point values.
> The MFE analysis aims to explain why a residue adopts a particular charge state or pKₐ under specific conditions (even when the pKₐ itself is not explicitly reported/calculated).

Let's examine the terms with the largest contributions to the ionization state of AspA170. These dominant contributions arise from two sources:
	1.	Residue–residue interactions (residues)
	2.	Desolvation energy (dsol)

The residues term are comprised of all pairwise residue-residues interactions. MFE decomposes how the local protein environment stabilizes or destabilizes specific residue ionization states and thereby shifts protonation equilibria (Jose: show which residues are important).

In practice, large desolvation penalties are common for buried or functionally important residues. Residues can remain charged when the presence of compensating local interactions—precisely are balanced whether in a highly dielectric solvent and deeply buried delectric medium (protein).

The results here align chemically to show how the negatively charged AspA170 helps stabilize the highly positively charged oxygen-evolving complex (OEC).

