# tximportData

Bioconductor ExperimentData package holding example quantifier output for
tximport / tximeta / fishpond vignettes and tests. All data lives in
`inst/extdata/`; there is no R code. Details of software versions and calls
are in `vignettes/tximportData.Rmd`.

## Goal: cut package to ~100 MB

Current state (survey on 2026-09-26, branch `devel`, v1.41.0):

- `inst/extdata` on disk: **~593 MB**
- `inst/extdata` gzipped tar (approximates the built tarball): **~388 MB**
- `.git` pack: ~633 MB (history is not part of the tarball)

Plan: drop inferential replicate data for Salmon and kallisto from this
package. The full pre-reduction data (commit `02887f7`) is on the
`with-inf-reps` branch at https://github.com/mikelove/tximportData and has
been released on Zenodo. All reduction work happens on `devel` only; do not
modify `with-inf-reps`.

## Datasets

Sizes: "disk" = `du -sh`, "gz" = size of `tar czf` of that entry.

| Entry | Disk | gz | Contents | Inf. reps? |
|---|---|---|---|---|
| `alevin/` | 178 MB | 58.6 MB | alevin 1.6.0, 50 mouse cells x3 (Hagai 2018): `mouse1_unst_50`, `mouse1_LPS2_50`, `mouse1_unst_50_boot` | `mouse1_unst_50_boot` (20 cell bootstraps; mean/var mats only) |
| `kallisto/` | 75 MB | 74.4 MB | kallisto 0.43.1, GEUVADIS x6, Gencode v27; `abundance.h5` + `abundance.tsv.gz` | no (`n_bootstraps: 0`) |
| `salmon_ec/` | 59 MB | 16.5 MB | Salmon 1.1.0 `--dumpEq`, 2 Tasic 2018 samples, plus `tx2gene_tasic.csv` | no (equivalence classes) |
| `gencode/` | 56 MB | 55.3 MB | `gencode.v48.annotation.gtf.gz` (for oarfish) | no |
| `kallisto_boot/` | 47 MB | 46.3 MB | kallisto 0.43.0, GEUVADIS x6, Ensembl v87, `-b 5` | **yes** |
| `salmon_gibbs/` | 43 MB | 39.7 MB | Salmon 0.8.1, GEUVADIS x6, Ensembl v87, `--numGibbsSamples 5` | **yes** |
| `oarfish/` | 35 MB | 28.6 MB | oarfish 0.9.0, SG-NEx H9 cDNA reps 2-4 (Gencode v48 + novel) | no |
| `rsem/` | 27 MB | 26.6 MB | RSEM 1.2.31, GEUVADIS x6, gene + isoform results | no |
| `salmon/` | 18 MB | 18.0 MB | Salmon 0.8.2, GEUVADIS x6, Gencode v27 | no |
| `salmon_dm/` | 14 MB | 11.7 MB | Salmon 0.14.1, Drosophila SRR1197474 (+ `.plus` with artificial "Newgene"), GTFs, linkedTxome headers | no |
| `sailfish/` | 9.8 MB | 4.1 MB | Sailfish 0.9.0, GEUVADIS x6 | no |
| `cufflinks/` | 7.9 MB | 3.0 MB | Cufflinks 2.2.1 cuffnorm isoform tables | no |
| `tx2gene.gencode.v27.csv` | 7.0 MB | 1.2 MB | tx2gene for Gencode v27 | - |
| `tx2gene.ensembl.v87.csv` | 6.0 MB | 1.1 MB | tx2gene for Ensembl v87 (used with `salmon_gibbs`/`kallisto_boot`) | - |
| `tx2gene_alevin.tsv` | 5.8 MB | 0.9 MB | tx2gene for alevin | - |
| `refseq/` | 2.9 MB | 2.3 MB | Salmon 0.99.0, ERR188021 only, RefSeq | no |
| `tx2gene.csv` | 852 KB | 0.2 MB | tx2gene (RefSeq/UCSC) | - |
| `samples.txt`, `samples_extended.txt` | 8 KB | ~0 | GEUVADIS sample tables | - |

## Inferential replicate data (to move to `with-inf-reps`)

