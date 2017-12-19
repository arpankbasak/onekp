[![Travis-CI Build Status](https://travis-ci.org/arendsee/oneKP.svg?branch=master)](https://travis-ci.org/arendsee/oneKP)
[![Coverage Status](https://img.shields.io/codecov/c/github/arendsee/oneKP/master.svg)](https://codecov.io/github/arendsee/oneKP?branch=master)

# Access 1000 plants dataset

The [1000 Plants initiative
(1KP)](https://sites.google.com/a/ualberta.ca/onekp/) provides the
transcriptome sequences to over 1000 plants from diverse lineages. This package
provides an R interface to the project metadata and raw sequence data.

## Installation


```r
library(devtools)
install_github('arendsee/oneKP')
```


## Examples

Retrieve the protein and gene transcript FASTA files for two 1KP transcriptomes: 


```r
onekp <- retrieve_oneKP()
seqs <- filter_by_code(onekp, c('URDJ', 'ROAP'))
download_proteins(seqs)
download_nucleotides(seqs)
```

This will create the following directory:

```
oneKP
 ├── Nucleotides
 │   ├── ROAP.fna
 │   └── URDJ.fna
 └── Peptides
     ├── ROAP.faa
     └── URDJ.faa
```

`oneKP` can also filter by species names, taxon ids, or clade.


```r
# filter by species name
filter_by_species(onekp, 'Pinus radiata')

# filter by species NCBI taxon ID
filter_by_species(onekp, 3347)

# filter by clade name scientific name (get all data for the Brassicaceae family)
filter_by_clade(onekp, 'Brassicaceae')

# filter by clade NCBI taxon ID
filter_by_clade(onekp, 3700)
```

So to get the protein sequences for all species in Brassicaceae:


```r
onekp <- retrieve_oneKP()
seqs <- filter_by_clade(onekp, 'Brassicaceae')
download_peptides(seqs)
download_nucleotides(seqs)
```


## Caveats

The dataset is a little dirty.

I attempt to map species to NCBI taxonomy IDs, but this fails for 95 out of
1171 taxa. Many of the species are either ambiguous or pool data across species
(e.g. *Hemerocallis sp.*). Worse, some are mis-named: "Kalanchoe
crenato-diagremontiana", which presumably refers to *Kalanchoe crenato* and the
sister species *Kalanchoe daigremontiana* (which they misspelled). Others are
just weird:

 * *Ettlia(?) oleoabundans*
 * *coccoid-prasinophyt*
 * *Ribes aff. giraldii* - *Rives giraldii* is a species, but what is *aff.*?

Others use names that are not from the NCBI taxonomy, e.g.

 * *Tribulus eichlerianus*
 * *Chlorochytridion tuberculata*
 * *Oenothera suffulta suffulta* - *Oenothera suffulta* is in NCBI common tree

Another issue is inconsistency in tissue naming.

The tissue column is of great biological importance and but is unfortunately
not very well standardized. For example, the following tissue types are included:

 1. young leaf AND shoot
    - 'young leaves and shoot'
    - 'young leaves and shoots'
 2. leaf
    - 'leaf'
    - 'Leaf'
    - 'leaves'
 3. leaf AND flower
    - 'leaf and flower'
    - 'leaves and flowers'
 4. flower AND stem AND leaf


```r
library(oneKP)
onekp <- retrieve_oneKP()
onekp@table %>%
    subset(grepl('lea[vf]', tissue, ignore.case=TRUE, perl=TRUE)) %>%
    subset(grepl('stem', tissue, ignore.case=TRUE)) %>%
    subset(grepl('flower', tissue, ignore.case=TRUE)) %>%
    subset(!grepl('bud|fruit|young|apex|devel', tissue, ignore.case=TRUE, perl=TRUE)) %$%
    tissue %>% unique %>% sort
```

```
##  [1] "flower, stem, leaves"   "flowers, leaves, stem" 
##  [3] "flowers, leaves, stems" "flowers, stem, leaves" 
##  [5] "flowers, stems, leaves" "leaf, stem, flower"    
##  [7] "leaf, stem, flowers"    "leaves, stem, flower"  
##  [9] "leaves, stem, flowers"  "leaves,stem, flowers"
```

Additionally, many of the entries are entirely missing a tissue annotation or
the annotation appears to be truncated (e.g. 'the little turrets (so mix of
young sporoph').

Making sense of all this would require either actually reading the tissue
annotations or performing fancy computational linguistics.

## Future Directions

I currently access the data by scraping URLs from an HTML table on the 1KP
website. It would be better to get the data from a more stable source. Luckily,
the 1KP data is managed by iPlant/CyVerse. Refactoring `onekp` to directly
access CyVerse would be a mostly internal change that should not affect the
API. It would, however, add an iRODs dependency and break portability to
Windows.
