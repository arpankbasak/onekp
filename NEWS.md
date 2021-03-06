# onekp 0.2.1

 First rOpenSci version

 * Transfer ownership to rOpenSci

 * Update README
 
 * Add .github files

 * Add reviewers to DESCRIPTION

# onekp 0.2.0

 The goal of this version is to address all issues raised by the rOpenSci
 editor (Scott Chamberlain) and my two reviewers: Jessica Minnier and Zachary
 Foster.

## Updates based on editor comments

 * Added `URL` tag to DESCRIPTION

 * Added `BugReports` tag to DESCRIPTION

 * Wrapped all lines longer than 80 characters

 * Removed unused imports `readr` and `tidyr`

 * Split test functions into smaller test files

 * Add `skip_on_cran` and `skip_on_travis` to `retrieve_onekp` test

 * Save metadata as package data: `data(onekp)` 

 * Add minimum version to `taxizedb`

## Updates based on Zachary Foster's review

 * Add space between equal signs in argument lists

 * Change package name from `oneKP` to `onekp`

 * Let user set the download directory (defaulting to a tempdir).

 * Re-export the `magrittr` `%>%` operator

 * Update README

 * Add CONDUCT.md

 * Add R `devel` test to travis

 * More package-level documentation

 * Do not export print.OneKP function

 * Add documentation details to `retrieve_onekp`

 * Update intro vignette

 * Do not run examples that access the internet (call `retrieve_onekp`)

## Updates based on Jessica Minnier's review  

 There was some overlap between the two reviews, here I just list updates made that are unique to this review.

 * Clarify `filter_by_code` and `filter_by_species` examples

 * Cleanup the `download` documentation

 * Add `absolute` parameter to `download_*` functions. If TRUE, this returns
   the absolute path to the retrieved data.

 * Add handling for downloading nothing: if everything is filtered out, just
   return an empty character vector.

 * Add test coverage for corner cases

   - filtering by clade with a clade that is not in the NCBI common tree raises
     an error.

   - filtering with valid input, but where no rows are selected, returns
     a OneKP object with 0 rows (no error).

   - download an empty object returns an empty vector of filenames (no error)

# onekp 0.1.0

 * Initial release
