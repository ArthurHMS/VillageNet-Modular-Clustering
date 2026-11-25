**Researcher:** Arthur Santos (DCOMP - UFSJ)  
**Repository:** [Modular Evaluation and Enhancement of VillageNet for Unsupervised Clustering](https://github.com/ArthurHMS/VillageNet-Modular-Clustering)

---

## Week 1 — November 7, 2026

### 1. Progress Summary
* **Scope Definition and Initial Documentation:** Prepared the 5-minute presentation slides and formal LaTeX research proposal, defining the bottlenecks in Steps 1, 3, and 4 of the original VillageNet algorithm (Ballal et al., 2026).
* **Environment and Repository Setup:** Created the GitHub repository and configured the local Python (`venv`) environment integrated with VS Code (`villagenet_experiments.ipynb`).
* **`ModularVillageNet` Class Implementation:** Implemented all 5 steps of VillageNet in a modular, decoupled design:
  * **Step 1 (Village Creation / Voronoi):** Implemented `kmeans_random` (paper default with 1 random initialization), `kmeans++`, `minibatch`, and `kmedoids` (custom PAM implementation for outlier robustness).
  * **Steps 2 and 3 (Boundary $\eta$ and Inter-Village Graph):** Vectorized geometric projection onto the line connecting centroids, selection of the $\eta = 30$ nearest external points via `np.argpartition`, and two weighting options for the adjacency matrix $A_{UV}$: `discrete` (original simple count) and `continuous` (Gaussian decay by distance to the boundary).
  * **Step 4 (Community Detection):** Integrated `leiden`, `louvain_igraph`, `infomap`, and `spectral` (with automatic cluster count $k$ estimation via the Laplacian *eigengap* heuristic).
* **First Benchmark Battery (Public Datasets):** Ran a comparative evaluation of the 8 initial configurations (Experiments 1 and 2) on *Wine* ($m^*=3$), *Breast Cancer* ($m^*=2$), and *Pen Digits* ($m^*=10$), recording NMI, ARI, Silhouette, cluster count error $|m - m^*|$, and runtime.

---

### 2. Preliminary Results and Key Findings

| Dataset | Best Tested Configuration | NMI (Original Paper) | NMI (Our Test) | ARI (Original Paper) | ARI (Our Test) | Inferred $m$ (True $m^*$) | Time (s) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Wine** ($m^*=3$) | `KMeans-Random + Discrete + Leiden/Infomap` | $0.8400$ | **$0.8920$** | $0.8600$ | **$0.9134$** | $3$ ($3$) | $0.0023\text{s}$ |
| **Breast Cancer** ($m^*=2$) | `KMeans-Random + Discrete + Infomap` | $0.5300$ | **$0.6225$** | $0.5400$ | **$0.6971$** | $3$ ($2$) | $0.0461\text{s}$ |
| **Breast Cancer** ($m^*=2$) | `KMeans-Random + Discrete + Spectral` | $0.5300$ | $0.5662$ | $0.5400$ | $0.6092$ | **$2$ ($2$)** | $0.0631\text{s}$ |
| **Breast Cancer** ($m^*=2$) | `KMeans++ + Continuous + Leiden` | $0.5300$ | $0.5313$ | $0.5400$ | **$0.6447$** | $3$ ($2$) | $0.0954\text{s}$ |
| **Pen Digits** ($m^*=10$) | `KMeans-Random + Discrete + Infomap` | $0.8300$ | **$0.8450$** | $0.7300$ | **$0.8099$** | $12$ ($10$) | $0.1248\text{s}$ |

1. **Infomap Superiority in Step 4 (Experiment 1):** *Infomap* (which, like WLCF, relies on *Random Walks*, but optimizes information flow compression) outperformed the metrics reported in the original paper across all three datasets, notably on the medical dataset *Breast Cancer* (ARI jump from $0.54$ to $0.6971$).
2. **Impact of Continuous Weighting and K-Means++ (Experiment 2):** On *Breast Cancer*, replacing single random initialization with `K-Means++` and applying continuous boundary weighting (`continuous`) raised *Leiden*'s ARI from $0.3296$ to $0.6447$, confirming the hypothesis that discrete boundary counting is sensitive to noise.
3. **Exact $k$ Recovery by Spectral Clustering:** *Spectral Clustering* paired with the *eigengap* heuristic was the only method to recover the exact number of true classes ($m=2$, zero error) on *Breast Cancer*.
