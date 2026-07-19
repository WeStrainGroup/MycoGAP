# Troubleshooting

## Reference database does not exist

Example:

```text
Error: taxonomy reference database does not exist: ...
```

Activate the environment before running the command:

```bash
conda activate mycogap
echo "$CONDA_PREFIX"
mycogap --help
```

For a source checkout, run `./bin/mycogap` without moving it away from the
repository's `ref/` directory, or pass `--ref` explicitly.

## No FASTQ files matched

Input patterns are R regular expressions. Test the intended suffix and quote
it:

```bash
find /path/to/input -maxdepth 1 -type f | head
mycogap ... --pattern_f '\.1\.fq\.gz$'
```

Do not use a shell glob such as `*.fq.gz` unquoted.

## Forward/reverse names do not pair

MycoGAP removes `--pattern_f` and `--pattern_r` from sorted basenames and
compares the remaining sample identifiers. Confirm equal counts, suffixes,
lane naming, and zero-byte files. Avoid patterns that match text outside the
intended suffix.

## ITSx produced no selected marker

Inspect `PROJECT.log` and `1_dada2/itsx/`. If ITSx returned a non-zero exit
status, inspect `1_dada2/itsx/PROJECT_itsx_error.log`. Possible causes include:

- no recognizable ITS region after upstream filtering;
- an explicitly requested marker that is absent;
- over-trimmed flanking regions;
- an environment or HMMER/ITSx installation problem; or
- unusual non-target amplicons.

Do not raise `--itsx_e` without documenting and validating the biological
effect.

## No sequences remained after the 50-bp or chimera filter

Review raw/filtered read counts, DADA2 error plots, merge success, extracted
ITS lengths, and the ITSx log. This is a fatal data/result condition; bypassing
the check would let invalid empty objects fail later with less useful errors.

## VSEARCH failed or produced empty outputs

Inspect `PROJECT.log`, `2_vsearch/PROJECT_vsearch_error.log` when present,
available disk space, the unclustered FASTA, and `vsearch --version`. MycoGAP
requires both a non-empty centroid FASTA and UC mapping after a successful
exit.

## No macrofungi or plant reads detected

This is an expected note, not a pipeline failure. Optional category folders
may be absent. The protective `try` blocks allow the workflow to finish when a
dataset contains no reads from one of these groups.

## Richness warning about no singletons

General low-abundance filtering can remove singletons before
`estimate_richness`. The warning indicates that richness estimates, especially
Observed richness, describe the filtered object and should not be treated as
an unbiased estimate of unseen diversity. Progress mode hides the repeated
known warning but does not change the calculation. The rationale and threshold
must still be reported.

## A taxonomy call changes between identical runs

RDP bootstrap classification can vary under multithreading near
`--minboot`. Compare exact sequences and abundances, inspect
`*_taxa_boot.csv`, and rerun taxonomy under the same conditions before
attributing the difference to another code change.

## Process was killed without an R error

Check the scheduler for out-of-memory or wall-time termination. DADA2 error
learning and taxonomy can require substantial memory. Reduce `--thread` if
per-thread memory or scheduler policy is limiting, and use a representative
subset to estimate resources.

## Rerunning after interruption

MycoGAP retains intermediates but is not a checkpoint/resume engine. Existing
files can be overwritten at different stages, creating a directory with mixed
provenance. Prefer a new output directory, then compare or remove the failed
directory only after confirming it is safe to do so.

## Getting useful support

Provide:

- the full command with private paths anonymized;
- MycoGAP and package versions;
- sequencing type and a filename example;
- the visible error and relevant tool log tail;
- scheduler exit reason and resources; and
- whether the issue reproduces on a small authorized subset.

Never attach human FASTQ data or credentials to a public issue.
