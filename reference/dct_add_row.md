# Add row(s) to a taxonomic database

Add one or more rows to a taxonomic database in Darwin Core (DwC)
format.

## Usage

``` r
dct_add_row(
  tax_dat,
  taxonID = NULL,
  scientificName = NULL,
  taxonomicStatus = NULL,
  acceptedNameUsageID = NULL,
  acceptedNameUsage = NULL,
  new_dat = NULL,
  fill_taxon_id = dct_options()$fill_taxon_id,
  fill_usage_id = dct_options()$fill_usage_id,
  taxon_id_length = dct_options()$taxon_id_length,
  stamp_modified = dct_options()$stamp_modified,
  stamp_modified_by_id = dct_options()$stamp_modified_by_id,
  stamp_modified_by = dct_options()$stamp_modified_by,
  strict = dct_options()$strict,
  ...
)
```

## Arguments

- tax_dat:

  Dataframe; taxonomic database in DwC format.

- taxonID:

  Character or numeric vector; values to add to taxonID column. Ignored
  if `new_dat` is not `NULL`.

- scientificName:

  Character vector; values to add to scientificName column. Ignored if
  `new_dat` is not `NULL`.

- taxonomicStatus:

  Character vector; values to add to taxonomicStatus column. Ignored if
  `new_dat` is not `NULL`.

- acceptedNameUsageID:

  Character or numeric vector; values to add to acceptedNameUsageID
  column. Ignored if `new_dat` is not `NULL`.

- acceptedNameUsage:

  Character vector; values to add to acceptedNameUsage column. Ignored
  if `new_dat` is not `NULL`.

- new_dat:

  A dataframe including columns corresponding to one or more of the
  above arguments, except for `tax_dat`. Other DwC terms can also be
  included as additional columns. All rows in `new_dat` will be appended
  to the input data (`tax_dat`).

- fill_taxon_id:

  Logical vector of length 1; if `taxon_id` is not provided, should
  values in the taxonID column be filled in by generating them
  automatically from the scientificName? If the `taxonID` column does
  not yet exist it will be created. Default `TRUE`.

- fill_usage_id:

  Logical vector of length 1; if `usage_id` is not provided, should
  values in the acceptedNameUsageID column be filled in by matching
  acceptedNameUsage to scientificName? If the `acceptedNameUsageID`
  column does not yet exist it will be created. Default `TRUE`.

- taxon_id_length:

  Numeric vector of length 1; how many characters should be included in
  automatically generated values of taxonID? Must be between 1 and 32,
  inclusive. Default `32`.

- stamp_modified:

  Logical vector of length 1; should the `modified` column of any newly
  created or modified row include a timestamp with the date and time of
  its creation/modification? If the `modified` column does not yet exist
  it will be created. Default `TRUE`.

- stamp_modified_by_id:

  Logical vector of length 1; should the `modifiedByID` column of any
  newly created or modified row include the ID of the current user? If
  the `modifiedByID` column does not yet exist it will be created; note
  that this is a non-DWC standard column, so `"modifiedByID"` is
  required in `extra_cols`. The current user ID can be specified with
  the `user_id` option. Default `FALSE`.

- stamp_modified_by:

  Logical vector of length 1; should the `modifiedBy` column of any
  newly created or modified row include the name of the current user? If
  the `modifiedBy` column does not yet exist it will be created; note
  that this is a non-DWC standard column, so `"modifiedBy"` is required
  in `extra_cols`. The current user can be specified with the
  `user_name` option. Default `FALSE`.

- strict:

  Logical vector of length 1; should taxonomic checks be run on the
  updated taxonomic database? Default `FALSE`.

- ...:

  Additional data to add, specified as sets of named character or
  numeric vectors; e.g., `parentNameUsageID = "6SH4"`. The name of each
  must be a valid column name for data in DwC format. Ignored if
  `new_dat` is not `NULL`.

## Value

Dataframe; taxonomic database in DwC format.

## Details

`fill_taxon_id` and `fill_usage_id` only act on the newly added data
(they do not fill columns in `tax_dat`).

If "taxonID" is not provided for the new row and `fill_taxon_id` is
`TRUE`, a value for taxonID will be automatically generated from the md5
hash digest of the scientific name.

To modify settings used for validation if `strict` is `TRUE`, use
[`dct_options()`](https://docs.ropensci.org/dwctaxon/reference/dct_options.md).

## Examples

``` r
tibble::tibble(
  taxonID = "123",
  scientificName = "Foogenus barspecies",
  acceptedNameUsageID = NA_character_,
  taxonomicStatus = "accepted"
) |>
  dct_add_row(
    scientificName = "Foogenus barspecies var. bla",
    parentNameUsageID = "123",
    nameAccordingTo = "me",
    strict = TRUE
  )
#> # A tibble: 2 × 7
#>   taxonID   scientificName acceptedNameUsageID taxonomicStatus parentNameUsageID
#>   <chr>     <chr>          <chr>               <chr>           <chr>            
#> 1 123       Foogenus bars… NA                  accepted        NA               
#> 2 cfedfbbb… Foogenus bars… NA                  NA              123              
#> # ℹ 2 more variables: nameAccordingTo <chr>, modified <chr>
```
