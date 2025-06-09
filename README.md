# CompliantRMed

As R continues to gain momentum in clinical research, regulatory agencies are paying closer attention to how it’s used in regulated environments. Whether supporting statistical analysis, data visualization, or automation pipelines, R packages must meet stringent validation requirements to be considered fit for use in clinical trials and other regulated activities. But what does validation actually mean in this context—and how can researchers, data scientists, and system administrators ensure their R-based tools hold up under scrutiny? This talk will walk through practical strategies for validating R packages in alignment with regulatory expectations, including those from FDA, EMA, and under ICH guidelines such as E6(R3), E9, and M10. We’ll explore the intersection of open-source software and GxP compliance, unpacking the nuances of how to assess, document, and justify the use of community-developed or custom-built R packages. Attendees will learn: • What regulators expect when R is used in a clinical or GxP-regulated environment. • How to categorize R packages based on risk and intended use. • Validation approaches for both CRAN packages and internally developed tools. • How to document package selection, qualification, and performance testing. • Best practices for ongoing change control, version tracking, and reproducibility. • Real-world examples of validation frameworks and testing workflows using tools like {testthat}, {renv}, and {devtools}. Whether you’re part of a small academic research team or a large sponsor organization, this session offers practical guidance for creating defensible validation packages that support transparency, reproducibility, and regulatory compliance. We’ll also touch on tools and templates that can help streamline validation documentation and collaborate with Quality Assurance teams more effectively. By the end of this talk, you’ll walk away with a clearer understanding of what it takes to “make R compliant,” how to integrate validation into your development workflows, and how to future-proof your R environment in a regulated research setting. Because in clinical research, compliance isn’t just a checkbox—it’s part of building trust in our data, our methods, and ultimately, the science we support.

## Welcome

-   Purpose: Show how to test and validate R packages for use in clinical environments\
-   Aim: To walk through validation approaches using the `assertthat` package

------------------------------------------------------------------------

## Why Validate?

-   R is open source — many packages are:
    -   Not formally tested\
    -   Maintained by individuals\
-   Clinical trial environments require:
    -   Verified tools\
    -   Documented validation\
    -   **Repeatable results**

------------------------------------------------------------------------

## When Is Validation Required?

-   Any R package used in:
    -   Data analysis submitted to regulatory bodies\
    -   Statistical programming for endpoints\
    -   Data cleaning or transformation steps\
-   Applies to:
    -   Internal packages\
    -   CRAN packages\
    -   GitHub packages (higher risk!)

------------------------------------------------------------------------

## Regulatory Context

-   **FDA Guidance** on Computerized Systems in Clinical Investigations\
-   **ICH E6(R3)** – Data integrity, system validation\
-   **ICH E9** – Statistical principles and reproducibility\
-   **21 CFR Part 11** – Electronic records and audit trails

------------------------------------------------------------------------

## A Quick Note

> The FDA’s “R: Regulatory Compliance and Validation Issues” applies **only to the base R distribution**\
> But the philosophy behind it can still guide validation for external and internal packages.\
> That’s the motivation behind this session.

------------------------------------------------------------------------

## What Needs to Be Proven

-   Package is suitable for intended use\
-   Key functions behave as expected\
-   Input/output integrity is maintained\
-   Errors are handled gracefully\
-   Environment is documented and controlled

------------------------------------------------------------------------

## Validation Depends on Risk

-   Not all packages require the same depth of validation\
-   Risk = combination of:
    -   Impact on primary endpoints\
    -   Source and complexity\
    -   Intended use (internal vs. submission)\
-   Examples:
    -   **Low Risk** – plotting, QA scripts\
    -   **Medium Risk** – cleaning, reshaping\
    -   **High Risk** – statistical outputs for submission

------------------------------------------------------------------------

## Validation Strategy

-   Step 1: Document the Package\
-   Step 2: Identify Key Functions\
-   Step 3: Write Tests\
-   Step 4: Run in Controlled Environment\
-   Step 5: Record Results\
-   Step 6: Review and Approve

