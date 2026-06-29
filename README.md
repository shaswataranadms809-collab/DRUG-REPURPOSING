# DRUG-REPURPOSING
An end-to-end bioinformatics pipeline from identifying host targets of *Haemophilus influenzae* to in silico drug repurposing.

## Background:

*Haemophilus influenzae* remains a significant human pathogen, responsible for severe respiratory tract infections, otitis media, and invasive diseases like meningitis, particularly in vulnerable pediatric populations. Traditionally, antimicrobial discovery relies heavily on targeting well-known, textbook bacterial enzymes. However, this static approach frequently overlooks the dynamic, real-time cellular battleground between the host and the pathogen during an active infection.

This project shifts the paradigm by introducing an **end-to-end translational bioinformatics pipeline**. Instead of selecting a drug target arbitrarily, this workflow captures the host-pathogen interface at peak infection hours using raw transcriptomic data. By tracking what truly matters during live cellular destruction, we isolate a critical biological target node and computationally screen an FDA-approved drug library to discover potent structural inhibitors—providing a highly accelerated framework for **drug repurposing**.

---

##  Computational Workflow

### Phase 1: Target Discovery via Host-Pathogen Transcriptomics

Rather than starting with chemical libraries, the pipeline begins with raw patient/cellular expression patterns to identify high-priority biological vulnerabilities.

* **Data Sourcing:** Retrieved raw time-course transcriptomic datasets (capturing critical intervals at 0h, 6h, 12h, and 24h post-infection) from the **NCBI Gene Expression Omnibus (GEO)** representing host tissues actively challenged by *H. influenzae*.
* **Differential Gene Expression (DGE):** Implemented an R/Bioconductor pipeline using the `DESeq2` framework to filter and isolate statistically significant genes that spike drastically during peak infection hours.
* **Pathway Enrichment Analysis:** Processed the highly upregulated gene clusters through **KEGG pathway enrichment** via the `clusterProfiler` R package. This isolated a dominant, statistically significant signaling cluster involved in the **"Cytokine-cytokine receptor interaction"** network ($p < 0.05$).

### Phase 2: Structural Modeling & Active Site Analysis

Before attempting to design a small-molecule blocker, a high-resolution 3D physical map of the chosen target protein was established.

* **Structural Retrieval:** Extracted the high-resolution X-ray crystallography structural coordinates from the **RCSB Protein Data Bank** (Target PDB Entry: **4NN5**).
* **Active Site Cavity Mapping:** Utilizing **PyMOL** for structural hygiene and **PrankWeb** (a machine-learning-driven pocket mapping algorithm), the protein topology was scanned to locate the deepest, geometrically and chemically favorable hydrophobic pockets.
* **Grid Box Parameterization:** Isolated the precise catalytic amino acid residues lining this binding cleft to calculate the exact spatial coordinates ($X, Y, Z$ dimensions) for the downstream docking grid box.

### Phase 3: In Silico High-Throughput Virtual Screening

Acting as a computational chemist, a massive library of compounds was thrown at the 3D target structure to find the tightest physical fit.

* **Compound Library Sourcing:** Downloaded a curated, pre-filtered library of **FDA-Approved Drugs** in structural `.sdf` format from the **ZINC20** and **DrugBank** databases.
* **Ligand Preparation & Energy Minimization:** Addressed structural constraints using **OpenBabel**. Flat 2D chemical drawings were converted into optimized 3D formats using **MMFF94/UFF forcefields**, adding explicit polar hydrogens and calculating appropriate electrical partial charges.
* **High-Throughput Molecular Docking:** Executed parallel screening in **PyRx (AutoDock Vina)**. The system systematically evaluated **2,934 distinct structural conformations and poses across 211 unique small-molecule drugs**.

---

##  Key Results & Insights

The screening pipeline successfully characterized multiple high-affinity ligands capable of forming thermodynamically stable configurations within the 4NN5 binding cleft.

### Top Lead Candidates by Thermodynamic Binding Energy

The raw docking profiles (`Drug_Energies.csv`) were evaluated by sorting the minimum binding affinities (where **more negative values indicate stronger, more favorable binding bonds**).

### Highlighted Findings

High-Throughput Virtual Screening sorted 211 unique FDA compounds by thermodynamic stability. While complex molecules like **Metocurine (-9.6 kcal/mol)** and **Fludarabine (-8.9 kcal/mol)** showed high binding scores, physiological profiling eliminated them due to extreme neuromuscular toxicity and systemic cytotoxicity, respectively. **Temafloxacin (DB01405)** at (-9.0 kcal/mol) was selected as the primary therapeutic lead, providing computational validation that a fluoroquinolone core fits perfectly inside the pocket.

---

## Future Directions

While this computational framework provides strong, data-backed candidate molecules, the next phases of this study look toward translational validation:

1. **2D/3D Interaction Profiling:** Mapping the precise atomic contacts (Hydrogen bonding, Pi-Pi stacking, and hydrophobic interactions) between the top drug hits and the specific amino acids lining the 4NN5 pocket using **BIOVIA Discovery Studio Visualizer**.
2. **Molecular Dynamics (MD) Simulations:** Running 100-nanosecond MD simulations (using GROMACS or AMBER) to test the stability of the drug-protein complex over time under realistic physiological conditions (simulating temperature, water box environment, and pressure).
3. **In Vitro Validation:** Transitioning the top-scoring repurposing candidates into wet-lab validation, evaluating minimal inhibitory concentrations (MIC) against live *Haemophilus influenzae* cultures in vitro.

## 🛠️ Computational Toolstack

* **Data Analysis & Scripting:** R (Bioconductor), Python (Matplotlib, Seaborn, Pandas)
* **Cheminformatics & Docking:** PyRx, AutoDock Vina, OpenBabel Suite
* **Structural Visualization:** PyMOL, BIOVIA Discovery Studio Visualizer
* **Biological Databases:** NCBI GEO, RCSB Protein Data Bank, DrugBank, ZINC20
