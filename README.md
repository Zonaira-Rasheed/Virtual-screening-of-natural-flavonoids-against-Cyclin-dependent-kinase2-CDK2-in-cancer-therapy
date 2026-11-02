# Virtual Screening of Natural Flavonoids Against CDK2 in Cancer Therapy

An in silico study that screens a library of natural flavonoids against **Cyclin-Dependent Kinase 2 (CDK2)**, a cell-cycle kinase and an established target in cancer research. The workflow combines molecular docking, interaction profiling, ADMET prediction, molecular dynamics (MD) simulation and MM/GBSA binding-energy estimation.

> **Project context:** Summer School in Bioinformatics and Computational Chemistry (2nd Internship Training Program 2026), Institute of Chemistry, University of Sargodha, 1 August – 15 September 2026.

> **Important:** All results here are computational predictions. Docking scores, predicted ADMET properties and MM/GBSA estimates are **not** experimental evidence of efficacy or safety. They only suggest candidates for experimental validation.

---

## Workflow

```
Flavonoid library (100 compounds, PubChem)
        │  RDKit optimisation, Lipinski filtering, Open Babel (PDBQT)
        ▼
Molecular docking vs CDK2 (PDB 4KD1) — AutoDock Vina
        │  top 10 ranked ligands
        ▼
Interaction profiling (PLIP, BIOVIA Discovery Studio)
        ▼
ADMET prediction (ADMETlab 3.0, ProTox 3.0)
        ▼
MD simulation (GROMACS) — Hesperidin (50 ns), Corylifol A (20 ns)
        ▼
MM/GBSA binding free energy (gmx_MMPBSA)
```

## Target protein

| Item | Detail |
|---|---|
| Protein | Human Cyclin-Dependent Kinase 2 (CDK2) |
| PDB ID | 4KD1 (X-ray, 1.70 Å) |
| Chain used | A (298 residues) |
| Docking box centre | x = 55.584, y = 79.685, z = 28.275 Å |
| Docking box size | 18 × 18 × 18 Å |

The docking site was defined from the co-crystallised ligand in 4KD1.

## Key results

### Top 10 docking results (AutoDock Vina)

| Rank | Compound | PubChem CID | Binding energy (kcal/mol) |
|---|---|---|---|
| 1 | Hesperidin | 10621 | −10.078 |
| 2 | Corylifol A | 25056407 | −9.569 |
| 3 | Catechin gallate | 5276454 | −9.516 |
| 4 | Baicalin | 64982 | −9.400 |
| 5 | Corylin | 5316097 | −9.330 |
| 6 | Eriocitrin | 83489 | −9.325 |
| 7 | Wighteone | 5281814 | −9.240 |
| 8 | Lupiwighteone | 5317480 | −9.112 |
| 9 | Gallocatechin gallate | 5276890 | −9.047 |
| 10 | Puerarin | 5281807 | −9.006 |

Residues that recur in the interaction analysis include **Leu83, Lys33, Asp86, Gln131, Ile10, Thr14 and Leu134**.

### ADMET (predicted)

- **Acute oral toxicity (ProTox 3.0):** predicted LD50 from 832 to 12,000 mg/kg (toxicity classes 4–6). Hesperidin and Eriocitrin have the highest predicted LD50 (12,000 mg/kg).
- **ADMETlab 3.0:** GI absorption, BBB permeability, CYP inhibition (CYP1A2, 2C19, 2C9, 2D6, 3A4), hepatotoxicity, Ames mutagenicity, skin sensitisation and drug-likeness (QED) were predicted for all ten compounds. See `4_ADMET_Analysis/` for the full tables.
- Several top compounds show poor predicted absorption, which is a known limitation of flavonoids (low bioavailability).

### Molecular dynamics and MM/GBSA