------------------------------------------------------------------------

## Step 1: Document the Package

-   Name, version, source\
-   License (CRAN, GitHub, internal)\
-   Dependencies\
-   Intended use\
-   Risk classification

------------------------------------------------------------------------

## Step 2: Identify Key Functions

-   Which functions do you actually use?\
-   Examples:
    -   `survival::coxph` for statistical modeling\
    -   `assertthat::assert_that` for validation\
    -   `ggplot2::ggplot` for data visualization

------------------------------------------------------------------------

## Step 3: Write Tests

-   Use `testthat`, `tinytest`, or similar\
-   Validate:
    -   Expected output\
    -   Error handling\
    -   Edge cases\
-   Don’t rely only on author-supplied tests

------------------------------------------------------------------------

## Step 4: Run in Controlled Environment

-   Use reproducible infrastructure:
    -   DEV/test server\
    -   Docker container\
    -   Controlled VM\
-   Lock R version and packages with `renv`\
-   Log session info with `sessioninfo`

------------------------------------------------------------------------

## Step 5: Record Results

-   Save:
    -   Test scripts\
    -   Output logs\
    -   Session info\
-   Use `capture.output()`, `test_dir()`, or `with_reporter()`\
-   Digitally sign if required by SOP

------------------------------------------------------------------------

## Step 6: Review and Approve

-   Internal QA or statistical lead should:
    -   Review validation artifacts\
    -   Sign off summary\
    -   File in eTMF or SOP-controlled folder

------------------------------------------------------------------------

## Tooling for Reproducibility

-   `renv::init()` – Lock package versions\
-   `devtools::check()` – Run checks for internal packages\
-   `sessioninfo::session_info()` – Capture platform state\
-   Ensures:
    -   Traceability\
    -   Reproducibility\
    -   Environment control

------------------------------------------------------------------------

## Version Control and Change Management

-   Track package versions, changes, and approvals

**Use Git to:**

-   Commit validation changes\
-   Tag releases\
-   Collaborate with QA\
-   Document reasons for upgrades\
-   Archive older validations if replaced

------------------------------------------------------------------------

## Case Study: Validating `assertthat`

### Package Overview

-   **Package**: `assertthat`\
-   **Version**: 0.2.1\
-   **License**: MIT\
-   **Purpose**: Input validation and readable assertions\
-   **Dependencies**: base R only\
-   **Maintainer**: Hadley Wickham

## Code Review

``` r
library(testthat)
library(assertthat)

test_that("assert_that passes when TRUE", {
  expect_silent(assert_that(1 == 1))
})

test_that("assert_that fails when FALSE", {
  expect_error(assert_that(1 == 2), regexp = "not equal")
})
```

```{r}
library(testthat)
library(assertthat)

test_that("assert_that passes when TRUE", {
  expect_silent(assert_that(1 == 1))
})

test_that("assert_that fails when FALSE", {
  expect_error(assert_that(1 == 2), regexp = "not equal")
})
```

------------------------------------------------------------------------

## Description of the Code

-   `testthat` checks the behavior of `assert_that()`
-   First test: should pass silently
-   Second test: should raise an error
-   These are examples of verifying intended behavior
-   Demonstrates how even small packages require validation

::: notes
Use `expect_silent()` to confirm no output or warnings occur.\
Seeing a 😀 or ✅ means the test framework worked — not the function.\
That distinction matters in regulated environments.
:::

## Code Demo / walkthrough

## 1. Introduction


  This document presents a validation report for selected R functions and packages using the `testthat` framework. The goal is to ensure that these functions behave as expected under defined inputs and edge cases.

## 2. Test Environment

``` {r}
library(knitr)

kable(data.frame(
  Component = c(
    "R Version", 
    "Platform", 
    "Packages Tested", 
    "Test Framework", 
    "Operating System"
  ),
  Details = c(
    as.character(getRversion()),
    R.version$platform,
    "assertthat, custom functions",
    "testthat",
    Sys.info()[["sysname"]]
  )
))

```

