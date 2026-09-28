# tximportData 1.41.1

## Reduced package size

The package's example data has been cut from ~593 MB on disk (~388 MB
compressed) to ~171 MB on disk (~96 MB compressed). The built tarball is
now ~96 MiB.

The full data as it was before this change (commit `02887f7`) is kept on
the `with-inf-reps` branch and tag at
https://github.com/mikelove/tximportData and archived on Zenodo:
https://doi.org/10.5281/zenodo.22982575

### Removed

- `salmon_gibbs/`: Salmon 0.8.1 output with Gibbs samples (Ensembl v87,
  6 samples)
- `kallisto_boot/`: kallisto 0.43.0 output with bootstraps (Ensembl v87,
  6 samples)
- `tx2gene.ensembl.v87.csv`
- `gencode/gencode.v48.annotation.gtf.gz`. tximeta can now link
  transcripts to genes with a DataFrame, so it no longer needs a GTF.
- `alevin/{mouse1_unst_50,mouse1_LPS2_50}/alevin/bfh.txt` and
  `raw_cb_frequency.txt`
- `kallisto/*/abundance.h5` (no bootstraps). `abundance.tsv.gz` is kept.
- GEUVADIS samples `ERR188297`, `ERR188329`, `ERR188288` and `ERR188356`
  from `kallisto/`, `rsem/`, `salmon/` and `sailfish/`. `samples.txt` and
  `samples_extended.txt` now have 2 rows: `ERR188088` and `ERR188021`.

### Kept

- `salmon_ec/*/aux_info/eq_classes.txt` (used by fishpond)
- `alevin/mouse1_unst_50_boot/` (used by eds)
- `cufflinks/`, still with all 6 samples

### Changes needed in downstream code

- Code that reads `samples.txt` now gets 2 samples instead of 6. For example,
  replace `paste0("sample", 1:6)` with `paste0("sample", seq_along(files))`,
  and replace `rep(c("A","B"), each=3)` with `factor(c("A","B"))`.
- For example data with inferential replicates (Gibbs samples or
  bootstraps), use the Zenodo record above or another data package such as
  `macrophage`.
