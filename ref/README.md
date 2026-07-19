# Packaged references

These files are installed with MycoGAP so a software release has a stable
default reference set. MycoGAP does not download or replace them at runtime.

## UNITE taxonomy FASTA

File:

```text
sh_general_release_dynamic_all_19.02.2025.fasta.gz
```

Metadata:

- UNITE version 10.0
- general FASTA release for all eukaryotes
- dynamic species hypotheses
- release date: 19 February 2025
- sequences in this archive: 156,820
- DOI: https://doi.org/10.15156/BIO/3301231
- SHA-256:
  `677670dedc05aa853cf12f6e58a9a4463b1e3cdb312ad8d519171b6e01da35f5`

## Edible-mushroom genus list

File: `macrofungi_list.csv`

- 611 genus names plus header
- required column: `Genus`
- compiled as the curated resource used by the Human Gut Mycobiome Atlas and
  provided as Supplementary Table S45
- taxonomy rationale supported by Li et al. (2021),
  https://doi.org/10.1111/1541-4337.12708
- SHA-256:
  `e42c5e6255399a0e38a8d19b54c94212220f7932ba4664bc9140e512cd24929a`

## Licensing and attribution

MycoGAP source code and project documentation are distributed under
CC BY-NC-SA 4.0. Third-party database records and taxonomic content may have
their own attribution and reuse terms. Consult the UNITE DOI record and source
release metadata before redistributing a modified database. Preserve the
citations, provenance, version, and checksums in scientific use.

See `docs/reference-databases.md` for user overrides and the maintainer update
procedure.
