# Check scientificName

Check for correctly formatted scientificName column in Darwin Core
taxonomic data.

## Usage

``` r
dct_check_sci_name(
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

- scientificName may not be missing (NA)

- scientificName must be unique

## Examples

``` r
dct_check_sci_name(
  data.frame(scientificName = NA_character_),
  on_fail = "summary", quiet = TRUE
)
#> # A tibble: 1 × 3
#>   scientificName check          error                                     
#>   <chr>          <chr>          <chr>                                     
#> 1 NA             check_sci_name scientificName detected with missing value

dct_check_sci_name(data.frame(scientificName = "a"))
#>   scientificName
#> 1              a
```
