# Macrofungal filtering

## Purpose

Human gut ITS datasets frequently contain fungal DNA from edible mushrooms.
These reads are genuinely fungal, but they may represent recent dietary or
transient material rather than resident gut microfungi. Because
mushroom-forming morphology does not map perfectly onto higher taxonomy, no
single automatic rule is both completely sensitive and completely specific.

MycoGAP therefore exposes the filtering decision.

## Strategies

### `dual` (default)

Exclude the union of:

1. SHs whose annotated genus occurs in `ref/macrofungi_list.csv`; and
2. SHs assigned to class `c__Agaricomycetes`.

This is the manuscript strategy and matches the logic used for the Human Gut
Mycobiome Atlas. It reduces false negatives but may remove non-dietary
Agaricomycetes that are biologically relevant in another environment.

### `list`

Exclude only genera in the curated edible-mushroom list. This is more
conservative outside the gut and retains Agaricomycetes not represented in the
list, but it can miss unresolved genera and edible taxa absent from the list.

### `agaricomycetes`

Exclude the entire class `c__Agaricomycetes`. This captures the major
mushroom-forming lineage without relying on genus resolution, but it does not
capture listed macrofungal genera in other classes and may be overly broad.

### `none`

Do not remove any macrofungal features from the fungal output. Choose this for
soil, plant-associated, environmental, or macrofungal questions where
Agaricomycetes and mushroom-forming taxa are part of the target community.

With `none`, no features are designated for the optional macrofungi output; all
fungal features remain in the microfungi-named directory for backward-
compatible output organization. Interpret that directory according to the
selected strategy.

## Custom genus list

```bash
mycogap ... \
  --macrofungi_filter list \
  --macrofungi_list /project/references/macrofungi_list.csv
```

The file must be readable CSV with a column named exactly `Genus`. Values
must be plain genus names such as `Agaricus`; MycoGAP adds the `g__` taxonomy
prefix internally. Preserve a copy and checksum with the run.

```bash
shasum -a 256 /project/references/macrofungi_list.csv
```
