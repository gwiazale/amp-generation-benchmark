# AMP Generation Benchmark

A benchmark for machine learning methods that generate antimicrobial peptides (AMPs). Evaluate both **de novo generation** and **analog improvement** tasks.

**Maintained by [Szczurek Lab](https://www.helmholtz-munich.de/en/aih/ewa-szczurek) @ Helmholtz Munich**

## 🌐 Website

Visit the benchmark at: **https://szczurek-lab.github.io/amp-generation-benchmark**

## 📊 Tasks

### 1. _De Novo_ Generation
Generate AMPs from scratch. No input required — methods produce novel peptide sequences.

### 2. Analog Generation
Improve given prototype peptides. Download the prototype sets and generate improved analogs:

| Prototype Set | Description | Download |
|---------------|-------------|----------|
| **Active** | AMPs with MIC <= 32 μg/ml — improve activity or broaden spectrum | [`prototype-active.fasta`](prototypes/prototype-active.fasta) |
| **Inactive** | Peptides with MIC >= 128 μg/ml — generate analogs that gain activity | [`prototype-inactive.fasta`](prototypes/prototype-inactive.fasta) |

## 📁 Repository Structure

```
amp-generation-benchmark/
├── index.html            # Website (loads dynamically from CSV)
├── references.csv        # Method metadata (single source of truth)
├── prototypes/           # Challenge inputs for analog task
│   ├── prototype-active.fasta
│   └── prototype-inactive.fasta
├── data/
│   ├── denovo/           # De novo generation outputs
│   │   ├── omegamp-u.fasta
│   │   ├── hydramp.fasta
│   │   └── ...
│   └── analog/           # Analog generation outputs
│       └── ...
└── README.md
```

## ➕ Contributing a Method

### For De Novo Generation:
1. Add your FASTA file to `data/denovo/`
2. Add a row to `references.csv` with `task` = `denovo`

### For Analog Generation:
1. Download prototypes from `prototypes/`
2. Generate analogs for each prototype
3. Add your FASTA file to `data/analog/`
4. Add a row to `references.csv` with `task` = `analog`

### CSV Format:
```csv
method,display_name,task,description,doi,github,sequences,length_min,length_max,lab
my-method,My-Method,denovo,Description here,10.xxxx/xxxxx,https://github.com/...,50000,5,50,
```

## 🐍 Quick Start

```python
from Bio import SeqIO

# Load de novo samples
sequences = list(SeqIO.parse("data/denovo/omegamp-u.fasta", "fasta"))
print(f"Loaded {len(sequences)} sequences")

# Load prototypes for analog task
prototypes = list(SeqIO.parse("prototypes/prototype-active.fasta", "fasta"))
print(f"Loaded {len(prototypes)} prototypes")
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
