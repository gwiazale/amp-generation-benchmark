# Experimentally Validated AMPs

Peptides from generative methods that were **experimentally tested** in wet-lab assays. Includes both active and inactive peptides to preserve hit-rate information.

## Format

One CSV per method in long format: **one row per peptide × strain measurement**.

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

### Encoding rules

- **Not tested** (peptide × strain pair): no row.
- **Tested, no activity** (e.g. MIC > 512 µg/mL): row with `mic=512, mic_relation=">"`.
- **Sequence modifications** (`-CONH2`, etc.): stripped from `sequence`, noted in `modification`.
- **Expression artifacts** (e.g. M- prefix from CFPS): stripped from `sequence`.
- **Strain names**: full genus, ATCC IDs where available, resistance markers in parentheses.

## Adding a new dataset

1. Get sequences from paper/repo/supplementary. Strip expression tags.
2. Identify modifications (amidation, acetylation, etc.).
3. Normalize strain names: expand abbreviations, add ATCC IDs, fix typos.
4. Parse MIC values: identify censored (`>`, `<`), sentinels, "not tested".
5. Get MIC unit and assay conditions from the methods section.
6. Melt to long format: one row per peptide × strain; omit untested pairs.
7. Add row to `references.csv` with `task=validated`.
