# tximportData

Bioconductor ExperimentData package holding example quantifier output for
tximport / tximeta / fishpond vignettes and tests. All data lives in
`inst/extdata/`; there is no R code. Details of software versions and calls
are in `vignettes/tximportData.Rmd`.

## Size reduction (v1.41.1, 2026-09-28)

Goal was a built tarball of ~100 MB. Result:

- before (v1.41.0): `inst/extdata` ~593 MB disk / ~388 MB gz
- after (v1.41.1): `inst/extdata` ~171 MB disk / ~96 MB gz;
  `R CMD build` tarball 100,303,405 bytes (95.7 MiB)

The full pre-reduction data (commit `02887f7`) is on the `with-inf-reps`
branch and tag at https://github.com/mikelove/tximportData and is released on
Zenodo: https://doi.org/10.5281/zenodo.22982575. All work happens on
`devel`; do not modify `with-inf-reps`.

Remotes: `origin` is GitHub (`git@github.com:mikelove/tximportData.git`),
`upstream` is Bioconductor git.

### What was removed in v1.41.1

- `salmon_gibbs/` (Salmon 0.8.1, Ensembl v87, 5 Gibbs samples, 6 samples)
- `kallisto_boot/` (kallisto 0.43.0, Ensembl v87, 5 bootstraps, 6 samples)
- `tx2gene.ensembl.v87.csv`
- `gencode/gencode.v48.annotation.gtf.gz` (tximeta now accepts a
  DataFrame linking transcripts to genes instead of a GTF)
- `alevin/{mouse1_unst_50,mouse1_LPS2_50}/alevin/bfh.txt` and
  `raw_cb_frequency.txt`
- `kallisto/*/abundance.h5` (no bootstraps; `abundance.tsv.gz` kept)
- GEUVADIS samples `ERR188297`, `ERR188329`, `ERR188288`, `ERR188356` from
  `kallisto/`, `rsem/`, `salmon/`, `sailfish/`; rows removed from
  `samples.txt` and `samples_extended.txt`

Kept on purpose: `salmon_ec/*/aux_info/eq_classes.txt` (fishpond
`salmonEC` test), `alevin/mouse1_unst_50_boot/` (2.2 MB; used by eds),
`cufflinks/` unchanged with all 6 samples.

## Current datasets

Sizes: "disk" = `du -sh`, "gz" = size of `tar czf` of that entry.

| Entry | Disk | gz | Contents |
|---|---|---|---|
| `oarfish/` | 35 MB | 28.6 MB | oarfish 0.9.0, SG-NEx H9 cDNA reps 2-4 (GENCODE v48 + 11,000 novel) |
| `salmon_ec/` | 59 MB | 16.5 MB | Salmon 1.1.0 `--dumpEq`, 2 Tasic 2018 samples, `eq_classes.txt`, `tx2gene_tasic.csv` |
| `kallisto/` | 12 MB | 12.2 MB | kallisto 0.43.1, GEUVADIS x2, GENCODE v27, `abundance.tsv.gz` only |
| `salmon_dm/` | 14 MB | 11.7 MB | Salmon 0.14.1, Drosophila SRR1197474 (+ `.plus`), GTFs, linkedTxome headers |
| `rsem/` | 8.3 MB | 8.3 MB | RSEM 1.2.31, GEUVADIS x2, gene + isoform results |
| `salmon/` | 6.1 MB | 6.0 MB | Salmon 0.8.2, GEUVADIS x2, GENCODE v27 |
| `alevin/` | 8.3 MB | 3.9 MB | alevin 1.6.0, 50 mouse cells: `mouse1_LPS2_50`, `mouse1_unst_50`, `mouse1_unst_50_boot` (mean/var mats) |
| `cufflinks/` | 7.9 MB | 3.0 MB | Cufflinks 2.2.1 cuffnorm isoform tables, 6 GEUVADIS samples (`q1_0`..`q6_0`) |
| `refseq/` | 2.9 MB | 2.3 MB | Salmon 0.99.0, ERR188021, RefSeq |
| `sailfish/` | 3.3 MB | 1.4 MB | Sailfish 0.9.0, GEUVADIS x2 |
| `tx2gene.gencode.v27.csv` | 7.0 MB | 1.2 MB | tx2gene for GENCODE v27 |
| `tx2gene_alevin.tsv` | 5.8 MB | 0.9 MB | tx2gene for alevin |
| `tx2gene.csv` | 852 KB | 0.2 MB | tx2gene (RefSeq/UCSC) |
| `samples.txt`, `samples_extended.txt` | 8 KB | ~0 | 2 GEUVADIS samples |

`samples.txt` now has 2 rows, in this order: `ERR188088` (NA20504),
`ERR188021` (NA20508).

Possible further cuts if needed: drop RSEM `*.genes.results.gz`; gzip oarfish
`*.ambig_info.tsv` (check tximport reads gz).

## Notes

- `external_data_store.txt` lists `inst/extdata` (Bioconductor external data
  store for large files).
- `.Rbuildignore` excludes `CLAUDE.md`.
- Local R lacks the `markdown` package, so `R CMD build` fails on the
  vignette; use `R CMD build --no-build-vignettes` and knit separately.

