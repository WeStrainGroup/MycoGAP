# Usage

## 1. Activate the environment

```bash
conda activate mycogap
mycogap --help
```

When running from a source checkout, substitute `./bin/mycogap` for
`mycogap`.

## 2. Inspect input names

Paired-end example:

```text
P01.1.fq.gz
P01.2.fq.gz
P02.1.fq.gz
P02.2.fq.gz
```

MycoGAP uses R regular expressions, not shell globs. Quote patterns to prevent
shell expansion. Forward and reverse files are sorted, counted, and compared
after removing their respective patterns.

## 3. Run paired-end data

```bash
mycogap \
  --project tutorial \
  --input /data/project/raw_its \
  --output /data/project/mycogap_output \
  --seq_type PE \
  --pattern_f '\.1\.fq\.gz$' \
  --pattern_r '\.2\.fq\.gz$' \
  --marker Auto \
  --thread 16 \
  --log_mode progress
```

Do not reuse an output directory from an interrupted or completed run unless
you have reviewed which intermediates already exist. A new output directory
is easiest to audit.

## 4. Choose the biological output

- `3_phyloseq/all/`: all kingdoms after general annotation and abundance QC.
- `3_phyloseq/microfungi/`: fungal community after the selected macrofungal
  filter.
- `3_phyloseq/vegetable/macrofungi/`: putative macrofungal features removed by
  the selected strategy, when present.
- `3_phyloseq/vegetable/plant/`: plant features, when present.

For non-gut fungal datasets, first consider:

```bash
mycogap ... \
  --macrofungi_filter none \
  --ref /path/to/environment-appropriate-reference.fasta.gz
```

Disabling macrofungal filtering changes only the downstream category split;
it does not change DADA2, ITSx, taxonomy, or VSEARCH processing.

The default writes only unfiltered genus/phylum `p0` OTU and taxonomy tables.
Request the standard prevalence-filtered OTU and taxonomy set only when
needed:

```bash
mycogap ... --prev_filter standard
```

Add CLR versions of p0 and the selected prevalence tables independently:

```bash
mycogap ... --prev_filter standard --clr T
```

Or request custom prevalence fractions:

```bash
mycogap ... --prev_filter 0.02,0.05
```

## 5. Inspect the run

```bash
find /data/project/mycogap_output -maxdepth 2 -type d | sort
tail -n 50 /data/project/mycogap_output/tutorial.log
```

The run log is named from `--project`. Unexpected warnings and stopping errors
are recorded in it. A failed ITSx or VSEARCH call additionally writes complete
diagnostics beside that stage's output files.

## Cluster execution

Use the scheduler provided by your institution. A generic non-scheduler
example is:

```bash
nohup mycogap ... > /dev/null 2>&1 &
echo $! > mycogap.pid
```

MycoGAP still writes `OUTPUT/PROJECT.log`; the redirection above only detaches
the process. For reproducibility, preserve the full command and do not infer
parameters later from memory.
