# Check taxonID

Check for correctly formatted taxonID column in Darwin Core taxonomic
data.

## Usage

``` r
dct_check_taxon_id(
  tax_dat,
  on_fail = dct_options()$on_fail,
  on_success = dct_options()$on_success,
  quiet = dct_options()$quiet
)
```

## Arguments

- tax_dat:

  Dataframe; taxonomic database in DwC format.

- on_fail:

  Character vector of length 1, either "error" or "summary". Describes
  what to do if the check fails. Default `"error"`.

- on_success:

  Character vector of length 1, either "logical" or "data". Describes
  what to do if the check passes. Default `"data"`.

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

The following rules are enforced:

- taxonID may not be missing (NA)

- taxonID must be unique

## Examples

``` r
dct_check_taxon_id(
  data.frame(taxonID = NA_character_),
  on_fail = "summary", quiet = TRUE
)
#> # A tibble: 1 × 3
#>   taxonID error                               check         
#>   <chr>   <chr>                               <chr>         
#> 1 NA      taxonID detected with missing value check_taxon_id
dct_check_taxon_id(data.frame(taxonID = 1))
#>   taxonID
#> 1       1
```
