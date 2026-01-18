## NPBIP: Predicting binding preferences of uncharacterized nucleic-acid-binding proteins

**NPBIP** is a computational framework designed to predict the binding affinities of uncharacterized Nucleic-acid Binding Proteins (NBPs) to any DNA or RNA sequence. 
By integrating a deep-learning model (**NucProNet**) with a similarity-based approach (**SimBind**), NPBIP offers state-of-the-art accuracy for "zero-shot" protein-binding predictions.

---

### 🚀 Quick Start & Usage
To run the full pipeline on the provided toy dataset:

**For RNA:**

```Bash
python NPBIP.py data/toy_example/RBPs.fasta data/toy_example/RNA.fasta RNA toy_run --intensities_csv data/toy_example/RNACompete_intensities.csv
```

**For DNA:**

```Bash
python NPBIP.py data/toy_example/DBPs.fasta data/toy_example/DNA.fasta DNA toy_run --intensities_csv data/toy_example/PBM_intensities.csv
```

#### 📝 Argument Descriptions:
- `protein_fasta`: Path to query protein sequences in FASTA format (e.g., `data/toy_example/RBPs.fasta`).

- `nuc_fasta`: Path to query nucleic-acid sequences (e.g., `data/toy_example/RNA.fasta`).

- `type`: The type of interaction: **RNA** or **DNA**.

- `run_id`: A unique name for your experiment (will create a folder in `output/`).

- `--intensities_csv`: (Optional) Path to ground-truth scores. If provided, the script calculates Pearson correlations.

---

### 🛠 Installation & Dependencies
**1. Python Environment**
```Bash

conda create -n npbip python=3.9
conda activate npbip
pip install -r requirements.txt
```

**2. HMMER Installation (Required for SimBind)**
NPBIP uses **HMMER** for domain identification. Ensure it is in your system's PATH.

- Linux: `sudo apt-get install hmmer`

- macOS: `brew install hmmer`

**3. External Weights**
**ESM-2** model weights (~10GB) will be downloaded automatically during the first run.

---

### 📂 Modular Script Guide (`src/`)
Every script can be executed independently for custom workflows:

#### 🧬 NucProNet Module (`src/NucProNet/`)
- `NucProNet.py`: Manages the deep-learning pipeline (embeddings + prediction).

- `create_protein_embeddings.py`: Generates ESM-2 vectors from proteins fasta files.

- `predict.py`: Performs core NucProNet inference.

- `train_NucProNet.py`: **Training Script** for retraining NucProNet on custom datasets.

- `interpretability.py`: Identifies critical binding motifs by employing the Occlusion algorithm to calculate importance scores for each nucleotide position.

#### 🔍 SimBind Module (`src/SimBind/`)
- `SimBind.py`: Manages the similarity-based pipeline.

- `protein_domain.py`: Extracts domains using HMM profiles.

- `pair_wise.py`: Computes protein-protein similarity matrices.

- `NewSeq_prediction.py`: Predicts binding to novel nucleic-acid sequences.

- `train_multi_task_newseq.py`: **Training Script** for retraining the SimBind multi-task model.

---

### 📊 Outputs & Results
Results are structured to provide both the "bottom line" and the biological reasoning behind it.

#### Primary Result
- `output/<run_id>/NPBIP_predictions.csv`: The integrated binding scores.

#### Biological Insights & Components
- `hmmscan_results_{type}.tbl`: See which functional domains were detected in your query proteins.

- `{type}_pairwise_AA_SID_results.csv`: Explore how similar your query is to the proteins in our reference database.

- `NucProNet/SimBind raw scores`: Individual scores from each component before aggregation.

- `correlation_results.csv`: Full statistical breakdown (if ground-truth was provided).
