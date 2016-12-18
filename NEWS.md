# DBItest 1.4 (2016-12-02)

## DBI specification

- Use markdown in documentation.
- Description of parametrized queries and statements (#88).
- New hidden `DBIspec-wip` page for work-in-progress documentation.
- Get rid of "Format" and "Usage" sections, and aliases, in the specs.

## Tests

- Not testing for presence of `max.connections` element in `dbGetInfo(Driver)` (rstats-db/DBI#56).
- Test multi-row binding for queries and statements (#96).
- New `ellipsis` check that verifies that all implemented DBI methods contain `...` in their formals. This excludes `show()` and all methods defined in this or other packages.
- Refactored `bind_` tests to use the new `parameter_pattern` tweak (#95).
- Rough draft of transaction tests (#36).
- New `fetch_zero_rows` test, split from `fetch_premature_close`.
- The "compliance" test tests that the backend package exports exactly one subclass of each DBI virtual class.
- Document and enhance test for `dbDataType("DBIDriver", "ANY")` (#88).
- Minor corrections for "bind" tests.

## Internal

- Isolate stress tests from main test suite (#92).
- Refactor test specification in smaller modules, isolated from actual test execution (#81). This breaks the documentation of the tests, which will be substituted by a DBI specification in prose.
- Align description of binding with code.
- Refactor tests for `dbBind()`, test is run by `BindTester` class, and behavior is specified by members and by instances of the new `BindTesterExtra` class.
- The `skip` argument to the `test_()` functions is again evaluated with `perl = TRUE` to support negative lookaheads (#33).
- Use `dbSendStatement()` and `dbExecute()` where appropriate.
- Avoid empty subsections in Rd documentation to satisfy `R CMD check` (#81).


# DBItest 1.3 (2016-07-07)


Bug fixes
---------

- Fix `read_table` test when the backend actually returns the data in a different order.


New tests
---------

- Test `dbDataType()` on connections (#69, #75, @imanuelcostigan).
- Check returned strings for UTF-8 encoding (#72).
- Repeated `dbBind()` + `dbFetch()` on the same result set (#51).


Features
--------

- `tweaks()` gains an `...` as first argument to support future/deprecated tweaks (with a warning), and also to avoid unnamed arguments (#83).
- `testthat` now shows a more accurate location for the source of errors, failures, and skips (#78).
- Aggregate skipped tests, only one `skip()` call per test function.
- Indicate that some tests are optional in documentation (#15).


Internal
--------

- New `constructor_relax_args` tweak, currently not queried.
- The `ctx` argument is now explicit in the test functions.
- Change underscores to dashes in file names.
- Remove `testthat` compatibility hack.
- New `all_have_utf8_or_ascii_encoding()` which vectorizes `has_utf8_or_ascii_encoding()`.
- Test on AppVeyor (#73).
- Work around regression in R 3.3.0 (fix scheduled for R 3.3.1) which affected stress tests.


# DBItest 1.2 (2016-05-21)

- Infrastructure
    - Support names for contexts (@hoesler, #67).
    - The `skip` argument to the test functions is now treated as a Perl regular expression to allow negative lookahead. Use `skip = "(?!test_regex).*"` to choose a single test to run (#33).
    - Added encoding arguments to non-ASCII string constants (#60, @hoesler).
- Improve tests
    - `simultaneous_connections` test always closes all connections on exit (@hoesler, #68).
    - More generic compliance check (@hoesler, #61).
    - Update documentation to reflect test condition (@imanuelcostigan, #70).
- `testthat` dependency
    - Import all of `testthat` to avoid `R CMD check` warnings.
    - Compatibility with dev version of `testthat` (#62).
- Improve Travis builds
    - Use container-based builds on Travis.
    - Install `RPostgres` and `RMySQL` from `rstats-db`.
    - Install `DBI` and `testthat` from GitHub.


Version 1.1 (2016-02-12)
===

- New feature: tweaks
    - New argument `tweaks` to `make_context()` (#49).
    - New `tweaks()`, essentially constructs a named list of tweaks but with predefined and documented argument names.
    - `constructor_name`, respected by the `constructor.*` tests.
    - `strict_identifier`, if `TRUE` all identifier must be syntactic names even if quoted. The quoting test is now split, and a part is ignored conditional to this tweak. The `roundtrip_quotes` tests also respects this tweak.
    - `omit_blob_tests` for DBMS that don't have a BLOB data type.
    - `current_needs_parens` -- some SQL dialects (e.g., BigQuery) require parentheses for the functions `current_date`, `current_time` and `current_timestamp`.
    - `union`, for specifying a nonstandard way of combining queries. All union queries now name each column in each subquery (required for `bigrquery`).
- New tests
    - `dbGetInfo(Result)` (rstats-db/DBI#55).
    - `dbListFields()` (#26).
    - New `package_name` test in `test_getting_started()`.
- Improved tests
    - Stress test now installs package in temporary library (before loading `DBI`) using `R CMD INSTALL` before loading DBI (rstats-db/RSQLite#128, #48).
    - Row count is now tested for equality but not identity, so that backends can return a numeric value > 2^31 at their discretion.
    - Call `dbRemoveTable()` instead of issuing `DROP` requests, the latter might be unsupported.
    - Use subqueries in queries that use `WHERE`.
    - Test that `dbClearResult()` on a closed result set raises a warning.
    - Expect a warning instead of an error for double disconnect (#50).
    - Move connection test that requires `dbFetch()` to `test_result()`.
    - Split `can_connect_and_disconnect` test.
    - Expect `DBI` to be in `Imports`, not in `Depends`.
- Removed tests
    - Remove test for `dbGetException()` (rstats-db/DBI#51).
- Bug fixes
    - Fix broken tests for quoting.
- Self-testing
    - Test `RPostgres`, `RMySQL`, `RSQLite` and `RKazam` as part of the Travis-CI tests (#52).
    - Travis CI now installs rstats-db/DBI, updated namespace imports (`dbiCheckCompliance()`, `dbListResults()`).
    - Use fork of `testthat`.
- Utilities
    - Return test results as named array of logical. Requires hadley/testthat#360, gracefully degrades with the CRAN version.
- Internal
    - Refactored the `get_info_()` tests to use a vector of names.
    - Use versioned dependency for DBI
    - Use unqualified calls to `dbBind()` again


Version 1.0 (2015-12-17)
===

- CRAN release
    - Eliminate errors on win-builder
    - Satisfy R CMD check
    - Use LGPL-2 license
    - Add RStudio as copyright holder
    - Move `devtools` package from "Imports" to "Suggests"


Version 0.3 (2015-11-15)
===

- Feature-complete, ready for review
- Tests from the proposal
    - Add missing methods to compliance check
    - Add simple read-only test (#27)
    - Add stress tests for repeated load/unload (with and without connecting) in new R session (#2),
    - Migrate all tests from existing backends (#28)
    - Refactor `data_` tests to use a worker function `test_select()`
    - Test tables with `NA` values above and below the non-`NA` value in `data_` tests
    - Test return values and error conditions for `dbBind()` and `dbClearResult()` (#31)
    - Test vectorization of `dbQuoteString()` and `dbQuoteIdentifier()` (#18)
    - Test that dates have `integer` as underlying data type (#9)
    - Roundtrip tests sort output table to be sure (#32)
    - Test `NA` to `NULL` conversion in `dbQuoteString()`, and false friends (#23)
    - Enhance test for `dbQuoteIdentifier()` (#30)
- Style
    - Avoid using `data.frame()` for date and time columns (#10)
    - Use `expect_identical()` instead of `expect_equal()` in many places (#13)
    - Catch all errors in `on.exit()` handlers via `expect_error()` (#20).
    - Combine "meta" tests into new `test_meta()` (#37)
- Documentation
    - New "test" vignette (#16)
    - Add package documentation (#38)
- Same as 0.2-5


Version 0.2 (2015-11-11)
===

- Tests from the proposal
    - SQL
    - Metadata
    - DBI compliance (not testing read-only yet)
- Migrate most of the tests from RMySQL
- Test improvements
    - Test BLOB data type (#17)
    - Check actual availability of type returned by `dbDataType()` (#19)
- Testing infrastructure
    - Disambiguate test names (#21)
    - Use regex matching for deciding skipped tests, skip regex must match the entire test name
- Documentation
    - Document all tests in each test function using the new inline documentation feature of roxygen2
    - Improve documentation for `test_all()`: Tests are listed in new "Tests" section
    - Add brief instructions to README
- Move repository to rstats-db namespace
- Same as 0.1-6


Version 0.1 (2015-10-11)
===

- First GitHub release
- Builds successfully on Travis
- Testing infrastructure
    - Test context
    - Skipped tests call `skip()`
    - Function `test_all()` that runs all tests
- Tests from the proposal
    - Getting started
    - Driver
    - Connection
    - Results
- Code formatting is checked with lintr
- Same as 0.0-5
