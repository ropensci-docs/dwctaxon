# Check mapping of usage taxonomic IDs

Check that values of terms like 'acceptedUsageID' map properly to
taxonID in Darwin Core (DwC) taxonomic data.

## Usage

``` r
dct_check_mapping(
  tax_dat,
  on_fail = dct_options()$on_fail,
  on_success = dct_options()$on_success,
  col_select = "acceptedNameUsageID",
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

- col_select:

  Character vector of length 1; the name of the column (DwC term) to
  check. Default `"acceptedNameUsageID"`.

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

- Value of taxonID may not be identical to that of the selected column
  within a single row (in other words, a name cannot be its own accepted
  name, parent taxon, or basionym).

- Every value in the selected column must have a corresponding taxonID.

`col_select` can take one of the following values:

- `"acceptedNameUsageID"`: taxonID corresponding to the accepted name
  (of a synonym).

- `"parentNameUsageID"`: taxonID corresponding to the immediate parent
  taxon of a name (for example, for a species, this would be the genus).

- `"originalNameUsageID"`: taxonID corresponding to the basionym of a
  name.

## Examples

``` r
# The bad data has an acceptedNameUsageID (third row, "4") that lacks a
# corresponding taxonID
bad_dat <- tibble::tribble(
  ~taxonID, ~acceptedNameUsageID, ~taxonomicStatus, ~scientificName,
  "1", NA, "accepted", "Species foo",
  "2", "1", "synonym", "Species bar",
  "3", "4", "synonym", "Species bat"
)

dct_check_mapping(bad_dat, on_fail = "summary", quiet = TRUE)
#> # A tibble: 1 × 5
#>   taxonID acceptedNameUsageID scientificName error                         check
#>   <chr>   <chr>               <chr>          <glue>                        <chr>
#> 1 3       4                   Species bat    taxonID detected whose accep… chec…
```
