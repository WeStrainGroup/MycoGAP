# Installation

## Recommended: Conda package

Use a new environment to avoid conflicts between R, Bioconductor, HMMER, ITSx,
SeqKit, and VSEARCH.

```bash
conda create -n mycogap \
  -c westraingroup -c conda-forge -c bioconda \
  mycogap
conda activate mycogap
mycogap --help
```

`mamba` can replace `conda`. To require this release explicitly, add
`mycogap=1.7.0` to the create command.

The package includes the command, a frozen UNITE reference, and the curated
macrofungal genus list. No database download is started when MycoGAP runs.

## Source installation with Conda-managed dependencies

Use this method when the `westraingroup` channel is unavailable or when
testing a source checkout.

```bash
git clone https://github.com/WeStrainGroup/MycoGAP.git
cd MycoGAP
conda env create -f environment.yml
conda activate mycogap-source
chmod +x bin/mycogap
./bin/mycogap --help
```

Run the source command as `./bin/mycogap`. It resolves the bundled references
from `ref/` relative to the script.

## Manual installation without Conda

Manual installation is supported but less reproducible. Install:

- R 4.4 or later;
- R packages `optparse`, `tidyverse`, `data.table`, `fs`, `vegan`, and
  `R.utils`;
- Bioconductor packages `dada2`, `Biostrings`, `phyloseq`, and `microbiome`;
- SeqKit, ITSx, and VSEARCH on `PATH`.

Example R installation:

```r
install.packages(c(
  "optparse", "tidyverse", "data.table", "fs", "vegan", "R.utils"
))
install.packages("BiocManager")
BiocManager::install(c("dada2", "Biostrings", "phyloseq", "microbiome"))
```

Then clone the repository and run `./bin/mycogap`. Installation commands for
system tools vary by platform; verify that the actual executables are visible:

```bash
Rscript --version
seqkit version
ITSx --help
vsearch --version
./bin/mycogap --help
```

## Reference verification

For a source checkout:

```bash
test -r ref/macrofungi_list.csv
test -r ref/sh_general_release_dynamic_all_19.02.2025.fasta.gz
gzip -t ref/sh_general_release_dynamic_all_19.02.2025.fasta.gz
```

For an activated Conda package:

```bash
test -r "$CONDA_PREFIX/lib/R/library/mycogap/ref/macrofungi_list.csv"
test -r "$CONDA_PREFIX/lib/R/library/mycogap/ref/sh_general_release_dynamic_all_19.02.2025.fasta.gz"
```

## Offline or restricted systems

MycoGAP itself does not require network access during analysis. For an offline
installation, mirror or pre-download the Conda packages and transfer this
repository, including `ref/`. Record the package list after installation:

```bash
conda list --explicit > mycogap-explicit-spec.txt
conda list > mycogap-package-list.txt
```

Do not share environment exports that contain private channel credentials.

## Installation smoke test

`mycogap --help` checks that the entry point can start. A full installation
test should additionally run a small, locally authorized FASTQ subset and
confirm that:

- all seven progress steps finish;
- `1_dada2`, `2_vsearch`, `3_phyloseq`, and `PROJECT.log` are created;
- the final process exits with status 0;
- no unexpected stage-local external-tool error logs are present; and
- the command, environment, database hashes, runtime, and peak memory are
  archived.

The repository intentionally contains no human FASTQ test data.
