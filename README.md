# Analysis of Algorithmic Variations and Enhancement of VillageNet in Unsupervised Clustering

**Author:** Arthur Santos  
**Institution:** Department of Computer Science (DCOMP) — Federal University of São João del-Rei (UFSJ)

## About the Project

This repository contains the modular implementation and experiments for research focused on investigating and structurally optimizing the **VillageNet** unsupervised clustering algorithm (Ballal et al., 2026).

The main objective is to evaluate whether modifying the algorithm's internal modules (Steps 1, 3, and 4) can improve accuracy, stability, and runtime over the standard formulation across public benchmark datasets and real clinical data of patients with Acute Myocardial Infarction (AMI).

## Investigated Algorithmic Variations

* **Step 1 (Village Creation / Voronoi Cells):**
  * `kmeans_random` (Original paper default: single random initialization)
  * `kmeans++`
  * `minibatch` (MiniBatch K-Means)
  * `kmedoids` (PAM — robustness to *outliers*)
* **Step 3 (Inter-Village Graph Edge Weighting $A_{UV}$):**
  * `discrete` (Original simple count)
  * `continuous` (Continuous Gaussian decay based on distance to boundary $\eta$)
* **Step 4 (Community Detection on the Inter-Village Network):**
  * `leiden`
  * `spectral` (Spectral Clustering with automatic $k$ estimation via *eigengap*)
  * `infomap`
  * `louvain_igraph`

## Main Structure

* `villagenet_experiments.ipynb`: Main notebook containing the `ModularVillageNet` class and benchmark experiments on public datasets (*Wine*, *Breast Cancer*, and *Pen Digits*).
* `updates.md`: Weekly log of progress, preliminary results, and next steps.
* `requirements.txt`: Project dependencies list.

## How to Run Locally

1. Create and activate a Python virtual environment:
   ```bash
   python3 -m venv venv
   source venv/bin/activate