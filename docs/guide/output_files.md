---
title: MCCE Output
parent: Guide
nav_order: 6
permalink: /docs/guide/output_files/
layout: default
---

As part of the standard four steps, MCCE produces a number of files associated with the initial protein output. These are best understood to be members of six categories:

**Input**: The given PDB file. Often, MCCE programs will symbolically link the name "prot.pdb" to the input PDB file.

**Output**: Results of MCCE computation, to be interpreted by the user. 

**Records**: Preserved information about the run. 

**Instructions**: Files that control how MCCE runs. run.prm can be edited to control MCCE in granual ways.

**Control**: "Midpoint" files to preserve information between steps.

**Progress**: Benchmark files to keep track of what processes are occurring, and whether the run is succeeding.

-------

Most of these files will not need to be referenced to obtain pKa information. The most important files will be bolded. These files are input for the next step, or are the target output.

## Step-by-Step Output
# Program start and Initialization

Input files:

- run.prm: mcce control file (if not specified, a default version from MCCE will be used)
- param: parameter directory (specified in run.prm)
- extra.tpl: extra parameters. Often used to correct for bias. (optional, specified in run.prm)
- new.tpl: temporary parameter file for unrecognized cofactors. (optional, created by step 1 if unknown residues are present)

# Step 1: Formatting PDB File

Input files:

- **PDB file**
- name.txt: Rules for renaming/formatting residues and atoms.

Output files:

- acc.res: solvent accessibility of residues
- acc.atm: solvent accessibility of atoms
- new.tpl: parameter file template of unrecognized cofactors (not always created)
- head1.lst: summary of rotamer making policy of residues
- **step1_out.pdb** (used by step 2)

# Step 2: Making Rotamers

Input files:

- **step1_out.pdb**: input structure of step 2 in MCCE extended PDB format
- head1.lst: rotamer making policy of residues

Output files:

- progress.log: progress report file. Dynamically updated
- rot_stat: rotamer making statistics, dynamically updated
- head2.lst: summary of rotamers made in step 2 (optionally used by step 3)
- **step2_out.pdb**: step 2 output file with multiple rotamers in extended PDB format

# Step 3: Calculate the Energy Lookup Table

Input files:

- **step2_out.pdb**: Input instructions of step 3 in MCCE extended PDB format

Output files:

- progress.log: progress report file. Dynamically updated
- progress_step3.log: logs specific conformers being processed. Used to estimate step 3 completion time.
- run.prm.record: Updates to keep track of settings- dielectric constant, solver, where MCCE is located, etc.
- **head3.lst**: rotamers in extended PDB format
- **energies** (directory): A collection of opp files, each one a pairwise interaction to a conformer.

# Step 4: Monte Carlo pKa Titration

Input files:

- **head3.lst**: self-energy of conformers, Monte Carlo sampling flags of conformers
- **energies**: energy lookup table for pairwise interaction between conformers

Output files:

- **pK.out**: pKa or Em values obtained by titration curve fitting  
- sum_crg.out: residue net charges
- fort.38: conformer occupancies- the pKa for each residue is extrapolated by where the occupancy is, or is predicted to be, .5.

# Alphabatized Reference

- acc.atm/acc.res (Control/Output) - Gives the percent surface accessibility to the solvent of the atom/residue. Used to make rotamers. 

- energies (Control) - Self and pairwise energies generated during step 3. 

- entropy.out - Table recording entropy for each conformer at different pH's.

- err.log (Progress) - Similar to progress.log, terminal output is moved here in the event of an error. Usually empty.

- fort.38 (Output) - The name is a reminder that MCCE's origins were in Fortran. 

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

- vdw0.lst - A conformer table that adds the energy from VDW interactions and the Interaction with Implicit Solvent (energy scaled by 0.06 kCal/mol/Angstrom^2), or SAS. Without the SAS term, the conformer is effectively in a vacuum.
