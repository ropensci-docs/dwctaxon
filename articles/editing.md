# Editing DwC taxon data

dwctaxon has two major purposes, (1) editing and (2) validation of
taxonomic data in [Darwin Core (DwC)](https://dwc.tdwg.org/terms/#taxon)
format. This vignette is about the former. Although you could use
dwctaxon to build a taxonomic database from scratch, it is more likely
you will be using it to modify an existing database, so we will focus on
that kind of use-case.

We start by loading packages needed for this vignette:

``` r

library(dwctaxon)
library(tibble) # recommended for pretty-printing of tibbles
```

## The data

dwctaxon comes with an example dataset `dct_filmies`, taxonomic data of
filmy ferns ([family
Hymenophyllaceae](https://en.wikipedia.org/wiki/Hymenophyllaceae)).
Let’s take a quick look at the data (you may need to scroll to the right
of the frame with the code to see all the text):

``` r

dct_filmies
#> # A tibble: 2,451 × 5
#>    taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName                            
#>    <chr>    <chr>               <chr>           <chr>     <chr>                                     
#>  1 54115096 NA                  accepted        species   Cephalomanes atrovirens Presl             
#>  2 54133783 54115097            synonym         species   Trichomanes crassum Copel.                
#>  3 54115097 NA                  accepted        species   Cephalomanes crassum (Copel.) M. G. Price 
#>  4 54133784 54115098            synonym         species   Trichomanes densinervium Copel.           
#>  5 54115098 NA                  accepted        species   Cephalomanes densinervium (Copel.) Copel. 
#>  6 54133786 54115100            synonym         species   Cephalomanes curvatum (J. Sm.) V. D. Bosch
#>  7 54133787 54115100            synonym         species   Cephalomanes javanica (Bl.) V. D. Bosch   
#>  8 54133788 54115100            synonym         species   Cephalomanes oblongifolium Presl          
#>  9 54133789 54115100            synonym         species   Cephalomanes zollingeri V. D. Bosch       
#> 10 54133790 54115100            synonym         species   Lacostea javanica (Bl.) Prantl            
#> # ℹ 2,441 more rows
```

For demonstration purposes, we will just use the first five rows:

``` r

filmies_small <- head(dct_filmies, 5)
```

Although DwC taxon format includes a large number of terms
(columns)[^1], a typical database does not use all of them.
`dct_filmies` only includes 5 columns. Their usage should be clear to
most biologists, but two columns need more explanation. `taxonID` is a
unique ID for each row (name), and `acceptedNameUsageID` is only
provided for synonyms; it indicates the `taxonID` of the accepted name.
For more information on DwC taxon format, see
[`vignette("what-is-dwc")`](https://docs.ropensci.org/dwctaxon/articles/what-is-dwc.md).

The rest of the vignette will consist of modifying this dataset.

## Adding rows

### Adding rows by vector

[`dct_add_row()`](https://docs.ropensci.org/dwctaxon/reference/dct_add_row.md)
is used to add rows. The simplest way to do this is by specifying the
new values as vectors (vectors of length 1 are recycled):

``` r

filmies_small |>
  dct_add_row(
    scientificName = c("Homo sapiens", "Drosophila melanogaster"),
    taxonomicStatus = "accepted",
    taxonRank = "species"
  )
#> # A tibble: 7 × 6
#>   taxonID                          acceptedNameUsageID taxonomicStatus taxonRank scientificName                            modified         
#>   <chr>                            <chr>               <chr>           <chr>     <chr>                                     <chr>            
#> 1 54115096                         NA                  accepted        species   Cephalomanes atrovirens Presl             NA               
#> 2 54133783                         54115097            synonym         species   Trichomanes crassum Copel.                NA               
#> 3 54115097                         NA                  accepted        species   Cephalomanes crassum (Copel.) M. G. Price NA               
#> 4 54133784                         54115098            synonym         species   Trichomanes densinervium Copel.           NA               
#> 5 54115098                         NA                  accepted        species   Cephalomanes densinervium (Copel.) Copel. NA               
#> 6 be111923a2780f4931ca7fa4ed09a1b9 NA                  accepted        species   Homo sapiens                              2026-08-30 07:23…
#> 7 5942a5b04c33f577946e4ccfbe490800 NA                  accepted        species   Drosophila melanogaster                   2026-08-30 07:23…
```

Notice that although we did not specify `taxonID` or `modified`, these
columns are automatically filled by default[^2]; they can be turned off
by setting the `fill_taxon_id` and `stamp_modified` arguments to
`FALSE`.

The names of the new values should be valid DwC terms. You can see the
terms available with `dct_terms`:

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

### Adding rows by dataframe

Adding rows with vectors as shown above works well if you only need to
add a small number of rows. However, this could get unwieldy if you have
a large number to add. In this case, you can instead add them via a
dataframe.

The dataframe should have column names matching valid DwC taxon terms:

``` r

# Let's add some rows from the original dct_filmies
to_add <- tail(dct_filmies)

filmies_small |>
  dct_add_row(new_dat = to_add)
#> # A tibble: 11 × 6
#>    taxonID  acceptedNameUsageID taxonomicStatus taxonRank    scientificName                                              modified           
#>    <chr>    <chr>               <chr>           <chr>        <chr>                                                       <chr>              
#>  1 54115096 NA                  accepted        species      Cephalomanes atrovirens Presl                               NA                 
#>  2 54133783 54115097            synonym         species      Trichomanes crassum Copel.                                  NA                 
#>  3 54115097 NA                  accepted        species      Cephalomanes crassum (Copel.) M. G. Price                   NA                 
#>  4 54133784 54115098            synonym         species      Trichomanes densinervium Copel.                             NA                 
#>  5 54115098 NA                  accepted        species      Cephalomanes densinervium (Copel.) Copel.                   NA                 
#>  6 54126747 NA                  accepted        infraspecies Hymenophyllum myriocarpum var. endiviifolium (Desv.) Stolze 2026-08-30 07:23:4…
#>  7 54135528 54126748            synonym         species      Hymenophyllum nigrescens Liebm.                             2026-08-30 07:23:4…
#>  8 54135530 54126748            synonym         species      Mecodium nigricans (Presl ex Kl.) Copel.                    2026-08-30 07:23:4…
#>  9 54135531 54126748            synonym         species      Sphaerocionium nigricans Presl ex Kl.                       2026-08-30 07:23:4…
#> 10 54126748 NA                  accepted        infraspecies Hymenophyllum myriocarpum var. nigrescens (Liebm.) Stolze   2026-08-30 07:23:4…
#> 11 54126749 NA                  accepted        infraspecies Hymenophyllum trichophyllum var. buesii C. V. Morton        2026-08-30 07:23:4…
```

Note that in this case the `taxonID` already existed in the data to add,
so it is not generated automatically.

## Deleting rows

[`dct_drop_row()`](https://docs.ropensci.org/dwctaxon/reference/dct_drop_row.md)
drops one or more rows by `taxonID` or `scientificName`.

For example, we can exclude the row for *Cephalomanes atrovirens* Presl
by either using its `scientificName` (`Cephalomanes atrovirens Presl`)
or its `taxonID` (`54115096`):

``` r

filmies_small |>
  dct_drop_row(scientificName = "Cephalomanes atrovirens Presl")
#> # A tibble: 4 × 5
#>   taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName                           
#>   <chr>    <chr>               <chr>           <chr>     <chr>                                    
#> 1 54133783 54115097            synonym         species   Trichomanes crassum Copel.               
#> 2 54115097 NA                  accepted        species   Cephalomanes crassum (Copel.) M. G. Price
#> 3 54133784 54115098            synonym         species   Trichomanes densinervium Copel.          
#> 4 54115098 NA                  accepted        species   Cephalomanes densinervium (Copel.) Copel.

filmies_small |>
  dct_drop_row(taxonID = "54115096")
#> # A tibble: 4 × 5
#>   taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName                           
#>   <chr>    <chr>               <chr>           <chr>     <chr>                                    
#> 1 54133783 54115097            synonym         species   Trichomanes crassum Copel.               
#> 2 54115097 NA                  accepted        species   Cephalomanes crassum (Copel.) M. G. Price
#> 3 54133784 54115098            synonym         species   Trichomanes densinervium Copel.          
#> 4 54115098 NA                  accepted        species   Cephalomanes densinervium (Copel.) Copel.
```

Since it looks up values by `taxonID` or `scientificName`,
[`dct_drop_row()`](https://docs.ropensci.org/dwctaxon/reference/dct_drop_row.md)
requires these to be unique and non-missing in the taxonomic database.

Of course, since the taxonomic database is a dataframe, you could also
use other subsetting techniques like brackets in base R or
[`dplyr::filter()`](https://dplyr.tidyverse.org/reference/filter.html)
from the tidyverse to delete rows.

## Modifying rows

### Identifying rows to modify

[`dct_modify_row()`](https://docs.ropensci.org/dwctaxon/reference/dct_modify_row.md)
changes the values in an existing row.

Here, it is helpful to reiterate the purpose of the `taxonID` column: it
is a unique identifier for each row (taxonomic name) in the data. So we
will use `taxonID` to identify the row to change, then apply new values
using other DwC terms.

``` r

# Change the status of Trichomanes crassum Copel. to "accepted"
filmies_small |>
  dct_modify_row(
    taxonID = "54133783", # taxonID of Trichomanes crassum Copel.
    taxonomicStatus = "accepted"
  )
#> # A tibble: 5 × 6
#>   taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName                            modified                  
#>   <chr>    <chr>               <chr>           <chr>     <chr>                                     <chr>                     
#> 1 54115096 NA                  accepted        species   Cephalomanes atrovirens Presl             NA                        
#> 2 54133783 NA                  accepted        species   Trichomanes crassum Copel.                2026-08-30 07:23:42.225346
#> 3 54115097 NA                  accepted        species   Cephalomanes crassum (Copel.) M. G. Price NA                        
#> 4 54133784 54115098            synonym         species   Trichomanes densinervium Copel.           NA                        
#> 5 54115098 NA                  accepted        species   Cephalomanes densinervium (Copel.) Copel. NA
```

Notice there were some additional automatic changes besides just
`taxonomicStatus`. Since the new status is `"accepted"`, dwctaxon
automatically set `acceptedNameUsageID` (which indicates the `taxonID`
of the accepted name for synonyms) to `NA`. This behavior can be
disabled by setting the `clear_usage_id` argument to `FALSE`. We see the
`modified` field has been updated as well.

However, it can be difficult for humans to keep track of which `taxonID`
matches which name; typically, we think in terms of species names, not
ID numbers. For that reason, you can also use `scientificName` instead
of `taxon_id` to specify a row to modify[^3].

``` r

# Change the status of Trichomanes crassum Copel. to "accepted"
filmies_small |>
  dct_modify_row(
    scientificName = "Trichomanes crassum Copel.",
    taxonomicStatus = "accepted"
  )
#> # A tibble: 5 × 6
#>   taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName                            modified                  
#>   <chr>    <chr>               <chr>           <chr>     <chr>                                     <chr>                     
#> 1 54115096 NA                  accepted        species   Cephalomanes atrovirens Presl             NA                        
#> 2 54133783 NA                  accepted        species   Trichomanes crassum Copel.                2026-08-30 07:23:42.297892
#> 3 54115097 NA                  accepted        species   Cephalomanes crassum (Copel.) M. G. Price NA                        
#> 4 54133784 54115098            synonym         species   Trichomanes densinervium Copel.           NA                        
#> 5 54115098 NA                  accepted        species   Cephalomanes densinervium (Copel.) Copel. NA
```

If you provide **both** `taxonID` and `scientificName`, dwctaxon will
identify the row with `taxonID` and apply `scientificName` as the new
scientific name:

``` r

# Change the name of Trichomanes crassum Copel.
filmies_small |>
  dct_modify_row(
    taxonID = "54133783", # taxonID of Trichomanes crassum Copel.
    scientificName = "Bogus name"
  )
#> # A tibble: 5 × 6
#>   taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName                            modified                  
#>   <chr>    <chr>               <chr>           <chr>     <chr>                                     <chr>                     
#> 1 54115096 NA                  accepted        species   Cephalomanes atrovirens Presl             NA                        
#> 2 54133783 54115097            synonym         species   Bogus name                                2026-08-30 07:23:42.359543
#> 3 54115097 NA                  accepted        species   Cephalomanes crassum (Copel.) M. G. Price NA                        
#> 4 54133784 54115098            synonym         species   Trichomanes densinervium Copel.           NA                        
#> 5 54115098 NA                  accepted        species   Cephalomanes densinervium (Copel.) Copel. NA
```

### Automatic re-mapping of synonyms

Another convenient automated behavior of dwctaxon is the ability to
“re-map” synonyms. That is, if a previously accepted name (say, “A”) is
changed to be the synonym of another name (say, “B”), all synonyms of
“A” are also changed to be synonyms of “B”. Let’s see how this works
with the example data:

``` r

# Change C. densinervium to a synonym of C. crassum
filmies_small |>
  dct_modify_row(
    scientificName = "Cephalomanes densinervium (Copel.) Copel.",
    taxonomicStatus = "synonym",
    acceptedNameUsage = "Cephalomanes crassum (Copel.) M. G. Price"
  )
#> # A tibble: 5 × 6
#>   taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName                            modified                  
#>   <chr>    <chr>               <chr>           <chr>     <chr>                                     <chr>                     
#> 1 54115096 NA                  accepted        species   Cephalomanes atrovirens Presl             NA                        
#> 2 54133783 54115097            synonym         species   Trichomanes crassum Copel.                NA                        
#> 3 54115097 NA                  accepted        species   Cephalomanes crassum (Copel.) M. G. Price NA                        
#> 4 54133784 54115097            synonym         species   Trichomanes densinervium Copel.           2026-08-30 07:23:42.440331
#> 5 54115098 54115097            synonym         species   Cephalomanes densinervium (Copel.) Copel. 2026-08-30 07:23:42.422082
```

Notice that **two** names were modified even though we only specified
one; since *Trichomanes densinervium* Copel. was a synonym of
*Cephalomanes densinervium* (Copel.) Copel., it also gets **re-mapped**
to the accepted name *Cephalomanes crassum* (Copel.) M. G. Price

## Filling columns

As described in
[`vignette("what-is-dwc")`](https://docs.ropensci.org/dwctaxon/articles/what-is-dwc.md),
there are several terms in DwC that I call “term - termID” pairs, e.g.,
`acceptedNameUsage` and `acceptedNameUsageID`, `parentNameUsage` and
`parentNameUsageID`, etc. Typically, one is an actual scientific name
(e.g., for `acceptedNameUsage`, the accepted name of a synonym), and one
is the `taxonID` of that name (e.g., for `acceptedNameUsageID`, the
`taxonID` of the accepted name of a synonym). It is up to the manager of
the database to choose whether to use either or both of the terms in the
pair.

This sort of data is redundant and could be prone to error if entered
manually, so dwctaxon can do it for us with
[`dct_fill_col()`](https://docs.ropensci.org/dwctaxon/reference/dct_fill_col.md).
The easiest way to see how this works is with an example (you may need
to scroll to the right to see the new column):

``` r

# Fill-in the acceptedNameUsage column with scientific names
filmies_small |>
  dct_fill_col(
    fill_to = "acceptedNameUsage",
    fill_from = "scientificName",
    match_to = "taxonID",
    match_from = "acceptedNameUsageID"
  )
#> # A tibble: 5 × 7
#>   taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName                            acceptedNameUsage                modified
#>   <chr>    <chr>               <chr>           <chr>     <chr>                                     <chr>                            <chr>   
#> 1 54115096 NA                  accepted        species   Cephalomanes atrovirens Presl             NA                               2026-08…
#> 2 54133783 54115097            synonym         species   Trichomanes crassum Copel.                Cephalomanes crassum (Copel.) M… 2026-08…
#> 3 54115097 NA                  accepted        species   Cephalomanes crassum (Copel.) M. G. Price NA                               2026-08…
#> 4 54133784 54115098            synonym         species   Trichomanes densinervium Copel.           Cephalomanes densinervium (Cope… 2026-08…
#> 5 54115098 NA                  accepted        species   Cephalomanes densinervium (Copel.) Copel. NA                               2026-08…
```

The meaning of the arguments `fill_to` and `fill_from` I think are
fairly clear: we are filling the `acceptedNameUsage` column with values
from `scientificName`.

`match_to` and `match_from` are a bit trickier; they describe *how* to
find the data for filling. Here, we are looking up `acceptedNameUsage`
by matching `acceptedNameUsageID` (`match_from`) to `taxonID`
(`match_to`).

Like I said, it’s easiest to figure out
[`dct_fill_col()`](https://docs.ropensci.org/dwctaxon/reference/dct_fill_col.md)
by trying it yourself.

[^1]: See `dct_terms` for a list

[^2]: `taxonID` is filled with the [md5
    hash](https://en.wikipedia.org/wiki/Hash_function) of the scientific
    name. By default, the hash is 32 characters long, so automatically
    generated values of `taxonID` should be unique if the scientific
    names are unique. This can be checked by running
    [`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md).

[^3]: This only works if the scientific name is unique within the
    dataset
