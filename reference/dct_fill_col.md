# Fill a column of a taxonomic database

Fill a column in a taxonomic database in Darwin Core (DwC) format.

## Usage

``` r
dct_fill_col(
  tax_dat,
  fill_to = "acceptedNameUsage",
  fill_from = "scientificName",
  match_to = "taxonID",
  match_from = "acceptedNameUsageID",
  stamp_modified = dct_options()$stamp_modified
)
```

## Arguments

- tax_dat:

  Dataframe; taxonomic database in DwC format.

- fill_to:

  Character vector of length 1; name of column to fill. If the column
  does not yet exist it will be created.

- fill_from:

  Character vector of length 1; name of column to copy values from when
  filling.

- match_to:

  Character vector of length 1; name of column to match to.

- match_from:

  Character vector of length 1; name of column to match from.

- stamp_modified:

  Logical vector of length 1; should the `modified` column of any newly
  created or modified row include a timestamp with the date and time of
  its creation/modification? If the `modified` column does not yet exist
  it will be created. Default `TRUE`.

## Value

Dataframe; taxonomic database in DwC format.

## Details

Several terms (columns) in DwC format come in pairs of "term" and
"termID"; for example, "acceptedNameUsage" and "acceptedNameUsageID",
where the first is the value in a human-readable form (in this case,
scientific name of the accepted taxon) and the second is the value used
by a machine (in this case, taxonID of the accepted taxon). Other pairs
include "parentNameUsage" and "parentNameUsageID", "scientificName" and
"scientificNameID", etc. None are required to be used in a given DwC
dataset.

Often when updating data, the user may only fill in one value or the
other (e.g., "acceptedNameUsage" or "acceptedNameUsageID"), but not
both. The purpose of `dct_fill_col()` is to fill the missing column.

`match_from` and `match_to` are used to locate the values used for
filling each cell. The values in the `match_to` column must be unique.

The default settings are to fill acceptedNameUsage with values from
scientificName by matching acceptedNameUsageID to taxonID (see Example).

When adding timestamps with `stamp_modified`, any row that differs from
the original data (`tax_dat`) is considered modified. This includes when
a new column is added, in which case all rows will be considered
modified.

## Examples

``` r
# Fill acceptedNameUsage with values from scientificName by
# matching acceptedNameUsageID to taxonID
(head(dct_filmies, 5)) |>
  dct_fill_col(
    fill_to = "acceptedNameUsage",
    fill_from = "scientificName",
    match_to = "taxonID",
    match_from = "acceptedNameUsageID"
  )
#> # A tibble: 5 × 7
#>   taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName         
#>   <chr>    <chr>               <chr>           <chr>     <chr>                  
#> 1 54115096 NA                  accepted        species   Cephalomanes atroviren…
#> 2 54133783 54115097            synonym         species   Trichomanes crassum Co…
#> 3 54115097 NA                  accepted        species   Cephalomanes crassum (…
#> 4 54133784 54115098            synonym         species   Trichomanes densinervi…
#> 5 54115098 NA                  accepted        species   Cephalomanes densinerv…
#> # ℹ 2 more variables: acceptedNameUsage <chr>, modified <chr>
```
