# Full outputs

All key filenames begin with `PROJECT_`, where `PROJECT` is the value passed
to `--project`.

## Directory overview

```text
OUTPUT/
├── 1_dada2/
│   ├── check/
│   ├── filtered/
│   ├── itsx/
│   └── result/
├── 2_vsearch/
├── 3_phyloseq/
│   ├── all/
│   ├── microfungi/
│   │   ├── ASV/
│   │   ├── genus/
│   │   └── phylum/
│   └── vegetable/
│       ├── macrofungi/ optional
│       └── plant/     optional
└── PROJECT.log
```

## `1_dada2/check`

- `*_fq_stats_raw.tsv`, `*_fq_stats_filtered.tsv`: SeqKit FASTQ summaries.
- `*_fq_stats_*1.jpg`: sample read-count distribution.
- `*_fq_stats_*2.jpg`: average read-length distribution.
- `*_fq_stats_*3.jpg`: average quality distribution.
- `*_quality_*.jpg`: DADA2 quality profiles for a subset of files.
- `*_error_F.png`, `*_error_R.png`, or `*_error.png`: learned DADA2 error
  profiles.

The exact forward/reverse plot set depends on sequencing type.

## `1_dada2/filtered`

DADA2-filtered FASTQ files. These are intermediate data and can be large.
Their presence supports auditing and reruns but increases storage use.

## `1_dada2/itsx`

- `*_asv_raw.fasta`: ASVs supplied to ITSx.
- `*_asv_itsx.ITS1.fasta`, `*.ITS2.fasta`, `*.full.fasta`: extracted marker
  outputs when recognized.
- ITSx summary, positions, and detailed-result files.

Only the selected marker output continues downstream. In `Auto` mode, the
largest non-empty marker FASTA by file size is selected.

## `1_dada2/result`

- `*_seqtab_raw.csv`: sequence table before ITS extraction, identical-ITS
  consolidation, and chimera removal.
- `*_seqtab.csv`: sequence table after ITS extraction, the 50-bp filter,
  identical-ITS consolidation, and chimera removal.
- `*_taxa.csv`: RDP/UNITE taxonomy for post-chimera ASVs.
- `*_taxa_boot.csv`: bootstrap support values.

Sequence strings are used as feature columns at this stage. The bootstrap
file is important when a taxon disappears after applying `--minboot`.

## `2_vsearch`

With the default `--vsearch_id 0.985`, names contain `c98.5`:

- `*_ASV_unclustered.fasta`: input ASV sequences with feature identifiers.
- `*_ASV_c98.5.fasta`: SH centroid sequences.
- `*_ASV_c98.5_index1.tsv`: VSEARCH UC cluster mapping.
- `*_ASV_c98.5_index2.tsv`: VSEARCH OTU table output.
- `*_seqtab_c98.5.csv`: reconstructed sample-by-SH feature table.
- `*_taxa_c98.5.csv`: centroid taxonomy used downstream.

The filename percentage changes with `--vsearch_id`.

## `3_phyloseq/all`

This directory includes all retained kingdoms after general annotation and
within-sample abundance QC. It is not an unfiltered raw table.

- `*_ps_all.rds`: phyloseq object.
- `*_otu_all.csv`: sample-by-SH feature table.
- `*_taxa_all.csv`: SH taxonomy.
- `*_refseq_all.fasta`: representative sequences.
- `*_readcount_all.csv`: sample read-count summary; the count column is named
  `readcount`.
- `*_diversity_all.csv`: Observed, Shannon, and Simpson estimates when
  calculation is possible.

## `3_phyloseq/microfungi/ASV`

Files without `_fdp` contain the fungal community after the selected
macrofungal strategy and general abundance QC. Files with `_fdp` also
require at least `--filter_depth` usable reads per sample.

- `*_ps_microfungi.rds`, `*_ps_microfungi_fdp.rds`
- `*_otu_microfungi.csv`, `*_otu_microfungi_fdp.csv`
- `*_taxa_microfungi.csv`, `*_taxa_microfungi_fdp.csv`
- `*_refseq_microfungi.fasta`, `*_refseq_microfungi_fdp.fasta`
- `*_readcount_microfungi.csv`, `*_readcount_microfungi_fdp.csv`
- `*_diversity_microfungi.csv`, `*_diversity_microfungi_fdp.csv`

Every `readcount` CSV remains separate from the corresponding diversity CSV
and uses `readcount` as its data-column header.

## Genus and phylum directories

For microfungi, macrofungi, and plant categories, MycoGAP writes aggregated
feature tables at genus and phylum levels. The default `--prev_filter none`
writes only the unfiltered OTU and taxonomy tables:

- `p0`: no prevalence aggregation.

Additional outputs are controlled by `--prev_filter`:

- `standard`: add 1%, 5%, and 10% prevalence-filtered OTU and taxonomy tables
  (`p1`, `p5`, and `p10`).
- comma-separated fractions such as `0.02,0.05`: add the requested 2% and 5%
  tables (`p2` and `p5`).
- `--clr T`: add centered log-ratio matrices using pseudocount 1 for p0 and
  every prevalence threshold selected by `--prev_filter`; the default
  `--clr F` writes no CLR matrices.
- `_fdp`: derived from the depth-filtered microfungal object.

Aggregation into `Other` preserves per-sample total counts. CLR tables are
transformed values and must not be treated as read counts.

## `3_phyloseq/vegetable`

The historical directory name is retained for compatibility.

- `macrofungi/`: features designated as putative macrofungi by the selected
  list/class strategy.
- `plant/`: features assigned to kingdom `k__Viridiplantae`.

Either directory may be absent when no corresponding reads are detected. This
is expected and does not mean the main workflow failed. With
`--macrofungi_filter none`, no features are designated for the macrofungi
branch.

## `PROJECT.log`

Both logging modes create one run-level log directly under `OUTPUT/`, named
from the exact value supplied to `--project`.

- progress mode records parameters, step start times, marker decisions,
  warnings, errors, and completion timing;
- verbose mode additionally records R code and objects plus DADA2, ITSx, and
  VSEARCH details.

SeqKit does not create a separate log. Only a failed ITSx or VSEARCH call
creates a stage-local `*_error.log` containing the complete tool diagnostics.

## Table orientation and safe use

Downstream `otu` tables are generally sample-by-feature, matching the
phyloseq object constructed with `taxa_are_rows = FALSE`. Verify the first
column and header rather than assuming orientation in a separate script.

Use the feature identifier to join abundance, taxonomy, and representative
sequence files. Do not join by row position after sorting or filtering.
