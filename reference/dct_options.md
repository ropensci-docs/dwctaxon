# Get and set function arguments via options

Changes the default values of function arguments.

## Usage

``` r
dct_options(reset = FALSE, ...)
```

## Arguments

- reset:

  Logical vector of length 1; if TRUE, reset all options to their
  default values.

- ...:

  Any number of `argument = value` pairs, where the left side is the
  name of the argument and the right side is its value. See Details and
  Examples.

## Value

Nothing; used for its side-effect.

## Details

Use this to change the default values of function arguments. That way,
you don't have to type the same thing each time you call a function.

The arguments that can be set with this function are as follows:

### Validation arguments

- `check_col_names`: Logical vector of length 1; should all column names
  be required to be a valid DwC term? Default `TRUE`.

- `check_mapping_accepted_status`: Logical vector of length 1; should
  rules about mapping of variants and synonyms be enforced? Default
  `FALSE`. (See
  [`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md)).

- `check_mapping_accepted`: Logical vector of length 1; should all
  values of `acceptedNameUsageID` be required to map to the `taxonID` of
  an existing name? Default `TRUE`.

- `check_mapping_original`: Logical vector of length 1; should all
  values of `originalNameUsageID` be required to map to the `taxonID` of
  an existing name? Default `TRUE`.

- `check_mapping_parent`: Logical vector of length 1; should all values
  of `parentNameUsageID` be required to map to the `taxonID` of an
  existing name? Default `TRUE`.

- `check_mapping_parent_accepted`: Logical vector of length 1; should
  all values of `parentNameUsageID` be required to map to the `taxonID`
  of an accepted name? Default `FALSE`.

- `check_sci_name`: Logical vector of length 1; should all instances of
  `scientificName` be required to be non-missing and unique? Default
  `TRUE`.

- `check_status_diff`: Logical vector of length 1; should each
  scientific name be allowed to have only one taxonomic status? Default
  `FALSE`.

- `check_tax_status`: Logical vector of length 1; should all taxonomic
  names be required to have a valid value for taxonomic status (by
  default, "accepted", "synonym", or "variant")? Default `TRUE`.

- `check_taxon_id`: Logical vector of length 1; should all instances of
  `taxonID` be required to be non-missing and unique? Default `TRUE`.

- `extra_cols`: Character vector; names of columns that should be
  allowed beyond those defined by the DwC taxon standard. Default NULL.
  Providing column name(s) that are valid DwC taxon column(s) has no
  effect.

- `on_fail`: Character vector of length 1, either "error" or "summary".
  Describes what to do if the check fails. Default `"error"`.

- `on_success`: Character vector of length 1, either "logical" or
  "data". Describes what to do if the check passes. Default `"data"`.

- `skip_missing_cols`: Logical vector of length 1; should checks be
  silently skipped if any of the columns they inspect are missing?
  Default `FALSE`.

- `valid_tax_status`: Character vector of length 1; valid values for
  `taxonomicStatus`. Each value must be separated by a comma. Default
  `accepted, synonym, variant, NA`. `"NA"` indicates that missing (NA)
  values are valid. Case-sensitive.

### Editing arguments

- `clear_usage_id`: Logical vector of length 1; should
  acceptedNameUsageID of the selected row be set to `NA` if the word
  "accepted" is detected in tax_status (not case-sensitive)? Default
  `TRUE`.

- `clear_usage_name`: Logical vector of length 1; should
  acceptedNameUsage of the selected row be set to `NA` if the word
  "accepted" is detected in tax_status (not case-sensitive)? Default
  `TRUE`.

- `fill_taxon_id`: Logical vector of length 1; if `taxon_id` is not
  provided, should values in the taxonID column be filled in by
  generating them automatically from the scientificName? If the
  `taxonID` column does not yet exist it will be created. Default
  `TRUE`.

- `fill_usage_id`: Logical vector of length 1; if `usage_id` is not
  provided, should values in the acceptedNameUsageID column be filled in
  by matching acceptedNameUsage to scientificName? If the
  `acceptedNameUsageID` column does not yet exist it will be created.
  Default `TRUE`.

- `fill_usage_name`: Logical vector of length 1; should the
  acceptedNameUsage of the selected row be set to the scientificName
  corresponding to its acceptedNameUsageID? Default `TRUE`.

- `remap_names`: Logical vector of length 1; should the
  acceptedNameUsageID be updated (remapped) for rows with the same
  acceptedNameUsageID as the taxonID of the row to be modified? Default
  `TRUE`.

- `remap_parent`: Logical vector of length 1; should the
  parentNameUsageID be updated (remapped) to that of its accepted name
  if it is a synonym? Will also apply to any other rows with the same
  parentNameUsageID as the taxonID of the row to be modified. Default
  `TRUE`.

- `remap_variant`: Same as `remap_names`, but applies specifically to
  rows with taxonomicStatus of "variant". Default `FALSE`.

- `stamp_modified`: Logical vector of length 1; should the `modified`
  column of any newly created or modified row include a timestamp with
  the date and time of its creation/modification? If the `modified`
  column does not yet exist it will be created. Default `TRUE`.

- `stamp_modified_by`: Logical vector of length 1; should the
  `modifiedBy` column of any newly created or modified row include the
  name of the current user? If the `modifiedBy` column does not yet
  exist it will be created; note that this is a non-DWC standard column,
  so `"modifiedBy"` is required in `extra_cols`. The current user can be
  specified with the `user_name` option. Default `FALSE`.

- `stamp_modified_by_id`: Logical vector of length 1; should the
  `modifiedByID` column of any newly created or modified row include the
  ID of the current user? If the `modifiedByID` column does not yet
  exist it will be created; note that this is a non-DWC standard column,
  so `"modifiedByID"` is required in `extra_cols`. The current user ID
  can be specified with the `user_id` option. Default `FALSE`.

- `taxon_id_length`: Numeric vector of length 1; how many characters
  should be included in automatically generated values of taxonID? Must
  be between 1 and 32, inclusive. Default `32`.

### General arguments

- `quiet`: Logical vector of length 1; should warnings be silenced?
  Default `FALSE`.

- `strict`: Logical vector of length 1; should taxonomic checks be run
  on the updated taxonomic database? Default `FALSE`.

- `user_name`: Character vector of length 1; the name of the current
  user. Default `""`.

- `user_id`: Character vector of length 1; the ID of the current user.
  Default `""`.

## Examples

``` r
# Show all options
dct_options()
#> $check_taxon_id
#> [1] TRUE
#> 
#> $check_tax_status
#> [1] TRUE
#> 
#> $check_mapping_accepted
#> [1] TRUE
#> 
#> $check_mapping_parent
#> [1] TRUE
#> 
#> $check_mapping_parent_accepted
#> [1] FALSE
#> 
#> $check_mapping_original
#> [1] TRUE
#> 
#> $check_mapping_accepted_status
#> [1] FALSE
#> 
#> $check_sci_name
#> [1] TRUE
#> 
#> $check_status_diff
#> [1] FALSE
#> 
#> $check_col_names
#> [1] TRUE
#> 
#> $valid_tax_status
#> [1] "accepted, synonym, variant, NA"
#> 
#> $extra_cols
#> NULL
#> 
#> $skip_missing_cols
#> [1] FALSE
#> 
#> $on_success
#> [1] "data"
#> 
#> $on_fail
#> [1] "error"
#> 
#> $fill_taxon_id
#> [1] TRUE
#> 
#> $fill_usage_id
#> [1] TRUE
#> 
#> $taxon_id_length
#> [1] 32
#> 
#> $clear_usage_id
#> [1] TRUE
#> 
#> $clear_usage_name
#> [1] TRUE
#> 
#> $fill_usage_name
#> [1] TRUE
#> 
#> $remap_names
#> [1] TRUE
#> 
#> $remap_parent
#> [1] TRUE
#> 
#> $remap_variant
#> [1] FALSE
#> 
#> $stamp_modified
#> [1] TRUE
#> 
#> $stamp_modified_by
#> [1] FALSE
#> 
#> $stamp_modified_by_id
#> [1] FALSE
#> 
#> $strict
#> [1] FALSE
#> 
#> $quiet
#> [1] FALSE
#> 
#> $user_name
#> [1] ""
#> 
#> $user_id
#> [1] ""
#> 

# Store existing settings, including any changes made by the user
old_settings <- dct_options()

# View one option
dct_options()$valid_tax_status
#> [1] "accepted, synonym, variant, NA"

# Change one option
dct_options(valid_tax_status = "accepted, weird, whatever")
dct_options()$valid_tax_status
#> [1] "accepted, weird, whatever"

# Reset to default values
dct_options(reset = TRUE)
dct_options()$valid_tax_status
#> [1] "accepted, synonym, variant, NA"

# Multiple options may also be set at once
dct_options(check_taxon_id = FALSE, check_status_diff = TRUE)

# Reset options to those before this example was run
do.call(dct_options, old_settings)
```
