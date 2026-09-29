# Real World Example

This vignette demonstrates using dwctaxon on “real life” data found in
the wild. Our goal is to import the data and validate it.

First, load the packages used for this vignette.

``` r

library(dwctaxon)
library(readr)
library(tibble)
library(dplyr)
```

### Import data

We will use the [Database of Vascular Plants of Canada
(VASCAN)](http://data.canadensys.net/ipt/resource.do?r=vascan), which is
available as a Darwin Core Archive.

The data can be obtained manually by going to the [VASCAN
website](http://data.canadensys.net/ipt/resource.do?r=vascan),
downloading the Darwin Core Archive, and unzipping it[^1].

Alternatively, it can be downloaded and unzipped from the comfort of R.

First, we set up some temporary folders for downloading.

``` r

# - Specify temporary folder for downloading data
temp_dir <- tempdir()
# - Set name of zip file
temp_zip <- paste0(temp_dir, "/dwca-vascan.zip")
# - Set name of unzipped folder
temp_unzip <- paste0(temp_dir, "/dwca-vascan")
```

The URL for the zip file can be obtained from the dwctaxon package by
running this code, which saves it as `vascan_url`:

``` r

source(system.file("extdata", "vascan_url.R", package = "dwctaxon"))

# Check that we now have the URL loaded:
vascan_url
#> [1] "https://data.canadensys.net/ipt/archive.do?r=vascan&v=37.12"
```

We are now ready to download and unzip the zip file.

``` r

# Download data
download.file(url = vascan_url, destfile = temp_zip, mode = "wb")

# Unzip
unzip(temp_zip, exdir = temp_unzip)
```

Let’s check the contents of the unzipped data (the Darwin Core Archive).

``` r

list.files(temp_unzip)
#> [1] "description.txt"          "distribution.txt"         "eml.xml"                  "meta.xml"                 "resourcerelationship.txt"
#> [6] "taxon.txt"                "vernacularname.txt"
```

Finally, load the taxonomic data (`taxon.txt`) into R. It is a
tab-separated text file, so we use
[`readr::read_tsv()`](https://readr.tidyverse.org/reference/read_delim.html)
to load it.

``` r

vascan <- read_tsv(paste0(temp_unzip, "/taxon.txt"))
#> Warning: One or more parsing issues, call `problems()` on your data frame for details, e.g.:
#>   dat <- vroom(...)
#>   problems(dat)
#> Rows: 32770 Columns: 24
#> ── Column specification ────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
#> Delimiter: "\t"
#> chr (20): nameAccordingToID, scientificName, acceptedNameUsage, parentNameUsage, nameAccordingTo, higherClassification, class, order, fa...
#> dbl  (4): id, taxonID, acceptedNameUsageID, parentNameUsageID
#> 
#> ℹ Use `spec()` to retrieve the full column specification for this data.
#> ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

# Take a peak at the data
vascan
#> # A tibble: 32,770 × 24
#>       id taxonID acceptedNameUsageID parentNameUsageID nameAccordingToID    scientificName acceptedNameUsage parentNameUsage nameAccordingTo
#>    <dbl>   <dbl>               <dbl>             <dbl> <chr>                <chr>          <chr>             <chr>           <chr>          
#>  1    73      73                  73                NA http://dx.doi.org/1… Equisetopsida… Equisetopsida C.… NA              Chase, M.W. & …
#>  2    26      26                  26                73 http://dx.doi.org/1… Equisetidae W… Equisetidae Warm… Equisetopsida … Chase, M.W. & …
#>  3    25      25                  25                26 http://www.jstor.or… Equisetales d… Equisetales de C… Equisetidae Wa… Smith, A.R., K…
#>  4   128     128                 128                25 http://www.jstor.or… Equisetaceae … Equisetaceae Mic… Equisetales de… Smith, A.R., K…
#>  5  1142    1142                1142               128 http://www.efloras.… Equisetum Lin… Equisetum Linnae… Equisetaceae M… FNA Editorial …
#>  6  2004    2004                2004              1142 http://www.efloras.… Equisetum sub… Equisetum subg. … Equisetum Linn… FNA Editorial …
#>  7  5467    5467                5467              2004 http://www.efloras.… Equisetum flu… Equisetum fluvia… Equisetum subg… FNA Editorial …
#>  8  5466    5466                5466              2004 http://www.efloras.… Equisetum arv… Equisetum arvens… Equisetum subg… FNA Editorial …
#>  9  5472    5472                5472              2004 http://www.efloras.… Equisetum pra… Equisetum praten… Equisetum subg… FNA Editorial …
#> 10  5471    5471                5471              2004 http://www.efloras.… Equisetum pal… Equisetum palust… Equisetum subg… FNA Editorial …
#> # ℹ 32,760 more rows
#> # ℹ 15 more variables: higherClassification <chr>, class <chr>, order <chr>, family <chr>, genus <chr>, subgenus <chr>,
#> #   specificEpithet <chr>, infraspecificEpithet <chr>, taxonRank <chr>, scientificNameAuthorship <chr>, taxonomicStatus <chr>,
#> #   modified <chr>, license <chr>, bibliographicCitation <chr>, references <chr>
```

The dataset includes 32770 rows (taxa) and 24 columns.

## Validation

Let’s see if the dataset passes validation with dwctaxon.

It is usually a good idea to just run
[`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md)
with default settings the first time. If it passes, you can move on.

``` r

dct_validate(vascan)
#> Error:
#> ! check_sci_name failed
#>    scientificName detected with duplicated value
#>    Bad scientificName: Scilla esculenta Ker Gawler, Arnica monocephala Rydberg, Arnica pedunculata Rydberg, Trifolium tridentatum Lindley, Oenothera angustissima R.R. Gates, Spiraea discolor Pursh, Viola discurrens Greene, Cogswellia simplex (Nuttall ex S. Watson) M.E. Jones, Scilla esculenta Ker Gawler, Ginseng trifolium (Linnaeus) Alph. Wood, Arnica pedunculata Rydberg, Arnica monocephala Rydberg, Swida racemosa (Lamarck) Moldenke, Spiraea discolor Pursh, Swida racemosa (Lamarck) Moldenke, Trifolium tridentatum Lindley, Oenothera angustissima R.R. Gates, Aralia triphylla Poiret, Panax lanceolatus Rafinesque, Panax pusillus Sims, Aralia triphylla Poiret, Ginseng trifolium (Linnaeus) Alph. Wood, Panax lanceolatus Rafinesque, Panax pusillus Sims, Cogswellia simplex (Nuttall ex S. Watson) M.E. Jones, Viola discurrens Greene
```

Looks like we’ve got problems…

To dig into these in more detail, let’s run
[`dct_validate()`](https://docs.ropensci.org/dwctaxon/reference/dct_validate.md)
again, but this time output a summary of errors.

``` r

validation_res <- dct_validate(vascan, on_fail = "summary")
#> Warning: scientificName detected with duplicated value
#> Warning: Invalid column names detected: id

validation_res
#> # A tibble: 27 × 4
#>    taxonID scientificName                                       error                                         check          
#>      <dbl> <chr>                                                <glue>                                        <chr>          
#>  1      NA NA                                                   Invalid column names detected: id             check_col_names
#>  2   10170 Scilla esculenta Ker Gawler                          scientificName detected with duplicated value check_sci_name 
#>  3   10664 Arnica monocephala Rydberg                           scientificName detected with duplicated value check_sci_name 
#>  4   10665 Arnica pedunculata Rydberg                           scientificName detected with duplicated value check_sci_name 
#>  5   16398 Trifolium tridentatum Lindley                        scientificName detected with duplicated value check_sci_name 
#>  6   17569 Oenothera angustissima R.R. Gates                    scientificName detected with duplicated value check_sci_name 
#>  7   20099 Spiraea discolor Pursh                               scientificName detected with duplicated value check_sci_name 
#>  8   21522 Viola discurrens Greene                              scientificName detected with duplicated value check_sci_name 
#>  9   21946 Cogswellia simplex (Nuttall ex S. Watson) M.E. Jones scientificName detected with duplicated value check_sci_name 
#> 10   24660 Scilla esculenta Ker Gawler                          scientificName detected with duplicated value check_sci_name 
#> # ℹ 17 more rows
```

The summary lists one `taxonID` per row. Let’s count these to get a
higher-level view of what’s going on.

``` r

validation_res %>%
  count(check, error)
#> # A tibble: 2 × 3
#>   check           error                                             n
#>   <chr>           <glue>                                        <int>
#> 1 check_col_names Invalid column names detected: id                 1
#> 2 check_sci_name  scientificName detected with duplicated value    26
```

We see there are 2 kinds of errors. There is 1 column with an invalid
name (`id`) and 26 rows with duplicated scientific names.

### Investigate errors

#### Duplicate names

Let’s take a closer look at some of those duplicated names.

``` r

dup_names <-
  validation_res %>%
  filter(grepl("scientificName detected with duplicated value", error)) %>%
  arrange(scientificName)

dup_names
#> # A tibble: 26 × 4
#>    taxonID scientificName                                       error                                         check         
#>      <dbl> <chr>                                                <glue>                                        <chr>         
#>  1   29934 Aralia triphylla Poiret                              scientificName detected with duplicated value check_sci_name
#>  2   31463 Aralia triphylla Poiret                              scientificName detected with duplicated value check_sci_name
#>  3   10664 Arnica monocephala Rydberg                           scientificName detected with duplicated value check_sci_name
#>  4   25705 Arnica monocephala Rydberg                           scientificName detected with duplicated value check_sci_name
#>  5   10665 Arnica pedunculata Rydberg                           scientificName detected with duplicated value check_sci_name
#>  6   25704 Arnica pedunculata Rydberg                           scientificName detected with duplicated value check_sci_name
#>  7   21946 Cogswellia simplex (Nuttall ex S. Watson) M.E. Jones scientificName detected with duplicated value check_sci_name
#>  8   32013 Cogswellia simplex (Nuttall ex S. Watson) M.E. Jones scientificName detected with duplicated value check_sci_name
#>  9   25350 Ginseng trifolium (Linnaeus) Alph. Wood              scientificName detected with duplicated value check_sci_name
#> 10   31464 Ginseng trifolium (Linnaeus) Alph. Wood              scientificName detected with duplicated value check_sci_name
#> # ℹ 16 more rows
```

We can join back to the original data to investigate these names.

``` r

inner_join(
  select(dup_names, taxonID),
  vascan,
  by = "taxonID"
) %>%
  # Just look at the first 6 columns
  select(1:6)
#> # A tibble: 26 × 6
#>    taxonID    id acceptedNameUsageID parentNameUsageID nameAccordingToID                                                 scientificName     
#>      <dbl> <dbl>               <dbl>             <dbl> <chr>                                                             <chr>              
#>  1   29934 29934                2695                NA NA                                                                Aralia triphylla P…
#>  2   31463 31463                2695                NA NA                                                                Aralia triphylla P…
#>  3   10664 10664                2857                NA http://www.efloras.org/volume_page.aspx?volume_id=1021&flora_id=1 Arnica monocephala…
#>  4   25705 25705                2857                NA http://www.botanicus.org/item/31753003488092                      Arnica monocephala…
#>  5   10665 10665                2857                NA http://www.efloras.org/volume_page.aspx?volume_id=1021&flora_id=1 Arnica pedunculata…
#>  6   25704 25704                2857                NA http://www.botanicus.org/item/31753003488092                      Arnica pedunculata…
#>  7   21946 21946                2613                NA NA                                                                Cogswellia simplex…
#>  8   32013 32013                2613                NA http://www.tropicos.org                                           Cogswellia simplex…
#>  9   25350 25350                2695                NA NA                                                                Ginseng trifolium …
#> 10   31464 31464                2695                NA NA                                                                Ginseng trifolium …
#> # ℹ 16 more rows
```

We see that in some cases, multiple entries for the exact same
scientific name (for example, `Arnica monocephala Rydberg`) differ only
in the value for `nameAccordingToID`.

So this seems like something the database manager should fix.

#### Invalid column names

Let’s see what is in the `id` column.

``` r

vascan %>%
  select(id)
#> # A tibble: 32,770 × 1
#>       id
#>    <dbl>
#>  1    73
#>  2    26
#>  3    25
#>  4   128
#>  5  1142
#>  6  2004
#>  7  5467
#>  8  5466
#>  9  5472
#> 10  5471
#> # ℹ 32,760 more rows

n_distinct(vascan$id)
#> [1] 32770
```

`id` contains numbers that are all unique. In other words, these appear
to be unique key values to each row in the dataset (as one would expect
from the name `id`).

It is probably the case that this dataset has a good reason for using
the `id` column, even though it is not a standard DwC column.

### Fixing the data

Let’s see if we can get this dataset to pass validation.

First, let’s remove the duplicated names. This is something that should
be done with more thought, but here let’s just keep the first name of
each pair.

``` r

vascan_fixed <-
  vascan %>%
  filter(!duplicated(scientificName))
```

Next, we will run validation again, but this time allow `id` as an extra
column.

``` r

dct_validate(
  vascan_fixed,
  extra_cols = "id"
)
#> # A tibble: 32,757 × 24
#>       id taxonID acceptedNameUsageID parentNameUsageID nameAccordingToID    scientificName acceptedNameUsage parentNameUsage nameAccordingTo
#>    <dbl>   <dbl>               <dbl>             <dbl> <chr>                <chr>          <chr>             <chr>           <chr>          
#>  1    73      73                  73                NA http://dx.doi.org/1… Equisetopsida… Equisetopsida C.… NA              Chase, M.W. & …
#>  2    26      26                  26                73 http://dx.doi.org/1… Equisetidae W… Equisetidae Warm… Equisetopsida … Chase, M.W. & …
#>  3    25      25                  25                26 http://www.jstor.or… Equisetales d… Equisetales de C… Equisetidae Wa… Smith, A.R., K…
#>  4   128     128                 128                25 http://www.jstor.or… Equisetaceae … Equisetaceae Mic… Equisetales de… Smith, A.R., K…
#>  5  1142    1142                1142               128 http://www.efloras.… Equisetum Lin… Equisetum Linnae… Equisetaceae M… FNA Editorial …
#>  6  2004    2004                2004              1142 http://www.efloras.… Equisetum sub… Equisetum subg. … Equisetum Linn… FNA Editorial …
#>  7  5467    5467                5467              2004 http://www.efloras.… Equisetum flu… Equisetum fluvia… Equisetum subg… FNA Editorial …
#>  8  5466    5466                5466              2004 http://www.efloras.… Equisetum arv… Equisetum arvens… Equisetum subg… FNA Editorial …
#>  9  5472    5472                5472              2004 http://www.efloras.… Equisetum pra… Equisetum praten… Equisetum subg… FNA Editorial …
#> 10  5471    5471                5471              2004 http://www.efloras.… Equisetum pal… Equisetum palust… Equisetum subg… FNA Editorial …
#> # ℹ 32,747 more rows
#> # ℹ 15 more variables: higherClassification <chr>, class <chr>, order <chr>, family <chr>, genus <chr>, subgenus <chr>,
#> #   specificEpithet <chr>, infraspecificEpithet <chr>, taxonRank <chr>, scientificNameAuthorship <chr>, taxonomicStatus <chr>,
#> #   modified <chr>, license <chr>, bibliographicCitation <chr>, references <chr>
```

It passes, so we have now confirmed that the only steps needed to obtain
correctly formatted DwC data are to de-duplicate the species names and
account for the `id` column.

### Summary

This vignette shows how dwctaxon can be used on DwC data to find
possible problems in a taxonomic dataset. We were able to identify
several rows with duplicated scientific names and one column that does
not follow DwC standards. Other than that, it passes validation, giving
us confidence that this dataset can be used for downstream taxonomic
analyses.

[^1]: If you download the data manually, it may be a different version
    than the one used here, v37.12
