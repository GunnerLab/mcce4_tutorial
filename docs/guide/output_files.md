---
title: MCCE Output Files
parent: Guide
nav_order: 6
permalink: /docs/guide/output_files/
layout: default
---

As part of the standard four steps, MCCE produces a number of files associated with the initial protein output. These are best understood to be members of six categories:

__Input__: The given PDB file. Often, MCCE programs will symbolically link the name "prot.pdb" to the input PDB file.

__Output__: Results of MCCE computation, to be interpreted by the user. 

__Records__: Preserved information about the run. 

__Instructions__: Files that control how MCCE runs. run.prm can be edited to control MCCE in granular ways.

__Control__: "Midpoint" files to preserve information between steps.

__Progress__: Benchmark files to keep track of what processes are occurring, and whether the run is succeeding.

-------

Most of these files will not need to be referenced to obtain pKa information. The most important files will be bolded. These files are input for the next step, or are the target output.

# Program start and Initialization

Input files and folder:
- run.prm: mcce control file (if not specified, a default version from MCCE will be used)
- param: parameter directory (specified in run.prm)
- extra.tpl: extra parameters. Often used to correct for bias. (optional, specified in run.prm)
- new.tpl: temporary parameter file for unrecognized cofactors. (optional, created by step 1 if unknown residues are present)

# Step 1: Formatting PDB File

Input files:
- __PDB file__
- name.txt: Rules for renaming/formatting residues and atoms.

Output files:
- acc.res: solvent accessibility of residues
- acc.atm: solvent accessibility of atoms
- new.tpl: parameter file template of unrecognized cofactors (not always created)
- head1.lst: summary of rotamer making policy of residues
- __step1_out.pdb__ (used by step 2)

# Step 2: Making Rotamers

Input files:
- __step1_out.pdb__: input structure of step 2 in MCCE extended PDB format
- head1.lst: rotamer making policy of residues

Output files:
- progress.log: progress report file. Dynamically updated
- rot_stat: rotamer making statistics, dynamically updated
- head2.lst: summary of rotamers made in step 2 (optionally used by step 3)
- __step2_out.pdb__: step 2 output file with multiple rotamers in extended PDB format

# Step 3: Calculate the Energy Lookup Table

Input files:
- __step2_out.pdb__: Input instructions of step 3 in MCCE extended PDB format

Output files:
- progress.log: progress report file. Dynamically updated
- progress_step3.log: logs specific conformers being processed. Used to estimate step 3 completion time.
- run.prm.record: Updates to keep track of settings- dielectric constant, solver, where MCCE is located, etc.
- __head3.lst__: rotamers in extended PDB format
- __energies__ (directory): A collection of opp files, each one a pairwise interaction to a conformer.

# Step 4: Monte Carlo pKa Titration

Input files:
- __head3.lst__: self-energy of conformers, Monte Carlo sampling flags of conformers
- __energies__: energy lookup table for pairwise interaction between conformers

Output files:
- __pK.out__: pKa or Em values obtained by titration curve fitting  
- sum_crg.out: residue net charges
- fort.38: conformer occupancies- the pKa for each residue is extrapolated by where the occupancy is, or is predicted to be, .5.

# Alphabatized Reference
S1: Step1 Prepare; S2: Step2 Make conformers; S3: Step3 Calculate Energy lookup tables; S4: Step4 MC sampling

- acc.atm/acc.res (Output S1) - Gives the percent surface accessibility to the solvent of the atom/residue. Used to make rotamers. 
    acc.atm e.g.:ATOM    N   VAL A0002    7.494       (Val N atom of residue 2; 7.494Å^2 solvent accesible) 
    acc.res e.g.:RES   VAL A0002  104.890    0.382    (Val res 2; 104.890 Å^2 solvent accesiblity; 0.382% of fully exposed VAL)
  
- energies folder (Calc S3;Input S4) - Pairwise energies generated during step 3.
  Each conformer makes --.opp file e.g.ASN01A0044_002.opp
  Conformers where no side chain atoms have a partial charge have an empty opp file
  opp file 
  Conformer the ASP is interacting with; PB: Corrected pair-wse electrostatic interaction; VdW: Pair-wise VDW; Raw energies (See JCC); * privilaged conformer. Energy in Kcal/mol; Negative energies are favorable. 
  Lines show interaction of this Asp with two Ser 50 confermoers;
  e.g. from ASP-1A0048_010.opp (10th conformer for Asp48; this is the ionized Asp)
       interact partner  PB      VdW    
  00105 SER01A0050_001   -4.001  -0.776  -3.785  -3.889 *
  00106 SER01A0050_002    1.570  -0.776   1.415   1.454
  Ser conf 001 hasproton pointing towards the Asp (with favorable interaction;
  Ser conf 002 has the proton pointing away so the interaction is unfavorable.There is no difference in vdW interaction energy.

- entropy.out (calc S4) - Table recording entropy for each conformer at different pH's.

- err.log (Progress) - Terminal output is moved here in the event of an error. Usually empty.

- fort.38 (Key Output S4) - The name is a reminder that MCCE's origins were in Fortran. 

- head1.lst/head2.lst/head3.lst (Control) - head1.lst is created by step 1, and can be modified to reduce the number of conformers made in step 2. head2.lst is a summary of rotamers made in step 2. head3.lst may be

  - iConf:conformer ID
  - CONFORMER: conformer name
  - FL: flag| f means the conformer is on, t means the conformer is off.
  - occ: occupancy
  - crg: charge
  - Em0: Em in solution
  - pKa0: pka in solution
  - ne: # of electrons
  - nH: # of protons
  - vdw0: self vdW energy + implicit vdW energy (favorable) with solvent (water)

- mc_out (Progress) - Provides details about the Monte Carlo process.

- name.txt (Instructions) - The file referenced by mcce when renaming atom names, residue names, sequence number, and chain ID. The purpose is to unify residue names to 3-char MCCE names, and break some larger cofactors into smaller ones (e.g., extracting PAA from heme groups).

- param - Contains necessary topology files, copied from the parent MCCE folder. If 00always_needed.tpl or mcce.tpl have been changed after a successful run, the param folder acts as an archive for the topology files used at runtime.

- pK.out (Output) - pKa or Em values obtained by titration curve fitting.

- prot.pdb (Input) - The name MCCE often uses in reference to the original input file.

- respair.lst - Records the pairwise energy for each pair of conformers.

- rot_stat (Progress) - Provides statistics about rotamer creation. 

- run.log (Record) - Keeps a record of terminal output from steps.

- run.prm (Instructions) - Created by step 1 if not provided- grants extra control over MCCE settings. For example, by default, the pH titration occurs along whole numbers from 0 - 14. The TITR settings can be edited to reduce the range of the pH titration, increase the points of titration, and more. 

- run.prm.record - Records the full run.prm details for each step. CAUTION: If different settings are used on different runs in the same direct, run.prm.record may not capture the changes to the settings. 

- step0_out.pdb/step1_out.pdb/step2_out.pdb - Restructured versions of the input file. step0_out deletes any header information (e.g., the headers included in RCSB downloads of PDB files). step1_out renames residues according to a file called name.txt, including the opening and concluding residues of a sequence to NTR and CTR, respectively. step2_out.pdb expands the list to include alternative conformers as well.

- sum_crg.out - Records information about the net charge of the PDB's residues at each pH titration.

- vdw0.lst (Input MC)- A conformer level; Energy from VDW interactions with Implicit Solvent (Energy = 0.06 kCal/mol/Å^2).
VAL01A0002_001   0.429  -5.084  -4.655