- `salmon_gibbs/` (all 6 samples): 43 MB disk / 39.7 MB gz
  - per sample: `aux_info/bootstrap/bootstraps.gz` (~3.5 MB) + `names.tsv.gz`,
    and `quant.sf.gz` (~2.5 MB)
- `kallisto_boot/` (all 6 samples): 47 MB disk / 46.3 MB gz
  - per sample: `abundance.h5` (~5.8 MB, holds bootstraps) + `abundance.tsv.gz`
- `alevin/mouse1_unst_50_boot/`: 3.7 MB disk / 2.2 MB gz
  (not Salmon/kallisto bulk; decide separately)
- `tx2gene.ensembl.v87.csv` (1.1 MB gz) is only needed for the two Ensembl v87
  inf-rep directories.

Dropping `salmon_gibbs` + `kallisto_boot` (+ ensembl v87 tx2gene) saves
~96 MB disk / ~87 MB gz, leaving ~497 MB disk / ~300 MB gz. That alone does
not reach 100 MB.

## Other large contributors (not inf reps)

- `alevin/*/alevin/bfh.txt` + `raw_cb_frequency.txt` (two non-boot samples):
  170 MB disk / 54.7 MB gz. Likely not needed for `tximport(type="alevin")`.
- `gencode/gencode.v48.annotation.gtf.gz`: 56 MB, already compressed.
- `kallisto/*/abundance.h5`: 38 MB total, redundant with `abundance.tsv.gz`
  (36.6 MB gz) since these runs have no bootstraps.
- `salmon_ec/*/aux_info/eq_classes.txt`: 40 MB disk / 12.3 MB gz
  (uncompressed text).

## Reduction plan

### Step 1: remove inf reps and redundant/unused large files

Remove (inf-rep items move to `with-inf-reps` branch / Zenodo):

- `salmon_gibbs/`
- `kallisto_boot/`
- `tx2gene.ensembl.v87.csv`
- `alevin/mouse1_unst_50_boot/`
- `alevin/*/alevin/bfh.txt`, `alevin/*/alevin/raw_cb_frequency.txt`
- `kallisto/*/abundance.h5` (no bootstraps; redundant with `abundance.tsv.gz`)
- `salmon_ec/*/aux_info/eq_classes.txt`

Result (measured by tarring the remaining 237 files): **~245 MB disk /
~194 MB gz**.

Remaining, gzipped:

| Entry | gz |
|---|---|
| `gencode/` GTF | 55.3 MB |
| `kallisto/` (tsv.gz only) | 36.6 MB |
| `oarfish/` | 28.6 MB |
| `rsem/` | 26.6 MB |
| `salmon/` | 18.0 MB |
| `salmon_dm/` | 11.7 MB |
| `salmon_ec/` | 4.1 MB |
| `sailfish/` | 4.1 MB |
| `cufflinks/` | 3.0 MB |
| tx2gene files (3) | ~2.3 MB |
| `refseq/` | 2.3 MB |
| `alevin/` | 1.7 MB |

Without the GENCODE GTF: ~139 MB gz.

### Step 2: further cuts to reach ~100 MB (candidates, not yet decided)

- Subset `gencode.v48.annotation.gtf.gz` to what the oarfish examples need
  (saves most of 55 MB).
- Drop RSEM `*.genes.results.gz`, keep isoforms (~8 MB).
- Gzip oarfish `*.ambig_info.tsv` (2.9 MB each) — check tximport reads gz.
- Reduce GEUVADIS sample sets (`kallisto`, `rsem`, `salmon`, `sailfish`,
  ~85 MB gz together) from 6 to e.g. 4 samples (~28 MB).

GTF subset + sample trimming should get close to 100 MB.

## Notes

- `external_data_store.txt` lists `inst/extdata` (Bioconductor external data
  store for large files).
- There is no `.Rbuildignore`; `CLAUDE.md` at top level will be included in
  the built tarball unless one is added (`^CLAUDE\.md$`).
- Before removing anything, check usage in downstream vignettes/tests:
  tximport, tximeta, fishpond, and others that call
  `system.file("extdata", package="tximportData")`.