## Downstream migration (copy into sessions for each repo)

Applies to tximportData >= 1.41.1. Local clones are under `~/bioc/`
(`tximport/tximport`, `tximeta/tximeta`, `fishpond/fishpond`, `fishpond/eds`,
`DESeq2/DESeq2`, `satuRn`). Line numbers are from the devel clones on
2026-09-28.

General changes for all repos:

- `samples.txt` has 2 rows, not 6. Replace `paste0("sample",1:6)` with
  `paste0("sample", seq_along(files))` (or `1:2`), and replace
  `rep(c("A","B"), each=3)` with `factor(c("A","B"))` (1 per group).
- `salmon_gibbs/`, `kallisto_boot/`, `tx2gene.ensembl.v87.csv`, the GENCODE
  v48 GTF, kallisto `abundance.h5`, and alevin `bfh.txt` no longer exist.
  For inferential replicates, point readers to
  https://doi.org/10.5281/zenodo.22982575, or use another package with Gibbs
  samples (e.g. `macrophage`).
- Consider bumping `Suggests: tximportData (>= 1.41.1)`.

### tximport

- `vignettes/tximport.Rmd`: every `names(files) <- paste0("sample",1:6)`
  (lines ~100, 207, 218, 246, 267, 300, 313, 323, 493) -> 2 samples.
- `vignettes/tximport.Rmd` ~240-280: "Salmon with inferential replicates"
  uses `salmon_gibbs`; "kallisto" uses `kallisto_boot/*/abundance.h5`;
  "kallisto with inferential replicates" uses `kallisto_boot`. Replace with
  `macrophage` Gibbs data, or `eval=FALSE` chunks plus the Zenodo link. The
  `abundance.h5` example can point to `abundance.tsv.gz`.
- `R/tximport.R` ~236-238 roxygen example (and `man/tximport.Rd` ~247):
  `paste0("sample",1:6)` -> 2 samples; re-document.
- `tests/testthat/test_h5.R`: uses `kallisto_boot/*/abundance.h5`. No h5
  files remain in tximportData; skip, or use another source for h5.
- `tests/testthat/test_inf_reps.R`: uses `salmon_gibbs` and
  `tx2gene.ensembl.v87.csv`; skip, or move to `macrophage`.
- `test_salmon.R`, `test_kallisto.R`, `test_rsem.R`, `test_sparse.R`,
  `test_no_tx2gene.R`, `test_one_sample.R`, `test_counts_from_abundance.R`:
  `paste0("sample",1:6)` -> 2 samples; check hard-coded sample indices
  (e.g. `[,3]`, `sample6`).
- `test_alevin.R`: uses `list.files(alevin)[1]` = `mouse1_LPS2_50`; still
  present, should work.

### tximeta

- `tests/testthat/test_mixed_reference.R` ~16-28 and
  `vignettes/tximeta.Rmd` ~548-561: `makeLinkedTxome(gtf=...)` uses
  `extdata/gencode/gencode.v48.annotation.gtf.gz`, which is gone. Switch to
  the new DataFrame-based tx-to-gene option. The oarfish quant files are
  still in `extdata/oarfish/`.
- `tests/testthat/test_tximeta.R`: `names=paste0("sample",1:6)` at ~92, 114,
  148, 195, 201 -> 2 samples. `salmon_gibbs` at ~113 and ~147 and
  `kallisto_boot` at ~200 are gone (these are inside `if (FALSE)` blocks, but
  update or delete them).
- `test_alevin.R`, `test_skipmeta.R` (`salmon_dm`): data still present.

### fishpond

- `tests/testthat/test_alevinEC.R`: reads `alevin/*/alevin/bfh.txt`
  (removed). The test is gated on `packageVersion("tximportData") >=
  "1.23.4"`; change to also require `< "1.41.1"`, or remove / use other data.
- `tests/testthat/test_salmonEC.R`: `salmon_ec/*/aux_info/eq_classes.txt`
  kept, should work.
- `tests/testthat/test_swish.R` ~134 and `test_compress.R` ~65 reference
  `alevin/neurons_900_v014`, which was already removed before this change.
- `vignettes/swish.Rmd` ~751: `neurons_900_v014` in an `eval=FALSE` chunk;
  already stale, no change forced.

### eds

- `tests/testthat/test_readEDS.R` and `vignettes/eds.Rmd` use
  `list.files(alevin)[3]` = `mouse1_unst_50_boot`; kept, should work.

### DESeq2

- `vignettes/DESeq2.Rmd` ~319 and `tests/testthat/test_txi.R` ~8:
  `samples$condition <- factor(rep(c("A","B"),each=3))` -> 2 samples, e.g.
  `factor(c("A","B"))`. Only `DESeqDataSetFromTximport()` and
  `estimateSizeFactors()` follow (no `DESeq()` fit), so 1 sample per group
  is fine.

### satuRn (not maintained by us; fork at github.com/mikelove/satuRn)

- `vignettes/Vignette_eqclass.Rmd` ~101-105 reads `alevin/*/alevin/bfh.txt`
  (removed). Notify the maintainers, and point them to the Zenodo record.
