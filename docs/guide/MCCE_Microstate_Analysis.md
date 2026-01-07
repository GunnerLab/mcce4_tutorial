Protonation Microstate Analysis in MCCE
Concepts, Methodology, and Practical Interpretation
 
1. What Is a Protonation Microstate?
A protonation microstate is a complete specification of the protonation states of all titratable groups (Asp, Glu, His, Lys, Arg, termini, and optionally cofactors) in a protein at a given chemical potential (pH, redox potential).
For a protein with N titratable sites, there are up to 2^Npossible protonation microstates. In practice, only a small subset of these states has significant Boltzmann probability at equilibrium.
Key distinctions:
Microstate:
One exact assignment of protonation, tautomer, and side-chain conformer states.
Protonation microstate:
A reduced representation retaining only charge states (−1, 0, +1, etc.), collapsing many conformational microstates into a single protonation pattern.
Tautomers:
Protonation microstates with the same net charge but different proton locations.
Proteins therefore exist not in a single protonation configuration, but in an ensemble of protonation microstates whose distribution depends on pH, ligands, redox state, and local electrostatics 
 
2. Why Protonation Microstates Matter
Traditional pKa calculations report average protonation probabilities. While useful, averages discard critical information:
Range of net charge states
Presence of low-probability but functionally relevant higher energy states
Coupling between protonation events at distant residues
Distinction between lowest-energy and highest-probability states
Entropic contributions arising from multiple equivalent microstates
Microstate analysis enables:
Identification of proton loading sites
Mapping of proton transfer pathways
Quantification of long-range electrostatic coupling
Generation of physically consistent protonation inputs for MD
These capabilities are particularly critical for proton pumps, and membrane proteins 
 
3. How MCCE Generates Protonation Microstates
3.1 Degrees of Freedom
In MCCE:
Protein backbone is fixed
Each titratable residue is assigned multiple conformers
Charged vs neutral
Different proton positions (e.g., His tautomers)
Optional rotamers and explicit waters
Each conformer has a precomputed energy
A microstate selects one conformer per residue.
 
3.2 MCCE Energy Model
The total energy of a microstate is a sum of:
Reference chemical potential terms
pH-dependent protonation
Redox potentials (if applicable)
Self-energies
Desolvation
Torsion
Electrostatics with backbone
Lennard-Jones interactions
Pairwise interaction energies
Electrostatics and van der Waals between residues
All energies are pre-calculated using Poisson–Boltzmann electrostatics and molecular mechanics, allowing rapid Monte Carlo sampling 
 
3.3 Monte Carlo Sampling
MCCE uses grand-canonical Monte Carlo (GCMC) sampling:
Randomly select residues and trial conformers
Accept or reject moves using the Metropolis–Hastings criterion
Allow multi-residue moves to improve sampling of strongly coupled sites
Perform annealing, equilibration, and production phases
Millions of trial microstates are explored; only accepted microstates contribute to the Boltzmann ensemble 
 
4. Storage of Microstates (ms_out Files)
Because storing every microstate explicitly is infeasible, MCCE uses a ticker-tape representation:
Initial microstate lists all conformers
Subsequent lines record only changes
Each accepted microstate stores:
Changed conformers
Total energy
Count of rejected trials before acceptance
This compact representation allows reconstruction of millions of microstates while minimizing disk usage 
 
5. Constructing Protonation Microstates
The ms_protonation tool performs the following reduction:
Fixed residues
Residues with a single conformer are assigned constant charge
Free residues
Charge states extracted from conformer identity
Charge vectors
Each microstate mapped to a vector of charges
Aggregation
Conformational microstates with identical charge vectors are grouped
Weighting
Each protonation microstate is weighted by its MC acceptance count
The result is a unique protonation microstate ensemble, each with:
Net charge
Probability
Underlying conformational degeneracy
 
6. Weighted Correlation Analysis of Protonation States
6.1 Rationale
Protonation of residues is often not independent. Electrostatic coupling means protonation at one site can stabilize or destabilize protonation elsewhere.
6.2 Weighted Pearson Correlation
For two residues p and q, the weighted correlation coefficient is:
r_pq=(∑_i▒w_i (p_i-p ˉ)(q_i-q ˉ))/√(∑_i▒w_i (p_i-p ˉ)^2 ∑_i▒w_i (q_i-q ˉ)^2 )

