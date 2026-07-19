# CLI reference

Run `mycogap --help` for the installed version. This page documents version
1.7.0.

## Input and naming

| Option | Type | Default | Validation and effect |
| --- | --- | --- | --- |
| `--project` | text | none | Required. Prefix for key output files. |
| `--input` | path | none | Required existing FASTQ directory. |
| `--output` | path | none | Required. Created recursively when absent. |
| `--seq_type` | choice | `PE` | Must be `PE`, `SE`, or `PB`. |
| `--marker` | choice | `Auto` | `Auto`, `ITS1`, `ITS2`, or `full`. |
| `--pattern_f` | regex | none | Required forward or single-read filename pattern. |
| `--pattern_r` | regex | none | Required reverse pattern; must equal forward for SE/PB. |

`Auto` selects the non-empty ITSx output with the largest file size. An
explicit marker overrides detection after a visible message, but the selected
ITSx FASTA must exist and be non-empty.

For PE data, matched forward and reverse counts and sample identifiers must be
identical after removing their patterns. Use anchored, quoted regular
expressions such as `'_R1_001\.fastq\.gz$'`.

## DADA2 and ITS processing

| Option | Type | Default | Allowed range and effect |
| --- | --- | --- | --- |
| `--maxee_f` | number | `5` | `>= 0`; DADA2 forward/single-read `maxEE`. |
| `--maxee_r` | number | `5` | `>= 0`; reverse `maxEE`; equal to forward for SE/PB. |
| `--itsx_e` | number | `0.01` | `>= 0`; ITSx domain-hit E-value. |
| `--thread` | integer | `8` | `>= 1`; passed to supported parallel stages. |

The tool also uses fixed DADA2 settings `truncQ = 2`, `maxN = 0`, and
`rm.phix = TRUE`. It does not impose a fixed truncation length, because ITS
length is highly variable.

For PE data, pairs are merged with overhang trimming. Unmerged pairs are not
concatenated and are not replaced with a single read.

## Taxonomy and clustering

| Option | Type | Default | Allowed range and effect |
| --- | --- | --- | --- |
| `--ref` | path | packaged UNITE | Existing DADA2-compatible FASTA or FASTA.GZ. |
| `--minboot` | number | `50` | `0` to `100`; RDP bootstrap threshold. |
| `--vsearch_id` | number | `0.985` | `0` to `1`; VSEARCH cluster identity. |

Taxonomy uses `dada2::assignTaxonomy` with reverse-complement checking and
batches of `100 x thread` sequences. The default reference contains all
eukaryotes so co-amplified plant and other non-fungal reads can be identified.

Multithreaded RDP bootstrapping is not guaranteed to reproduce every
bootstrap value bit-for-bit. Features close to `--minboot` may occasionally
cross the reporting threshold even when all inputs and parameters are
unchanged.

## Downstream filtering

| Option | Type | Default | Allowed range and effect |
| --- | --- | --- | --- |
| `--filter_abundance` | number | `0.0001` | `0` to `1`; within-sample relative abundance below this value is set to zero. |
| `--filter_depth` | integer | `5000` | `>= 0`; minimum usable microfungal reads for `_fdp` outputs. |
| `--prev_filter` | text | `none` | `none`, `standard`, or comma-separated fractions in `(0, 1]`. |
| `--clr` | choice | `F` | `T` or `F`; `T` writes CLR versions of p0 and selected prevalence tables. |
| `--macrofungi_filter` | choice | `dual` | `dual`, `list`, `agaricomycetes`, or `none`; `agar` is an alias. |
| `--macrofungi_list` | path | packaged CSV | Required for `dual` and `list`; must have a `Genus` column. |

The abundance threshold is applied independently within each sample after SH
clustering and preliminary annotation QC. Features that sum to at most one
read after this step are removed. Samples with zero remaining reads are
removed from downstream objects.

`--filter_depth` affects filenames containing `_fdp`. Non-depth-filtered
microfungal outputs remain available in the same run.

`--prev_filter none` writes only unfiltered `p0` genus/phylum OTU and taxonomy
tables. `--prev_filter standard` additionally writes `p1`, `p5`, and `p10`
OTU and taxonomy tables. A custom value such as `--prev_filter 0.02,0.05`
writes the corresponding `p2` and `p5` tables. CLR is independent: set
`--clr T` to write CLR versions of p0 and every selected threshold. With the
default `--clr F`, no CLR matrices are calculated.

## Logging

| Option | Values | Default | Effect |
| --- | --- | --- | --- |
| `--log_mode` | `progress`, `verbose` | `progress` | Concise steps or complete debug output; both write `OUTPUT/PROJECT.log`. |

Progress mode filters only explicitly recognized harmless messages. Errors,
external-tool failures, and unrecognized warnings are not hidden. Verbose mode
retains complete R code and external-tool output for debugging. Both modes
write one `OUTPUT/PROJECT.log`.

## Fixed biological defaults

The following are deliberately not exposed as CLI parameters in 1.7.0:

- minimum extracted ITS length: 50 bp;
- chimera method: DADA2 `consensus`;
- chimera fold-parent threshold: 2 for PE/SE and 3.5 for PacBio;
- VSEARCH strand search: both;
- genus/phylum `p0` OTU and taxonomy output.

Changing these values is a core algorithm modification and requires a focused
validation rather than an undocumented script edit.
