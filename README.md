# Machine Learning–Based Cryptographic Selection System for IoT Devices  
Supplementary Data and Code Repository

This repository contains the electronic supplementary materials (ESM1–ESM4)
associated with the research project:

**“Machine Learning–Based Cryptographic Selection System for IoT Devices Based on Computational Resources.”**

The materials document the complete workflow from cryptographic scripting and
simulation to data analysis and supervised machine learning model training.

---

## Repository structure

```text
.
├─ ESM 1 - Cryptographic Scripting/
│ └─ Pseudocode, parameter configurations, and Python scripts implementing
│ symmetric and hybrid cryptographic algorithms
│
├─ ESM 2 - Data Generation/
│ └─ Scripts and pseudocode for cryptographic simulation, performance
│ measurement, IoT scaling, and raw data generation
│
├─ ESM 3 - Data Analysis/
│ └─ Scripts and pseudocode for data cleaning, weighted scoring model
│ development, and generation of summary datasets
│
├─ ESM 4 - Machine Learning Training/
│ └─ Notebooks and scripts for supervised machine learning model training,
│ optimization, and evaluation
│
└─ README.md
```
---

## Description of electronic supplementary materials

### ESM 1 – Cryptographic Scripting
Contains pseudocode and reference Python implementations for sixteen
cryptographic algorithms, including symmetric and hybrid (ECC-based) methods.
These files define system initialization, helper functions, unified encryption
and decryption interfaces, and algorithm-specific parameter settings.

### ESM 2 – Data Generation
Includes scripts used to simulate cryptographic encryption and decryption on a
host machine, record performance and resource utilization metrics, and scale the
results to represent IoT devices with varying computational capabilities. Raw
simulation outputs are generated during this stage.

### ESM 3 – Data Analysis
Contains scripts for processing raw simulation data, including decryption
verification, filtering, normalization, and weighted scoring. Standardized
datasets are produced as intermediate byproducts, followed by summary files that
identify the most suitable cryptographic configuration, or an infeasible
outcome, for each IoT device specification. Only the summary files are used as
inputs for supervised machine learning.

### ESM 4 – Machine Learning Training
Provides notebooks and scripts for preparing feature-label datasets, training
supervised machine learning models, applying optimization techniques, and
evaluating model performance.

---

## Large datasets

Due to size constraints, large datasets generated during cryptographic
simulation and data analysis (e.g., raw simulation outputs, standardized
intermediate datasets, and summary files) are **not stored directly in the
repository**.

These datasets are provided as compressed archives attached to the
corresponding **GitHub release**.

All release assets are **permanently archived and versioned via Zenodo** and are
accessible through the Zenodo DOI associated with the repository release.

---

## Data availability

The complete set of code, documentation, and large datasets described in the
associated Data Note are openly available through Zenodo under the following DOI:

> **DOI:** https://doi.org/10.5281/zenodo.17958818

The Zenodo record contains:
- A frozen snapshot of this GitHub repository
- All large dataset archives attached to the corresponding GitHub release

---

## Reproducibility notes

- Scripts are written in Python and were developed using Python 3.13.
- Required libraries and environment information are provided within each ESM
  folder.
- Users are encouraged to consult the documentation within each ESM directory
  for detailed execution instructions.

---

## License

Code is released under the MIT License.  
Data are released under the Creative Commons Attribution 4.0 International
(CC BY 4.0) License.

---

## Citation

If you use this repository or its datasets, please cite the associated research
article and the Zenodo dataset record:

Ng QM, Juremi J, Abd Rahman NA.  
Supplementary data and code for a machine learning–based cryptographic selection
system for IoT devices.  
Zenodo. DOI: _to be added_.
