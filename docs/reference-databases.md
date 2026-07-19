# Reference databases

## Packaged taxonomy reference

MycoGAP 1.7.0 contains:

```text
ref/sh_general_release_dynamic_all_19.02.2025.fasta.gz
```

It is the UNITE 10.0 all-eukaryotes dynamic release dated 19 February 2025,
formatted for DADA2 taxonomy assignment. It contains 156,820 reference
sequences in this repository snapshot.

SHA-256:

```text
677670dedc05aa853cf12f6e58a9a4463b1e3cdb312ad8d519171b6e01da35f5
```

The all-eukaryotes scope is deliberate. It helps distinguish co-amplified
plants and other eukaryotes from fungi; a fungi-only reference can yield
ambiguous kingdom-level fungal calls for non-fungal queries under some
classifiers.

## Packaged macrofungal reference

```text
ref/macrofungi_list.csv
```

The file contains 611 curated edible-mushroom genera plus its header.

SHA-256:

```text
e42c5e6255399a0e38a8d19b54c94212220f7932ba4664bc9140e512cd24929a
```

The CSV column must be named `Genus`. Values are compared to genus-level
UNITE annotations after MycoGAP adds the `g__` prefix.

## Use another UNITE release

Do not overwrite an installed package to test a reference. Pass the path
explicitly:

```bash
mycogap ... --ref /references/unite/new_release.fasta.gz
```

Before use:

```bash
test -r /references/unite/new_release.fasta.gz
gzip -t /references/unite/new_release.fasta.gz
gzip -cd /references/unite/new_release.fasta.gz | head -n 2
shasum -a 256 /references/unite/new_release.fasta.gz
```

Confirm that FASTA headers carry semicolon-separated taxonomic ranks in a
format accepted by `dada2::assignTaxonomy`. Confirm whether the release is
dynamic or species-hypothesis-based and whether it covers fungi only or all
eukaryotes. These choices change classification behavior.
