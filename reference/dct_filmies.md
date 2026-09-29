# Taxonomic data of filmy ferns

Taxonomic data of filmy ferns (family Hymenophyllaceae) in Darwin Core
format. Non-ASCII characters have been converted to ASCII, so some
author names may not be as expected. Meant for demonstration purposes
only, not formal data analysis.

## Usage

``` r
dct_filmies
```

## Format

Dataframe (tibble), with 2451 rows and 5 columns. For details about data
format, see <https://dwc.tdwg.org/terms/#taxon>.

## Source

<https://www.catalogueoflife.org/>

## Details

Modified from data downloaded from the [Catalog of
Life](https://www.catalogueoflife.org/) under the [Creative Commons
Attribution (CC BY) 4.0](https://creativecommons.org/licenses/by/4.0/)
license.

## Examples

``` r
dct_filmies
#> # A tibble: 2,451 × 5
#>    taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName        
#>    <chr>    <chr>               <chr>           <chr>     <chr>                 
#>  1 54115096 NA                  accepted        species   Cephalomanes atrovire…
#>  2 54133783 54115097            synonym         species   Trichomanes crassum C…
#>  3 54115097 NA                  accepted        species   Cephalomanes crassum …
#>  4 54133784 54115098            synonym         species   Trichomanes densinerv…
#>  5 54115098 NA                  accepted        species   Cephalomanes densiner…
#>  6 54133786 54115100            synonym         species   Cephalomanes curvatum…
#>  7 54133787 54115100            synonym         species   Cephalomanes javanica…
#>  8 54133788 54115100            synonym         species   Cephalomanes oblongif…
#>  9 54133789 54115100            synonym         species   Cephalomanes zollinge…
#> 10 54133790 54115100            synonym         species   Lacostea javanica (Bl…
#> # ℹ 2,441 more rows
```