## 3. Validation Objectives

  - Confirm `assert_that()` behaves correctly with valid and invalid conditions.
- Validate custom functions used in clinical/statistical workflows.
- Provide reproducible, auditable test results.
- Demonstrate test-driven validation for regulatory readiness.

---
## 4. Test Cases and Scripts

```r
library(testthat)
library(assertthat)

# Test 1: Basic TRUE condition passes silently
test_that("Basic TRUE condition passes silently", {
  expect_silent(assert_that(1 == 1))
})

# Test 2: FALSE condition throws expected error
test_that("False condition throws an error", {
  expect_error(assert_that(1 == 2), regexp = "not equal")
})

# Custom function: calculate_bmi
calculate_bmi <- function(weight, height) {
  assert_that(weight > 0, height > 0)
  weight / (height^2)
}

test_that("calculate_bmi returns correct result", {
  expect_equal(calculate_bmi(70, 1.75), 22.85714, tolerance = 1e-4)
})

test_that("calculate_bmi throws error for invalid input", {
  expect_error(calculate_bmi(-70, 1.75), regexp = "weight > 0")
})

# Custom function: convert_temperature
convert_temperature <- function(temp, to = "F") {
  assert_that(is.numeric(temp))
  assert_that(to %in% c("F", "C"))

  if (to == "F") {
    return(temp * 9/5 + 32)
  } else {
    return((temp - 32) * 5/9)
  }
}

test_that("convert_temperature works for valid inputs", {
  expect_equal(convert_temperature(0, "F"), 32)
  expect_equal(convert_temperature(32, "C"), 0)
})

test_that("convert_temperature fails for invalid input", {
  expect_error(convert_temperature("cold", "F"), regexp = "is.numeric")
  expect_error(convert_temperature(20, "K"), regexp = "to %in%")
})
```

## 5. Test Cases and Scripts - Outputs
```{r, error=TRUE}
library(testthat)
library(assertthat)

# Test 1: Basic TRUE condition passes silently
test_that("Basic TRUE condition passes silently", {
  expect_silent(assert_that(1 == 1))
})

# Test 2: FALSE condition throws expected error
test_that("False condition throws an error", {
expect_error(assert_that(1 == 2), regexp = "not equal")
})

# Custom function: calculate_bmi
calculate_bmi <- function(weight, height) {
  assert_that(weight > 0, height > 0)
  weight / (height^2)
}

test_that("calculate_bmi returns correct result", {
  expect_equal(calculate_bmi(70, 1.75), 22.85714, tolerance = 1e-4)
})

test_that("calculate_bmi throws error for invalid input", {
  expect_error(calculate_bmi(-70, 1.75), regexp = "weight > 0")
})

# Custom function: convert_temperature
convert_temperature <- function(temp, to = "F") {
  assert_that(is.numeric(temp))
  assert_that(to %in% c("F", "C"))
  if (to == "F") {
    return(temp * 9/5 + 32)
  } else {
    return((temp - 32) * 5/9)
  }
}

test_that("convert_temperature works for valid inputs", {
  expect_equal(convert_temperature(0, "F"), 32)
  expect_equal(convert_temperature(32, "C"), 0)
})

test_that("convert_temperature fails for invalid input", {
  expect_error(convert_temperature("cold", "F"), regexp = "is.numeric")
  expect_error(convert_temperature(20, "K"), regexp = "to %in%")
})
```



## Key Takeaways

-   Validation should reflect package risk and intended use\
-   Use R-native tools to document and automate testing\
-   Record and version everything\
-   Partner with QA early in the process\
-   Good documentation beats complexity every time

## Contact Info

I'd love to hear from you - how you're validating, tools you've used, auditor's findings you can share - so please email me at chris.battiston@wchospital.ca!  

------------------------------------------------------------------------

Validation Summary ((Very generic) Template)

Package: assertthat

Version: 0.2.1

Source: CRAN

Purpose: Input validation

Risk Level: Medium

Tests Conducted: 4

Environment: R 4.3.3, Windows 10, renv locked

Reviewed By: [Name]

Date: [Date]