| System | Length | Observations |
|---|---|---|
| CDK2–Hesperidin | 50 ns | Protein backbone RMSD stable at about 0.25–0.30 nm; Rg 1.99–2.08 nm; SASA 145–168 nm². The ligand RMSD rises to about 0.4–0.6 nm after ~20 ns, indicating greater ligand mobility. |
| CDK2–Corylifol A | 20 ns | Protein backbone RMSD about 1.5–2.3 Å; ligand RMSD about 2.5–3.1 Å; mean 1.5 H-bonds (max 4); Leu83 and Lys33 repeatedly involved. |

**MM/GBSA (CDK2–Corylifol A, 101 snapshots from 15–20 ns):** ΔG<sub>bind</sub> = −43.63 ± 3.99 kcal/mol (van der Waals −49.48, electrostatic −58.52, polar solvation +70.21, non-polar solvation −5.84 kcal/mol).
For CDK2–Hesperidin, the report states an MM/GBSA estimate of −40.89 kcal/mol.

## Repository structure

```
CDK2-Flavonoid-Virtual-Screening/
├── 1_Project_report/              Full project report
├── 2_Ligand_Data/                 Flavonoid library and prepared ligand files
├── 3_Docking_Results/             Docking scores and protein–ligand interaction analysis
├── 4_ADMET_Analysis/              ADMETlab 3.0 and ProTox 3.0 outputs
├── 5_Molecular_Dynamics_Simulation/   MD analysis (RMSD, RMSF, Rg, SASA, H-bonds) and MM/GBSA
├── 6_Figures/                     Figures and visualisations
└── README.md
```

## Software and tools

| Step | Tool |
|---|---|
| Structure retrieval | RCSB Protein Data Bank |
| Ligand retrieval | PubChem (PUG REST) |
| Ligand preparation | RDKit, Open Babel 3.1.1 |
| Docking | AutoDock Vina |
| Interaction analysis | PLIP, BIOVIA Discovery Studio |
| ADMET | ADMETlab 3.0, ProTox 3.0 |
| MD simulation | GROMACS, AMBER99SB-ILDN, TIP3P, ACPYPE |
| Binding free energy | gmx_MMPBSA v1.7.0 (Generalized Born, igb = 2) |

Protein and ligand preparation followed the workflows in
[biochemi/pdb_extract](https://github.com/biochemi/pdb_extract) and
[biochemi/pubchem_extract_optimize](https://github.com/biochemi/pubchem_extract_optimize).

## Reproducibility notes

- Ligands were filtered with Lipinski's rule of five (MW ≤ 500 Da, logP ≤ 5, HBD ≤ 5, HBA ≤ 10); of 100 compounds, 97 could be optimised and 95 remained after cleaning.
- Inputs, docking parameters and MD settings are provided in the folders above. Large trajectory files (`.xtc`, `.trr`, `.edr`, `.cpt`) are not included because of size.

## Limitations

- Docking scores are approximate and depend on the scoring function; no experimental validation was performed.
- MD runs are short (20–50 ns) and were run once per system.
- ADMET values come from web-based predictive models.

## Citation

If you use this work, please cite the project report in `1_Project_report/`.

## Authors

This is a **group project**, carried out by:

| Author | Affiliation |
|---|---|
| Ahmad Jamilu Rilwan | Department of Life Science, Mewar University, Rajasthan, India |
| Zonaira Rasheed | Department of Biotechnology, Institute of Life Sciences, Gulab Devi Educational Complex, Lahore, Pakistan |
| Nadar Esther Bhaskar | Department of Biochemistry, Biotechnology and Bioinformatics, Avinashilingam Institute for Home Science and Higher Education for Women, Coimbatore, India |
| Sandip Biswas | Department of Zoology, Krishnagar Government College, West Bengal, India |
| Atka Shahid | Department of Chemistry, University of Education Lahore, Punjab, Pakistan |

**Supervisor:** Dr. M. Fayyaz ur Rehman, Summer School Coordinator, Institute of Chemistry, University of Sargodha

Repository maintained by Zonaira Rasheed.

## Acknowledgment

The authors thank Dr. M. Fayyaz ur Rehman for guidance and for teaching bioinformatics during the Summer School.

## License

This project is released under the MIT License. See the [LICENSE](LICENSE) file for details.
