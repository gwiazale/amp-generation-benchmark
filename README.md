# AMP Generation Benchmark

A benchmark for machine learning methods that generate antimicrobial peptides (AMPs). Evaluate **de novo generation**, **analog improvement**, and **experimental validation** tasks.

**Maintained by [Szczurek Lab](https://www.helmholtz-munich.de/en/aih/ewa-szczurek) @ Helmholtz Munich**

## 🌐 Website

Visit the benchmark at: **https://szczurek-lab.github.io/amp-generation-benchmark**

## 📊 Tasks

### 1. De Novo Generation

Generate AMPs from scratch. No input required — methods produce novel peptide sequences. Each method contributes a FASTA file in `data/denovo/`.

### 2. Analog Generation

Improve given prototype peptides. Download the prototype sets and generate improved analogs:

| Prototype Set | Description | Download |
|---------------|-------------|----------|
| **Active** | AMPs with MIC ≤ 32 μg/mL — improve activity or broaden spectrum | [`prototype-active.fasta`](prototypes/prototype-active.fasta) |
| **Inactive** | Peptides with MIC ≥ 128 μg/mL — generate analogs that gain activity | [`prototype-inactive.fasta`](prototypes/prototype-inactive.fasta) |

### 3. Experimentally Validated

Peptides from generative methods that were tested in wet-lab MIC assays. Includes both active and inactive peptides to preserve hit-rate information. Each method contributes a CSV file in `data/validated/`.

| File | Method | Peptides | Strains | Unit | Status | DOI |
|------|--------|----------|---------|------|--------|-----|
| `hydramp.csv` | HydrAMP | 34 | 5 | µg/mL | Complete | 10.1038/s41467-023-36994-z |
| `class.csv` | CLaSS | 21 | 2 | µg/mL | Complete | 10.1038/s42256-021-00306-1 |
| `joker.csv` | Joker | 12 | 2 | µg/mL | Complete | 10.1093/bioinformatics/btac200 |
| `amp-diffusion.csv` | AMP-Diffusion | 35 | 11 | µg/mL | Partial* | 10.1126/sciadv.adp7171 |
| `deep-amp.csv` | DeepAMP | 30 | 2 | µM | Partial* | 10.1038/s41467-023-42434-9 |

\* AMP-Diffusion: sequences pending extraction from supplementary. DeepAMP: MIC values pending extraction from Supplementary Table 10.

#### Validated CSV schema

One row per peptide × strain measurement (long format).

| Column | Required | Description |
|--------|----------|-------------|
| `peptide_id` | ✓ | Unique peptide identifier within the study |
| `sequence` | ✓ | Amino acid sequence (canonical AAs only) |
| `modification` | | C/N-terminal or other modifications (e.g. `C-term amidation`) |
| `strain` | ✓ | Standardized: `Genus species STRAIN_ID` |
| `strain_type` | ✓ | `gram+` / `gram-` / `fungus` / `mycobacterium` |
| `mic` | ✓ | Numeric MIC value |
| `mic_unit` | ✓ | `ug/mL` or `uM` |
| `mic_relation` | ✓ | `=` exact, `>` right-censored, `<` left-censored |
| `medium` | | Growth medium: `MHB`, `MHB-CA`, `LB`, `TSB`, `BHI` |
| `cfu_per_ml` | | Starting inoculum |
| `ph` | | Medium pH |
| `salt` | | Salt/cation adjustment |

#### Encoding rules

- **Not tested** (peptide × strain pair): no row.
- **Tested, no activity** (e.g. MIC > 512 µg/mL): row with `mic=512, mic_relation=">"`.
- **Sequence modifications** (`-CONH2`, etc.): stripped from `sequence`, noted in `modification`.
- **Expression artifacts** (e.g. M-prefix from CFPS): stripped from `sequence`, noted in `modification`.
- **Strain names**: full genus, ATCC IDs where available, resistance markers in parentheses.

## 📁 Repository Structure

```
amp-generation-benchmark/
├── index.html                  # Website (loads dynamically from CSV)
├── references.csv              # Method metadata (single source of truth)
├── prototypes/                 # Challenge inputs for analog task
│   ├── prototype-active.fasta
│   └── prototype-inactive.fasta
├── data/
│   ├── denovo/                 # De novo generation outputs (FASTA)
│   │   ├── omegamp-u.fasta
│   │   ├── hydramp.fasta
│   │   └── ...
│   ├── analog/                 # Analog generation outputs (FASTA)
│   │   └── ...
│   └── validated/              # Experimentally validated MICs (CSV)
│       ├── hydramp.csv
│       ├── class.csv
│       ├── joker.csv
│       ├── amp-diffusion.csv
│       └── deep-amp.csv
└── README.md
```

## ➕ Contributing a Method

### For De Novo Generation
1. Add your FASTA file to `data/denovo/`
2. Add a row to `references.csv` with `task=denovo`

### For Analog Generation
1. Download prototypes from `prototypes/`
2. Generate analogs for each prototype
3. Add your FASTA file to `data/analog/`
4. Add a row to `references.csv` with `task=analog`

### For Experimentally Validated Data
1. Get sequences from the paper/repo/supplementary — strip expression tags
2. Identify modifications (amidation, acetylation, etc.)
3. Normalize strain names: expand genus abbreviations, add ATCC IDs, fix typos
4. Parse MIC values: identify censored (`>`, `<`), sentinel values, "not tested"
5. Get MIC unit and assay conditions from the methods section
6. Melt to long format: one row per peptide × strain; omit untested pairs
7. Add CSV to `data/validated/`
8. Add a row to `references.csv` with `task=validated`

### references.csv columns

```csv
method,display_name,task,sequences,description,doi,github,lab,strains,mic_unit,min_len,max_len,completeness
```

- `method`: short identifier, matches the filename (without extension)
- `display_name`: human-readable name for the website
- `task`: `denovo`, `analog`, or `validated`
- `sequences`: number of sequences (denovo/analog) or peptides (validated)
- `description`: one-line description
- `doi`: paper DOI (without `https://doi.org/` prefix)
- `github`: link to code repository
- `lab`: `szczurek` for Szczurek Lab methods, empty otherwise
- `strains`: number of strains tested (validated only)
- `mic_unit`: `ug/mL` or `uM` (validated only)
- `min_len`, `max_len`: peptide length range (optional)
- `completeness`: `complete` or `partial` (validated only)

## 🐍 Quick Start

```python
from Bio import SeqIO
import pandas as pd

# Load de novo samples
sequences = list(SeqIO.parse("data/denovo/omegamp-u.fasta", "fasta"))
print(f"Loaded {len(sequences)} sequences")

# Load prototypes for analog task
prototypes = list(SeqIO.parse("prototypes/prototype-active.fasta", "fasta"))
print(f"Loaded {len(prototypes)} prototypes")

# Load validated MIC data
df = pd.read_csv("data/validated/hydramp.csv")
active = df[df["mic_relation"] == "="]
inactive = df[df["mic_relation"] == ">"]
print(f"{len(active)} active measurements, {len(inactive)} inactive")
```

## 📖 Citation

If you use these datasets, please cite the original papers for each method.

```bibtex
@misc{amp-generation-benchmark,
  title={AMP Generation Benchmark},
  author={Paulina Szymczak},
  year={2025},
  url={https://github.com/szczurek-lab/amp-generation-benchmark}
}
```

## 🔗 Related Resources

- [Szczurek Lab Website](https://www.helmholtz-munich.de/en/aih/ewa-szczurek) — AI in Healthcare, Helmholtz Munich
- [Szczurek Lab Github](https://github.com/szczurek-lab/) — Szczurek lab methods
- [HydrAMP Web Server](https://hydramp.mimuw.edu.pl/) — Online AMP generation tool
- [OmegAMP](https://github.com/szczurek-lab/omegamp) — Targeted AMP generation framework