Where:
p_i,q_iare protonation states in microstate i
w_iis the microstate count
Only variable residues are included
Interpretation:
Positive correlation: residues protonate together
Negative correlation: protonation of one disfavors the other
Near zero: independent behavior
This analysis reveals functional proton networks and their reorganization upon ligand binding or redox change 
 
7. Thermodynamics from Microstate Ensembles
Because microstates represent a full Boltzmann ensemble, MCCE allows direct calculation of:
ΔG from relative probabilities
ΔH from average microstate energies
ΔS from ΔG=ΔH-TΔS
Microstates are grouped by reaction coordinates (e.g., protonation of specific residues), enabling quantitative thermodynamic cycles with reproducible precision—something difficult to achieve with standard MD 
 
8. Practical Outputs of ms_protonation
The ms_protonation program provides:
Enumeration of protonation microstates
Net charge distributions
Microstate probabilities
Residue-residue correlation matrices
Identification of strongly coupled clusters
Inputs for MD with physically consistent protonation states
Optional flags allow listing ionizable residues (list_head3_ionizables = true) to define correlation subsets.
 
9. Summary
MCCE protonation microstate analysis moves beyond average pKa values to provide a statistical-mechanical description of proton behavior in proteins. By explicitly enumerating and weighting protonation microstates, and by quantifying correlations between residues, the method captures:
Electrostatic coupling
Entropic contributions
Functional proton transfer networks
State-dependent reorganization of proton pathways
This framework is especially powerful for proton pumps, redox enzymes, and membrane proteins, where rare but essential protonation states govern biological function.
 
Requirements
Input Files (Required)
The following files must be present in the working directory:
Parameter file (*.crgms)
Examples provided in:
MCCE4/runprms/params.crgms
MCCE Monte Carlo output
head3.lst
Microstate output file in: “pH7.00eH0.00ms.txt”
These files are produced automatically by a completed MCCE run with Monte Carlo sampling enabled (step4.py).
 
Installation
No separate installation is required if MCCE4 is properly configured.
Ensure the MCCE tools directory is in your PATH:
export PATH=/path/to/MCCE4/MCCE_bin:$PATH
 
Usage
ms_protonation params.crgms
Help
ms_protonation -h
 
Parameter File (*.crgms) Overview
The parameter file controls which residues are analyzed, how correlations are computed, and what outputs are generated.
Key Parameters
# Required
msout_file = ms_out/pH7eH0ms.txt

# Optional: list ionizable residues and exit
list_head3_ionizables = false

# Residues included in correlation analysis
correl_resids = GLU-H0192_, GLU-H0227_, ASP-A0066_, LYS-H0026_

# Output control
write_charge_microstates = true
write_correlation_matrix = true
 
Workflow Summary
Read MCCE Monte Carlo microstate file
Reconstruct full microstate trajectory
Collapse conformational microstates into unique protonation microstates
Weight microstates by Monte Carlo acceptance count
Compute:
Charge distributions
Protonation probabilities
Residue–residue weighted correlations
 
Output Files
Typical outputs include:
Unique protonation microstate table
Charge vectors
Net charge
Probability
Charge distribution histogram
Weighted correlation matrix
Residue coupling summaries
These outputs are suitable for:
Heatmap visualization
Network analysis
MD protonation state selection
Mechanistic interpretation of proton transfer
 
General Syntax Rules
Comments start with #and are ignored unless they appear on a data line.
Lists are written on a single line using brackets:
correl_resids = [ GLUA0068_, GLUH0192_ ]
Tuples (e.g., figure sizes, bounds) use parentheses:
corr_heatmap.fig_size = (20, 8)
Plot attributes are specified using dot notation:
corr_heatmap.save_name = corr.png
Valid plot attributes include:
.save_name
.fig_size
.title
.bounds (charge histograms only)
 
Input / Output Paths
mcce_dir
Directory containing the MCCE run, including the ms_out subdirectory.
Default: current working directory.
mcce_dir = 4lzt
output_dir
Subdirectory (within mcce_dir) where all analysis outputs are written.
Default: crgms_corr.
output_dir = crgms_corr
 
Ionizable Residue Listing
list_head3_ionizables
If set to true, the program prints all ionizable residues found in head3.lst and exits.
This is useful for generating residue lists for correl_resids.
Valid values: true, false (case-insensitive).
list_head3_ionizables = False
 
