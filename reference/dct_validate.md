# Validate a taxonomic database

Runs a series of automated checks on a taxonomic database in Darwin Core
(DwC) format.

## Usage

``` r
dct_validate(
  tax_dat,
  check_taxon_id = dct_options()$check_taxon_id,
  check_tax_status = dct_options()$check_tax_status,
  check_mapping_accepted = dct_options()$check_mapping_accepted,
  check_mapping_parent = dct_options()$check_mapping_parent,
  check_mapping_parent_accepted = dct_options()$check_mapping_parent_accepted,
  check_mapping_original = dct_options()$check_mapping_original,
  check_mapping_accepted_status = dct_options()$check_mapping_accepted_status,
  check_sci_name = dct_options()$check_sci_name,
  check_status_diff = dct_options()$check_status_diff,
  check_col_names = dct_options()$check_col_names,
  valid_tax_status = dct_options()$valid_tax_status,
  extra_cols = dct_options()$extra_cols,
  on_success = dct_options()$on_success,
  on_fail = dct_options()$on_fail,
  skip_missing_cols = dct_options()$skip_missing_cols,
  quiet = dct_options()$quiet
)
```

## Arguments

- tax_dat:

  Dataframe; taxonomic database in DwC format.

- check_taxon_id:

  Logical vector of length 1; should all instances of `taxonID` be
  required to be non-missing and unique? Default `TRUE`.

- check_tax_status:

  Logical vector of length 1; should all taxonomic names be required to
  have a valid value for taxonomic status (by default, "accepted",
  "synonym", or "variant")? Default `TRUE`.

- check_mapping_accepted:

  Logical vector of length 1; should all values of `acceptedNameUsageID`
  be required to map to the `taxonID` of an existing name? Default
  `TRUE`.

- check_mapping_parent:

  Logical vector of length 1; should all values of `parentNameUsageID`
  be required to map to the `taxonID` of an existing name? Default
  `TRUE`.

- check_mapping_parent_accepted:

  Logical vector of length 1; should all values of `parentNameUsageID`
  be required to map to the `taxonID` of an accepted name? Default
  `FALSE`.

- check_mapping_original:

  Logical vector of length 1; should all values of `originalNameUsageID`
  be required to map to the `taxonID` of an existing name? Default
  `TRUE`.

- check_mapping_accepted_status:

  Logical vector of length 1; should rules about mapping of variants and
  synonyms be enforced? Default `FALSE`. (see Details).

- check_sci_name:

  Logical vector of length 1; should all instances of `scientificName`
  be required to be non-missing and unique? Default `TRUE`.

- check_status_diff:

  Logical vector of length 1; should each scientific name be allowed to
  have only one taxonomic status? Default `FALSE`.

- check_col_names:

  Logical vector of length 1; should all column names be required to be
  a valid DwC term? Default `TRUE`.

- valid_tax_status:

  Character vector of length 1; valid values for `taxonomicStatus`. Each
  value must be separated by a comma. Default
  `accepted, synonym, variant, NA`. `"NA"` indicates that missing (NA)
  values are valid. Case-sensitive.

- extra_cols:

  Character vector; names of columns that should be allowed beyond those
  defined by the DwC taxon standard. Default NULL. Providing column
  name(s) that are valid DwC taxon column(s) has no effect.

- on_success:

  Character vector of length 1, either "logical" or "data". Describes
  what to do if the check passes. Default `"data"`.

- on_fail:

  Character vector of length 1, either "error" or "summary". Describes
  what to do if the check fails. Default `"error"`.

- skip_missing_cols:

  Logical vector of length 1; should checks be silently skipped if any
  of the columns they inspect are missing? Default `FALSE`.

- quiet:

  Logical vector of length 1; should warnings be silenced? Default
  `FALSE`.

## Value

Depends on the result of the check and on values of `on_fail` and
`on_success`:

- If the check passes and `on_success` is "logical", return `TRUE`

- If the check passes and `on_success` is "data", return the input
  dataframe

- If the check fails and `on_fail` is "error", return an error

- If the check fails and `on_fail` is "summary", issue a warning and
  return a dataframe with a summary of the reasons for failure

## Details

For `check_mapping_accepted_status` and `check_status_diff`, "accepted",
"synonym", and "variant" are determined by string matching of
`taxonomicStatus`; so "provisionally accepted" is counted as "accepted",
"ambiguous synonym" is counted as "synonym", etc. (case-sensitive).

For `check_mapping_accepted_status`, the following rules are enforced:

- Rows with `taxonomicStatus` of "synonym" (synonyms) must have an
  `acceptedNameUsageID` matching the `taxonID` of an accepted name
  (`taxonomicStatus` of "accepted")

- Rows with `taxonomicStatus` of "variant" (orthographic variants) must
  have an `acceptedNameUsageID` matching the `taxonID` of an accepted
  name or synonym (but not another variant)

- Rows with `taxonomicStatus` of "accepted" must not have any value
  entered for `acceptedNameUsageID`

- Rows with a value for `acceptedNameUsageID` must have a valid value
  for `taxonomicStatus`.

Default settings of all arguments can be modified with
[`dct_options()`](https://docs.ropensci.org/dwctaxon/reference/dct_options.md)
(see Examples).

Most columns are expected to be vectors of class character, but this is
not checked for all columns. Columns (DwC terms) with names including
'ID', for example 'taxonID', may be character, numeric, or integer.

## Examples

``` r
# The example dataset dct_filmies is already correctly formatted and passes
# validation
dct_validate(dct_filmies)
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

# So make some bad data on purpose with a duplicated scientific name
bad_dat <- dct_filmies
bad_dat$scientificName[1] <- bad_dat$scientificName[2]

# The incorrectly formatted data won't pass
try(
  dct_validate(bad_dat)
)
#> Error : check_sci_name failed
#>    scientificName detected with duplicated value
#>    Bad scientificName: Trichomanes crassum Copel., Trichomanes crassum Copel.
#> 

# It will pass if we allow duplicated scientific names though
dct_validate(bad_dat, check_sci_name = FALSE)
#> # A tibble: 2,451 × 5
#>    taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName        
#>    <chr>    <chr>               <chr>           <chr>     <chr>                 
#>  1 54115096 NA                  accepted        species   Trichomanes crassum C…
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

# Individual checks can also be turned or off with dct_options()

# First save the current settings before making any changes
old_settings <- dct_options()

# Let's allow duplicated scientific names by default
dct_options(check_sci_name = FALSE)

# The data passes validation as before, but we don't have to specify
# `check_sci_name = FALSE` in the function call
dct_validate(bad_dat)
#> # A tibble: 2,451 × 5
#>    taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName        
#>    <chr>    <chr>               <chr>           <chr>     <chr>                 
#>  1 54115096 NA                  accepted        species   Trichomanes crassum C…
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

# Reset options to those before this example was run
do.call(dct_options, old_settings)
```
