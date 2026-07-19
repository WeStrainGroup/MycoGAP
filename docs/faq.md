# Frequently asked questions

## Does MycoGAP require primer sequences?

No. It can process trimmed or untrimmed ITS amplicons without prior primer
metadata. After DADA2 inference, ITSx identifies conserved rRNA structure and
extracts ITS1, ITS2, or full ITS. If reads were aggressively trimmed before
MycoGAP, short remaining flanks can still reduce ITSx recognition.

## What happens to paired reads that do not overlap?

They are discarded. MycoGAP does not concatenate them and does not substitute
R1 or R2 alone. This avoids introducing an artificial junction and mixing
different marker coverages in one feature table.

## Is `3_phyloseq/all` completely unfiltered?

No. It includes all retained kingdoms after preliminary annotation and
within-sample abundance QC. It is broader than the fungal/microfungal output,
but it is not the raw DADA2 table.

## Can I retain Agaricomycetes?

Yes. Use `--macrofungi_filter list` to retain Agaricomycetes not in the genus
list, or `--macrofungi_filter none` to retain all fungal taxa. Choose based on
the biological question and document the strategy.

## Can I use MycoGAP for soil or plant-associated fungi?

Yes, as an ITS-aware processing workflow, but gut-focused defaults may be
inappropriate. Select a suitable eukaryotic/fungal taxonomy reference, disable
or revise macrofungal filtering, and validate abundance/depth thresholds.

## Does MycoGAP automatically download the latest UNITE release?

No. Each release contains a frozen default reference for reproducibility. Pass
`--ref /path/to/reference.fasta.gz` to use another release. Maintainers update
the packaged default manually with a software/database release and regression.

## Does the mushroom filter use UNITE identifiers?

It uses taxonomy assigned from UNITE. Each clustered feature inherits an
annotation; its genus is compared with the curated CSV and/or its class is
compared with `c__Agaricomycetes`, according to
`--macrofungi_filter`.

## Is there a merged dietary output?

Version 1.7.0 retains separate optional macrofungi and plant
outputs under `3_phyloseq/vegetable/`. It does not introduce a new merged
dietary table.

## Can I choose another abundance or read-depth threshold?

Yes. Use `--filter_abundance` and `--filter_depth`. Non-depth-filtered
microfungal outputs are also written, allowing another downstream depth rule.
Changing thresholds changes the analyzed community and must be reported.

## Can I reduce the number of genus/phylum output files?

Yes. The default `--prev_filter none` writes only unfiltered `p0` OTU and
taxonomy tables and skips CLR calculation. Use `--prev_filter standard` for
the 1%, 5%, and 10% filtered OTU and taxonomy tables, or pass custom fractions
such as `--prev_filter 0.02,0.05`. Set `--clr T` independently to write CLR
versions of p0 and every selected threshold; the default is `--clr F`.

## Are 98.5% SHs species?

They are operational species hypotheses used to reduce ASV over-resolution.
The threshold is not a universal biological species definition.

## Does a low-identity UNITE match prove a novel species?

No. It shows limited representation in the selected reference release. Formal
novelty requires additional taxonomic and biological evidence.

## Why can one taxonomy call change between identical runs?

RDP bootstrap classification can vary with multithreading near `minBoot`.
Compare sequence and abundance layers first, inspect bootstrap values, and run
a focused repeat before interpreting the difference as a code regression.

## Why is the richness no-singleton warning absent in progress mode?

It is a known repeated consequence of calculating richness after low-
abundance filtering and is routed out of the concise display. The calculation
is unchanged. Users should still interpret Observed richness as richness of
the filtered object.