Output Data Files
all_res_crg_csv
CSV file reporting charge status for all residues, including fixed and free residues.
all_res_crg_csv = all_res_crg_status.csv
res_of_interest_data_csv
CSV file containing charge statistics for residues specified in correl_resids.
res_of_interest_data_csv = crg_count_res_of_interest.csv
 
Microstate File Selection
msout_file
Name of the Monte Carlo microstate file located in mcce_dir/ms_out.
The filename typically encodes the pH and Eh values used in the MCCE run.
Examples:
pH7eH0ms.txt
pH7.00eH0.00ms.txt
pH7.50eH30.00ms.txt
msout_file = pH7.00eH0.00ms.txt
 
Microstate Filtering Parameters
min_occ
Minimum occupancy threshold for including a unique protonation microstate.
Microstates with probabilities below this value are discarded.
min_occ = 0.000000001
n_top (optional)
Limits the number of most-populated unique protonation microstates returned.
n_top = 500
 
Residue Selection
residue_kinds
Filters which residue types are included when constructing protonation microstates.
If omitted, commented out, or empty, all ionizable residues are included.
residue_kinds = [ASP, PL9, LYS, GLU, HIS, TYR, NTR, CTR]
Order and duplicates do not affect behavior.
 
correl_resids
Explicit list of residues used for correlation analysis.
Format:
<RES><CHAIN><4-digit residue number>_
Example:
correl_resids = [GLUH0024_, GLUH0227_, LYSH0026_, ASPA0066_, GLUH0192_, GLUH0143_, GLUA0068_]
Only residues listed here are included in the correlation matrix.
 
Correlation Parameters
corr_method
Correlation metric used to assess coupling between protonation states.
Valid values:
pearson (default)
spearman
corr_method = pearson
corr_cutoff
Minimum absolute correlation value required for reporting.
corr_cutoff = 0
n_clusters (optional)
Number of clusters used for hierarchical clustering of the correlation matrix.
n_clusters = 9
 
Correlation Heatmap
corr_heatmap.save_name = corr.png
corr_heatmap.fig_size  = (20, 8)
Optional title:
corr_heatmap.title = Loaded
 
Example Workflows 
Complex I / NDH-1 Protonation Microstate Analysis
Example 1: Central Cluster Analysis (Complex I)
Scientific Goal
Identify coupled protonation behavior in the central cluster (E-channel) that supports proton loading and long-range coupling.
Step 1: Run MCCE Untill step4.py
Ensure MCCE is run at the desired pH and redox state:
mcce
Confirm output:
head3.lst
ms_out/pH7eH0ms.txt
 
Step 2: Identify Ionizable Residues
list_head3_ionizables = true
ms_protonation params.crgms
This prints all titratable residues found in head3.lst.
 
Step 3: Define residues of interest
Example (Thermus thermophilus numbering):
correl_resids = [GLU-H0192_, GLU-H0227_, GLU-H0143_, ASP-A0066_, GLU-A0068_, LYS-H0026_]
 
Step 4: Run Correlation Analysis
ms_protonation params.crgms
Interpretation
Positive correlations → residues protonate together (cooperative loading)
Negative correlations → mutually exclusive protonation
Identify functional proton-loading site clusters
This analysis reproduces known central-cluster coupling observed in Complex I and NDH-1 systems 
 
Best Practices
Use multiple independent MC runs to ensure convergence
Avoid aggressive conformer pruning in highly coupled systems
Interpret correlations in structural context (e.g., PyMOL)
Use high-probability microstates for MD initialization
Investigate low-probability microstates for mechanistic insight
 

Tutorial started here


Tutorial: Protonation Microstate Analysis with ms_protonation
Example System: Hen Egg White Lysozyme (PDB ID: 4LZT)
Overview
This tutorial walks through a complete workflow for running protonation microstate analysis with weighted correlation using ms_protonation, starting from an MCCE Monte Carlo run and ending with interpretable outputs.
By the end of this tutorial, you will be able to:
Run an MCCE simulation step4 with microstate file as output  
Identify ionizable residues
Configure the params.crgms parameter file that used to run microstate analysis program
Run ms_protonation
Interpret charge microstates and residue correlations
 
Prerequisites
MCCE4 installed and compiled
mcce and ms_protonation available in your PATH
Verify availability:
which mcce
which ms_protonation
 
Step 1: Prepare the Lysozyme Structure
1.1 Obtain the PDB
Download Hen Egg White Lysozyme (4LZT) from the PDB:
wget https://files.rcsb.org/download/4LZT.pdb
Alternatively, download manually and rename:
getpdb 4LZT prot.pdb
MCCE expects the input structure to be named prot.pdb.
 
