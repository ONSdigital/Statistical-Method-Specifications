# Thousand Pounds Correction Specification

## 1.0 Meta

* Support area – Methodology – Processing, Editing & Imputation
* Method theme – Automatic Editing
* Status – draft, not tested

## 2.0 Terminology

* Contributor - A member of the sample; identified by a unique identifier.
* Record - A set of values for each contributor and period.
* Target Period - The period currently undergoing data validation.
* Principal Variable – Variable that the method is working on and will
 determine if the remaining monetary variables, if any, will be
 automatically corrected.
* Target Variable(s) – List of all monetary variables that may be automatically
 corrected, excluding the principal variable.
* Target Record - A contributor's record in the target period.
* Predictive Variable - The corresponding value used as predictor for the
 principal variable for each contributor.
* Predictive Record - The record containing a contributor's predictive value.
* Predictive Period - The period containing predictive records; defined
 relative to the target period.
* Auxiliary variable - An alternative variable used as a predictor for a
 contributor's principal variable, where the predictive value is not available
 for a given contributor (i.e. where the contributor was not sampled in the
 predictive period).
* Responder - A contributor who has responded to the survey within a given period.

## 3.0 Introduction

The thousand pounds correction is commonly used across business surveys. It is
a generic rule that uses user defined thresholds to automatically detect and
correct thousand pounds errors. This is when the respondent should have reported
values in thousands of pounds but has reported in actual pounds e.g., returned
a value of £56,000 instead of correctly submitting 56.

## 4.0 Assumptions

* A ratio inside of the upper or lower thresholds is due to a thousand pounds error
* If the principal variable is found to be a thousand pounds error, then it is
 assumed that all other monetary values are also thousand pounds errors
* The principal variable and predictive value are well correlated and are intended
 to be reported in the same denomination (i.e., thousand pounds)
* The principal variable and auxiliary value are well correlated and intended
 to be reported in the same denomination (i.e., thousand pounds)
* The auxiliary variable is known and available for all contributors processed
 by the method
* Thresholds set are a good indication of whether a value should be corrected

## 5.0 Method Input and Output - need DST input to finalise

### 5.1 Input Records

Input records must include the following fields of the correct types:

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* Principal Variable – Single variable, numeric
* Target Variable(s) – Can be a list, numeric – nulls allowed
* Predictive Variable – Single variable, numeric – nulls allowed
* Auxiliary Variable – Optional, numeric – nulls allowed
* Upper Limit – Single variable, numeric, must be greater than Lower Limit
* Lower Limit – Single variable, numeric, must be less than Upper Limit

Unless otherwise noted, fields must not contain null values.
 All other fields shall be ignored.

The method requires at least one of the following as the previous period predictive
 variable, in the given priority order:

1. Returned, cleaned response
2. Imputed or constructed value
3. Auxiliary variable e.g., registered annual turnover

Although predicted and auxiliary are both optional, at least one must be provided
 for the method to work. Else the method will not run.

Note that the predictive variable is indirectly defined as the principal variable
in the predictive period.

### 5.2 Output Records

Output records shall always contain the following fields with the following types:

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* TPC Ratio – Numeric
* Final Principal Variable – Numeric
* Final Target Variables – Can be a list, numeric – nulls allowed
* TPC Marker –  To indicate the result of the Thousand Pounds Correction
 method, string

Fields of type "Any" shall be of the same type as the corresponding input
 fields as the values shall be the same in both input and output records.

The TPC Marker must be one of the following:

* C = Correction applied
* N = No correction applied
* S = Method stops

## 6.0 Method

### 6.1 Overall Method

A principal variable must be specified as a priority indictor for whether a
 thousand pounds error has occurred. The method checks the principal variable
 for a given contributor to determine whether the ratio of the principal variable
 by the predictive variable is within a fixed set of upper and lower thresholds.
 If the ratio lies within these thresholds, then a thousand pounds correction is
 automatically applied to the principal variable and the rest of the target
 variables, if any, are automatically corrected.

If the predictive period’s data is missing, then the method is not applied, unless
 an appropriate variable that is well correlated with the target variable is
 available to the user. The auxiliary variable should not be read into the data
 if the user does not require it.

### 6.2 Error Detection

The error detection calculation is applied to each contributor and calculates the
 ratio of the principal variable and predictive variable at the contributor level.
 The principal variable is the current period data value, and the predictive variable
 is the corresponding previous period data value, if the contributor was previously
 sampled. Previous period data can be a clean response, imputed or constructed data
 value. If there is no predictive value available (i.e., the contributor was
 not sampled in the previous period), then a well correlated alternative
 auxiliary variable can be used if available required by the user. Note that
 the auxiliary variable should be recorded in the same denomination as
 the target variable.

If the ratio is within the predefined upper and lower thresholds, then a
 thousand pounds error is detected.

If the predictive or auxiliary variable's value is zero or missing, then the method
 does not continue. A thousand pounds error is neither detected nor corrected.

### 6.3 Error Correction

A detected thousand pounds error will be automatically corrected by dividing the
 principal variable (i.e., suspicious returned value) by 1000 then rounding to
 the nearest whole number.

All other monetary questions, the target variables excluding the principal variable,
 on the form will be automatically corrected as described without checking the
 returned or corresponding previous values.

### 6.4 Method Error Handling

In the case of the method experiencing processing issues, the method shall not result
 in any output records. Instead, a suitable error description shall be emitted.

## 7.0 Calculations

### 7.1 Error Detection Calculation

If a predictive value for the principal question *q* is available for contributor
 *i* at time *t-1*, then a thousand pounds error is detected if the following ratio
 lies within the defined lower or upper thresholds, $L_{Lower}$
 or $L_{Upper}$.

$$ \large L_{Lower} < \frac{y_{i, q, t}}{y_{i, q, t-1}} < L_{Upper} $$

```asciimath
L_{Lower} < \frac{y_{i, q, t}}{y_{i, q, t-1}} < L_{Upper}
```

Where $y_{i, q, t}$ is the returned value for the principal question *q* and
 $y_{i, q, t-1}$ is its corresponding previous period value. Previous period
 data may be a clean response, impute or constructed data.

If the ratio lies within, and not equal to, the limits, then a thousand pounds error
 is detected; else a thousand pounds error has not been identified.

If a cleared, predictive value does not exist and the user has chosen to input an
 auxiliary variable, then the error detection calculation is as follows:

$$ \large L_{Lower} < \frac{y_{i, q, t}}{x_{i, q, t}} < L_{Upper} $$

```asciimath
L_{Lower} < \frac{y_{i, q, t}}{x_{i, q, t}} < L_{Upper}
```

Where $x_{i, q, t}$ is the well correlated auxiliary variable for contributor
 *i* and appropriately converted to the same denomination as the principal
 variable, if necessary.

If the ratio lies within the limits, then a thousand pounds error is detected;
 else a thousand pounds error has not been identified.

### 7.2 Error Correction Calculation

A detected error for question *q* is automatically corrected for
 contributor *i* at time *t* by:

$$ \large \hat{y}\_{i, q, t} = \frac{y_{i, q, t}}{1000} $$

```asciimath
\hat{y}_{i, q, t} = \frac{y_{i, q, t}}{1000}
```

Where $\hat{y}\_{i, q, t}$ is the corrected value and rounded to the
 nearest whole number.

Once the principal target variable has been corrected, all other monetary values,
 the remaining target variables, for a given contributor will be automatically
 corrected by the same method as described above.
