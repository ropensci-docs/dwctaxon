# Validating DwC taxon data

dwctaxon has two major purposes, (1) editing and (2) validation of
taxonomic data in [Darwin Core (DwC)](https://dwc.tdwg.org/terms/#taxon)
format. This vignette is about the latter.

## Setup

Start by loading packages and setting the random number generator seed
since this vignette involves some random samples.

``` r

library(dwctaxon)
library(dplyr)

set.seed(12345)
```

## The data

As
[before](https://docs.ropensci.org/dwctaxon/articles/editing.html#the-data),
we will use the example dataset that comes with dwctaxon, `dct_filmies`:

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

However, `dct_filmies` already is well-formatted and would pass all
validation checks! So lets introduce some noise to make things more
interesting.

``` r

filmies_dirty <-
  dct_filmies |>
  # Change taxonomic status of one row to 'good'
  dct_modify_row(taxonID = "54115096", taxonomicStatus = "good") |>
  # Duplicate some rows at the end
  bind_rows(tail(dct_filmies)) |>
  # Insert bad values for `acceptedNameUsageID` of 5 random rows
  rows_update(
    tibble(
      taxonID = sample(dct_filmies$taxonID, 5),
      acceptedNameUsageID = sample(letters, 5)
    ),
    by = "taxonID"
  )

filmies_dirty
#> # A tibble: 2,457 × 6
#>    taxonID  acceptedNameUsageID taxonomicStatus taxonRank scientificName                             modified                  
#>    <chr>    <chr>               <chr>           <chr>     <chr>                                      <chr>                     
#>  1 54115096 NA                  good            species   Cephalomanes atrovirens Presl              2026-08-30 07:23:50.204345
#>  2 54133783 54115097            synonym         species   Trichomanes crassum Copel.                 NA                        
#>  3 54115097 NA                  accepted        species   Cephalomanes crassum (Copel.) M. G. Price  NA                        
#>  4 54133784 54115098            synonym         species   Trichomanes densinervium Copel.            NA                        
#>  5 54115098 NA                  accepted        species   Cephalomanes densinervium (Copel.) Copel.  NA                        
#>  6 54133786 54115100            synonym         species   Cephalomanes curvatum (J. Sm.) V. D. Bosch NA                        
#>  7 54133787 54115100            synonym         species   Cephalomanes javanica (Bl.) V. D. Bosch    NA                        
#>  8 54133788 54115100            synonym         species   Cephalomanes oblongifolium Presl           NA                        
#>  9 54133789 54115100            synonym         species   Cephalomanes zollingeri V. D. Bosch        NA                        
#> 10 54133790 54115100            synonym         species   Lacostea javanica (Bl.) Prantl             NA                        
#> # ℹ 2,447 more rows
```

The first few rows may look the same, but we know that these data now
have some problems.

## Error on failure

[`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md)
is the workhorse function for validating DwC data.

In default mode,
[`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md)
will issue an error the first time it finds something wrong with the
data (in other words, on the first check that fails):

``` r

dct_validate(filmies_dirty)
#> Error:
#> ! check_taxon_id failed
#>    taxonID detected with duplicated value
#>    Bad taxonID: 54126747, 54135528, 54135530, 54135531, 54126748, 54126749
```

dwctaxon tries to provide useful error messages that help you determine
what in the data is causing the problem. Here, we see that rows with
`taxonID` 54126747, 54135528, 54135530, 54135531, 54126748, and 54126749
are duplicated. Here of course we know that’s because we duplicated them
on purpose; in a real dataset, you could use this information to search
out the duplicated values and fix them.

## Summary on failure

If you are troubleshooting a DwC taxon dataset, it may be more useful to
know about all of the problems at once instead of fixing them one at a
time. In that case, set the `on_fail` argument to `"summary"` (`on_fail`
can be either its default value `"error"` or `"summary"`):

``` r

dct_validate(filmies_dirty, on_fail = "summary")
#> Warning: taxonID detected with duplicated value
#> Warning: taxonID detected whose taxonomicStatus is not in valid_tax_status (accepted, synonym, variant, NA)
#> Warning: taxonID detected whose acceptedNameUsageID value does not map to taxonID of an existing name.
#> Warning: scientificName detected with duplicated value
#> # A tibble: 18 × 6
#>    taxonID  acceptedNameUsageID scientificName                                              taxonomicStatus error                                                                                              check        
#>    <chr>    <chr>               <chr>                                                       <chr>           <glue>                                                                                             <chr>        
#>  1 54133841 k                   Trichomanes cumingii (Presl) C. Chr.                        NA              taxonID detected whose acceptedNameUsageID value does not map to taxonID of an existing name.      check_mapping
#>  2 54134450 z                   Trichomanes omphalodes (Vieill.) C. Chr.                    NA              taxonID detected whose acceptedNameUsageID value does not map to taxonID of an existing name.      check_mapping
#>  3 54134462 b                   Trichomanes amabile Nakai                                   NA              taxonID detected whose acceptedNameUsageID value does not map to taxonID of an existing name.      check_mapping
#>  4 54134950 v                   Mecodium atrovirens (Col.) Copel.                           NA              taxonID detected whose acceptedNameUsageID value does not map to taxonID of an existing name.      check_mapping
#>  5 54135730 x                   Leptocionium attenuatum (Hook.) Bosch                       NA              taxonID detected whose acceptedNameUsageID value does not map to taxonID of an existing name.      check_mapping
#>  6 54126747 NA                  Hymenophyllum myriocarpum var. endiviifolium (Desv.) Stolze NA              scientificName detected with duplicated value                                                      check_sci_na…
#>  7 54135528 NA                  Hymenophyllum nigrescens Liebm.                             NA              scientificName detected with duplicated value                                                      check_sci_na…
#>  8 54135530 NA                  Mecodium nigricans (Presl ex Kl.) Copel.                    NA              scientificName detected with duplicated value                                                      check_sci_na…
#>  9 54135531 NA                  Sphaerocionium nigricans Presl ex Kl.                       NA              scientificName detected with duplicated value                                                      check_sci_na…
#> 10 54126748 NA                  Hymenophyllum myriocarpum var. nigrescens (Liebm.) Stolze   NA              scientificName detected with duplicated value                                                      check_sci_na…
#> 11 54126749 NA                  Hymenophyllum trichophyllum var. buesii C. V. Morton        NA              scientificName detected with duplicated value                                                      check_sci_na…
#> 12 54115096 NA                  Cephalomanes atrovirens Presl                               good            taxonID detected whose taxonomicStatus is not in valid_tax_status (accepted, synonym, variant, NA) check_tax_st…
#> 13 54126747 NA                  NA                                                          NA              taxonID detected with duplicated value                                                             check_taxon_…
#> 14 54135528 NA                  NA                                                          NA              taxonID detected with duplicated value                                                             check_taxon_…
#> 15 54135530 NA                  NA                                                          NA              taxonID detected with duplicated value                                                             check_taxon_…
#> 16 54135531 NA                  NA                                                          NA              taxonID detected with duplicated value                                                             check_taxon_…
#> 17 54126748 NA                  NA                                                          NA              taxonID detected with duplicated value                                                             check_taxon_…
#> 18 54126749 NA                  NA                                                          NA              taxonID detected with duplicated value                                                             check_taxon_…
```

(You may need to scroll to the right in the output below to see all the
text).

In this case,
[`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md)
still issues a warning to let us know validation did not pass. The
`error` and `check` columns describe what went wrong; the other columns
tell us where in the data to find the errors.

With this detailed summary, we should definitely be able to hunt down
the bugs in this dataset!

## Checks

You may be wondering, why the separate “error” and “check” columns in
the summary output?

That is because
[`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md)
conducts many smaller checks, each of which can be turned on or off. For
a complete description, run `?dct_validate()`. In turn, the checks can
each identify different particular problems; the most granular
description is given in the “error” column.

Furthermore, each of the checks run by
[`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md)
can also be run as an individual function. For example, let’s just check
that all values of `acceptedUsageID` have a corresponding `taxonID` (in
other words, that all synonyms map properly):

``` r

filmies_dirty |>
  dct_check_mapping()
#> Error:
#> ! check_mapping failed.
#> taxonID detected whose acceptedNameUsageID value does not map to taxonID of an existing name.
#> Bad taxonID: 54133841, 54134450, 54134462, 54134950, 54135730
#> Bad scientificName: Trichomanes cumingii (Presl) C. Chr., Trichomanes omphalodes (Vieill.) C. Chr., Trichomanes amabile Nakai, Mecodium atrovirens (Col.) Copel., Leptocionium attenuatum (Hook.) Bosch
#> Bad acceptedNameUsageID: k, z, b, v, x
```

It is important to note that not all checks are compatible with each
other. For example, `check_sci_name` checks that all scientific names
(DwC term `scientificName`) are non-missing and unique;
`check_status_diff` checks that in cases of *identical* scientific
names, the taxonomic status of each name is different. The default
settings for
[`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md)
are to use the former but not the latter. Whether you expect all
scientific names to be unique or not depends on how you set up your
data[^1].

## Controlled vocabularies

Some DwC taxon terms are expected only to take a small number values
from a controlled vocabulary. For example, `taxonStatus` (taxonomic
status of a scientific name) may only be expected to include the values
“accepted”, “synonym”, etc. This is unlike, e.g., `scientificName`,
where we would not try to control the range of possible values.

However, although DwC recommends using a controlled vocabulary for such
terms, it does not specify the actual values! So dwctaxon lets you set
those yourself (and tries to employ reasonable defaults), as shown in
the [next section](#changing-the-defaults).

## Changing the defaults

Say you want to use a different set of allowed values for `taxonStatus`.
Here, let’s include “good” so that the data will pass the check for
taxonomic status (remember [we modified the data](#the-data) so the
`taxonomicStatus` of one of the rows was `"good"`).

One way would be to use the `valid_tax_status` argument of
[`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md)
or
[`dct_check_tax_status()`](https://docs.ropensci.org/dwctaxon/reference/dct_check_tax_status.md):

``` r

filmies_dirty |>
  dct_check_tax_status(
    valid_tax_status = "good, accepted, synonym",
    on_success = "logical" # Issue "TRUE" if the check passes
  )
#> [1] TRUE
```

But specifying this argument every time you want to check something gets
tedious.

So we can change the default setting for `valid_tax_status` with
[`dct_options()`](https://docs.ropensci.org/dwctaxon/reference/dct_options.md)
like so:

``` r

# First save the current settings before making any changes
old_settings <- dct_options()

# Change valid_tax_status setting
dct_options(valid_tax_status = "good, accepted, synonym")
```

Now we can run
[`dct_check_tax_status()`](https://docs.ropensci.org/dwctaxon/reference/dct_check_tax_status.md)
and it will use the new default value:

``` r

filmies_dirty |>
  dct_check_tax_status(on_success = "logical")
#> [1] TRUE
```

You can change back to the original default values with `reset = TRUE`:

``` r

dct_options(reset = TRUE)
```

Now running the same code as above throws an error:

``` r

filmies_dirty |>
  dct_check_tax_status(on_success = "logical")
#> Error:
#> ! check_tax_status failed.
#> taxonID detected whose taxonomicStatus is not in valid_tax_status (accepted, synonym, variant, NA)
#> Bad taxonID: 54115096
#> Bad scientificName: Cephalomanes atrovirens Presl
#> Bad taxonomicStatus: good
```

There are a large number of settings that can be modified. See
`?dct_options()` for a description of each.

You can view the current status of all options (default values) by
running
[`dct_options()`](https://docs.ropensci.org/dwctaxon/reference/dct_options.md)
with no arguments:

``` r

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
```

Or check the value of one particular setting by passing its name with
the `$` operator:

``` r

dct_options()$valid_tax_status
#> [1] "accepted, synonym, variant, NA"
```

We can restore the settings as they were before any of these changes
were applied by running
[`do.call()`](https://rdrr.io/r/base/do.call.html) on the settings we
saved above:

``` r

do.call(dct_options, old_settings)
```

[^1]: According to the rules of taxonomic nomenclature, of course each
    full scientific name *should* be unique, but there [have been errors
    in the
    past](https://www.iapt-taxon.org/nomen/pages/main/art_31.html?zoom_highlight=identical)
    where the same author published the same name more than once!
