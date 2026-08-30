# Check that taxonomicStatus is within valid values in Darwin Core taxonomic data

Check that taxonomicStatus is within valid values in Darwin Core
taxonomic data

## Usage

``` r
dct_check_tax_status(
  tax_dat,
  on_fail = dct_options()$on_fail,
  on_success = dct_options()$on_success,
  valid_tax_status = dct_options()$valid_tax_status,
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

- valid_tax_status:

  Character vector of length 1; valid values for `taxonomicStatus`. Each
  value must be separated by a comma. Default
  `accepted, synonym, variant, NA`. `"NA"` indicates that missing (NA)
  values are valid. Case-sensitive. (see Examples).

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

## References

<https://dwc.tdwg.org/terms/#dwc:taxonomicStatus>

## Examples

``` r
# The bad data has an taxonomicStatus (third row, "foo") that is not
# a valid value
bad_dat <- tibble::tribble(
  ~taxonID, ~acceptedNameUsageID, ~taxonomicStatus, ~scientificName,
  "1", NA, "accepted", "Species foo",
  "2", "1", "synonym", "Species bar",
  "3", NA, "foo", "Species bat"
)

dct_check_tax_status(bad_dat, on_fail = "summary", quiet = TRUE)
#> # A tibble: 1 × 5
#>   taxonID scientificName taxonomicStatus error                             check
#>   <chr>   <chr>          <chr>           <chr>                             <chr>
#> 1 3       Species bat    foo             taxonID detected whose taxonomic… chec…

# Example of setting valid values of taxonomicStatus via dct_options()

# First store existing settings, including any changes made by the user
old_settings <- dct_options()

# Change options for valid_tax_status
dct_options(valid_tax_status = "provisionally accepted, synonym, NA")
tibble::tribble(
  ~taxonID, ~acceptedNameUsageID, ~taxonomicStatus, ~scientificName,
  "1", NA, "provisionally accepted", "Species foo",
  "2", "1", "synonym", "Species bar",
  "3", NA, NA, "Strange name"
) |>
  dct_check_tax_status()
#> # A tibble: 3 × 4
#>   taxonID acceptedNameUsageID taxonomicStatus        scientificName
#>   <chr>   <chr>               <chr>                  <chr>         
#> 1 1       NA                  provisionally accepted Species foo   
#> 2 2       1                   synonym                Species bar   
#> 3 3       NA                  NA                     Strange name  

# Reset options to those before this example was run
do.call(dct_options, old_settings)
```