1.2 Create an MCCE Working Directory
mkdir lysozyme_mcce
cd lysozyme_mcce
cp ../prot.pdb .
 
Step 2: Run MCCE Customizing Run
2.1 Use the following customization in step4:
step4.py --xts -i 7 -n 1 –ms
--ms                  Enable microstate output; default: False.
2.2 Confirm the output files
When the run completes, confirm the following files exist:
head3.lst
ms_out/pH7.00eH0.00ms.txt
These are required inputs for ms_protonation.
 
Step 3: Create the params.crgms File
Create a file named params.crgms in the same directory.
3.1 Minimal Working Example
# Input file for protonation microstate analysis

msout_file = pH7.00eH0.00ms.txt
fig_show = False
This is sufficient to test that the program runs.
 
Step 4: List Ionizable Residues
Before defining correlations, identify ionizable residues detected by MCCE.
Edit params.crgms:
vi params.crgms
list_head3_ionizables = True
Step 4: Run the MS analysis program
ms_protonation params.crgms
The program will:
Print all ionizable residues from head3.lst
Exit immediately
Example output (truncated):
GLUA0035_
ASPA0052_
HISA0015_
LYSA0013_
...
Copy the residues of interest for the next step.
 
Step 5: Define Residues for Correlation Analysis
Lysozyme’s catalytic residues are Glu35 and Asp52, which are known to be strongly coupled.
Edit params.crgms:
list_head3_ionizables = False

correl_resids = [
  GLUA0035_,
  ASPA0052_
]

residue_kinds = [ASP, GLU, HIS, LYS, ARG, NTR, CTR]
 
Step 6: Enable Analysis Outputs and Plots
Extend params.crgms:
# Output directories
output_dir = crgms_corr

# Data outputs
all_res_crg_csv = all_res_crg_status.csv
res_of_interest_data_csv = crg_count_res_of_interest.csv

# Correlation parameters
corr_method = pearson
corr_cutoff = 0

# Figures
fig_show = False
energy_histogram.save_name = enthalpy_dist.png
energy_histogram.fig_size = (8, 8)

corr_heatmap.save_name = corr.png
corr_heatmap.fig_size = (10, 4)

# Charge microstate histograms
charge_histogram0.bounds = (None, None)
charge_histogram0.title = Protonation MS Energy
charge_histogram0.save_name = crgms_logcount_vs_E.png

charge_histogram1.bounds = (Emin, Emin + 1.36)
charge_histogram1.title = Protonation MS within 1.36 kcal/mol of Lowest
charge_histogram1.save_name = crgms_logcount_vs_lowestE.png
 
Step 7: Run ms_protonation
Execute:
ms_protonation params.crgms
Upon completion, a new directory will be created:
crgms_corr/
 
Step 8: Understand the Outputs
8.1 Charge Microstate Distributions
Files such as:
crgms_logcount_vs_E.png
crgms_logcount_vs_lowestE.png
show:
How many unique protonation microstates exist
How microstates are distributed by energy
Separation between lowest-energy and most-probable states
 
8.2 Correlation Heatmap
corr.png
This plot shows the weighted Pearson correlation between Glu35 and Asp52:
Positive correlation → protonation states rise and fall together
Negative correlation → one protonates while the other deprotonates
For lysozyme, Asp52 is typically protonated while Glu35 remains deprotonated near neutral pH, reflecting their catalytic roles.
 
8.3 CSV Data Tables
all_res_crg_status.csv
→ Protonation statistics for all residues
crg_count_res_of_interest.csv
→ Detailed microstate counts for Glu35 and Asp52
These files are ideal for:
Custom plotting
Statistical analysis
Cross-pH comparisons
 
Step 9: Interpreting Results (Lysozyme)
Key observations you should see:
Only a small number of protonation microstates have significant probability
Glu35 and Asp52 show strong coupling
Lowest-energy microstates are not always the most populated
Entropy plays a significant role in microstate selection
These features are consistent with lysozyme’s known catalytic mechanism and validate the microstate approach.
 
Common Pitfalls
❌ Running ms_protonation without head3.lst
❌ Using an incorrect msout_file name
❌ Forgetting the trailing underscore (_) in residue IDs
❌ Over-restricting residue_kinds in large systems


