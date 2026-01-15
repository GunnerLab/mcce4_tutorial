---
title: MCCE Output Files
nav_order: 9
parent: Guide
permalink: /docs/guide/output_files/
layout: default
---


# Alphabatized Reference
S1: Step1 Prepare; S2: Step2 Make conformers; S3: Step3 Calculate Energy lookup tables; S4: Step4 MC sampling

- **acc.atm/acc.res** (Output S1) - Gives the percent surface accessibility to the solvent of the atom/residue. Used to make rotamers. 
    acc.atm e.g.:ATOM    N   VAL A0002    7.494       (Val N atom of residue 2; 7.494Å^2 solvent accesible) 
    acc.res e.g.:RES   VAL A0002  104.890    0.382    (Val res 2; 104.890 Å^2 solvent accesiblity; 0.382% of fully exposed VAL)
---  
- **energies folder** (Calc S3;Input S4) - Pairwise energies generated during step 3.  
  :Each conformer makes *CONF_NAME*.opp file e.g.ASN01A0044_002.opp  
  :Conformers where no side chain atoms have a partial charge have an empty opp file   
  :The conformer indicated in the file is interacting with the 'target conf', one line for each conformer;  
  :PB: Corrected pair-wse electrostatic interaction; VdW: Pair-wise VDW; The PB and vdW energies are used in MC   sampling. Energy in Kcal/mol; Negative energies are favorable.  
  ASK JUNJUN (See Song JCC 2003); * privilaged conformer.   
  e.g. from ASP-1A0048_010.opp (10th conformer for Asp48; this is the ionized Asp)  
  Lines show interaction of this Asp conformer with two Ser 50 confermoers;
  ```
       target conf       PB      VdW    
  00105 SER01A0050_001   -4.001  -0.776  -3.785  -3.889 *  
  00106 SER01A0050_002    1.570  -0.776   1.415   1.454
  ```  
  Ser conf 001 hasproton pointing towards the Asp (with favorable interaction;  
  Ser conf 002 has the proton pointing away so the interaction is unfavorable.There is no difference in vdW interaction energy.
---

- **entropy.out** (report S4) - Table recording entropy correction for each conformer at each ti titration point.  The correction is needed because there are  more neutral than ionized conformers for most acids and bases.  This will favor the neutral form modifying the pK.  The added energy 
One line for each conformer.  The value is in Kcal/mol.  
- See Song JCC 2003 for a more complete description.  
```
ph              0.0   1.0   2.0   3.0   4.0   5.0   6.0   7.0   8.0   
ASP01A0087_001 0.440 0.438 0.447 0.449 0.402 0.170 0.356 0.000 0.000   
ASP01A0087_002 0.440 0.438 0.447 0.449 0.402 0.170 0.356 0.000 0.000   
ASP02A0087_003 0.440 0.438 0.447 0.449 0.402 0.170 0.356 0.000 0.000   
ASP02A0087_004 0.440 0.438 0.447 0.449 0.402 0.170 0.356 0.000 0.000   
ASP-1A0087_005 0.000 0.000 0.000 0.000 0.000 0.000 0.000 0.000 0.000    
```
---
- **err.log** (Progress) - Should be empty.  Will have clues if run fails.

- **fort.38** (Key Output S4) - One line per conformer. Probability (0 to 1) of microstates with this conformer.  
The file name is a reminder that MCCE's origins were in Fortran.  
Example Conformers for Asp 18 in 4LZT.  Lines truncated at pH 8 for readability.
First line is pH value.  It will be Eh in mV if it is a redox titration.
There are 4 neutral conformers (_001 to _004).  Probability of first one is <0.000.  
At low pH the Asp is in a mixture of 3 neutraol conformers.  
The ionized conformer builds up as the pH inceases.  It's 50% ionized between pH 2 and 3 (pK.out gives pK at 2.294)
```
 ph              0.0   1.0   2.0   3.0   4.0   5.0   6.0   7.0   8.0 
ASP01A0018_001 0.000 0.000 0.000 0.000 0.000 0.000 0.000 0.000 0.000 
ASP01A0018_002 0.123 0.118 0.079 0.024 0.003 0.000 0.000 0.000 0.000 
ASP02A0018_003 0.634 0.601 0.411 0.126 0.015 0.003 0.001 0.000 0.000 
ASP02A0018_004 0.238 0.223 0.151 0.045 0.005 0.001 0.000 0.000 0.000 
ASP-1A0018_005 0.005 0.059 0.358 0.805 0.978 0.996 0.999 1.000 1.000 
```
- **head1.lst** (made S1, control S2)  
```
NTR A0001_ R f 00 S f  0.0 H t 36 M 999
LYS A0001_ R f 00 S f  0.0 H t 36 M 999
```
- **head2.lst** (report S2)
---
- **head3.lst** (output S3; Key contol S4) - One line for each conformer. In summary     Controls: (1) which conformers are free in MC sampling and which have fixed occupancy. (2) inputs solution pK and Em; (3) Has conformer self-energy; (4) User added extra energy; (5) Information about how confor was made
- Any value can be modified and step4 (MC) rerun.  (Try playing and see what happens)  
DONOT REMOVE LINES.  If you want to disable a conformer make FL occ  t 0.00   
```
iConf CONFORMER     FL  occ    crg   Em0  pKa0 ne nH    vdw0    vdw1    tors    epol   dsolv   extra    history
00001 NTR01A0001_001 f 0.00  0.000     0  0.00  0  0  -0.002  -1.531   2.800   0.005  -0.000   0.000 01O000M000 t
00002 NTR+1A0001_002 f 0.00  1.000     0  8.00  0  1  -0.004  -1.596   0.000   1.039   4.786  -1.300 +1O000M000 t
00003 LYS01A0001_001 f 0.00  0.000     0  0.00  0  0  -0.578  -4.280   1.010   0.005   0.232   0.000 01O000M000 t
00004 LYS+1A0001_002 f 0.00  1.000     0 10.40  0  1  -0.624  -4.286   1.010   0.099   1.029   0.400 +1O000M000 t
```
  - iConf:conformer ID
  - CONFORMER: conformer name  
  **USE TO CONTROL ACTIVE CONFORMERS IN MC SAMPLING**  
  - FL: flag| f means the conformer is subjected to MC sampling,  
    t means the conformer is fixed at the vallue given in occ  
  - occ: occupancy; Ignored if FL is f; if FL is t the occupancy of this confer is fixed  with this value.  0 conf cannot be chosen; 1 Conf on in all states; 0.00<occ<1.00  
  e.g. Fix neutral: make ionized conformer t 0.00; keep all neutral conformers as f 0.00  
  system will MC sample amongst the neutral conformers
**REPORTING VALUES SET IN TPL FILES; Em, pK, ne, nH used to change energy with pH or Eh**  
  - crg: Check of the charge of the conformer; Should be an integer.  If this is wrong you need to fix the tpl file for the conformer type.  
  - Em0: Em in solution.  0 for the reference (neutral) redox state (or residues with no redox reactions)  
  - pKa0: pka in solution. 0 for ref (neutral protonation state)  
  - ne: # of electrons. Difference #electrons from neutral, reference state.  
  - nH: # of protons. Difference # protons from neutral, refence state.
**ENERGY TERMS CALCULATED IN STEP3; DEFINE MICORSTATE ENERGY IN MC SAMPLING.  Energy in Kcal/mol**  
  - vdw0: vdW interactions within the conformer (a side chain or ligand) + interaction with implicit solvent (See vdw0.lst)  
  - vdw1: vdw interaction of this conformer with the protein backbon  
  - tors: Torsion energy of this conformer  
  - epol: Enectrostatic interation of this conformer with protein backbone dipoles  
  - dsolv: Loss of solvation energy compared with this conformer in solution.  It should be positive as it is a loss in energy.  
  - extra: Offset added for standard amino acid pK; Free for you to modify.  
  - **ENERGY IN KCAL/MOL; NEGATIVE ENERGIES ARE FAVORABLE**  
---
- mc_out (Progress) - Provides details about the Monte Carlo process.

- name.txt (input S1) - Instructions to modify PDB input to match MCCE needs.  By default it is in the directory set with your MCCE path.  
The line with # aligns the characters in PDB file
4 character atom name; 3 character residue name; 1 chaaacter chain; 3 character residue number
first group input; changed to value on right; Lines are executed sequentially  
```
####_###_#_###  ####_###_#_###
***** ********  *****_********     e.g. if res name has 2 characters with leading space add _ for space
*****HEC******  *****HEM******        Make HEC into HEM   
*O1A*HEM******  *****PAA******        Heme propionic acids turned into PAA or PDD; This changes O1A HEM ot O1A PAA
```

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
```
VAL01A0002_001   0.429  -5.084  -4.655
```


```
|Input     | Output for next step | information |
|file.pdb        |step0.pdb       |acc.atm      |
|name.txt        |head0.lst       |acc.res      |
|param directory |




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
