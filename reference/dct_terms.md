# Darwin Core Taxon terms

A table of valid Darwin Core terms. Only terms in the Taxon class or at
the record-level are included.

## Usage

``` r
dct_terms
```

## Format

Dataframe (tibble), including two columns:

- `group`: Darwin Core term group; either "taxon" (terms in the Taxon
  class) or "record-level" (terms that are generic in that they might
  apply to any type of record in a dataset.)

- `term`: Darwin Core term

with two additional attributes:

- `retrieved`: Date the terms were obtained

- `url`: URL from which the terms were obtained

## Source

<https://dwc.tdwg.org/terms/#taxon>

## Details

Modified from data downloaded from [TDWG Darwin
Core](https://dwc.tdwg.org/) under the [Creative Commons Attribution (CC
BY) 4.0](https://creativecommons.org/licenses/by/4.0/) license.

## Examples

``` r
dct_terms
#> # A tibble: 47 × 2
#>    group term               
#>  * <chr> <chr>              
#>  1 taxon taxonID            
#>  2 taxon scientificNameID   
#>  3 taxon acceptedNameUsageID
#>  4 taxon parentNameUsageID  
#>  5 taxon originalNameUsageID
#>  6 taxon nameAccordingToID  
#>  7 taxon namePublishedInID  
#>  8 taxon taxonConceptID     
#>  9 taxon scientificName     
#> 10 taxon acceptedNameUsage  
#> # ℹ 37 more rows
```
