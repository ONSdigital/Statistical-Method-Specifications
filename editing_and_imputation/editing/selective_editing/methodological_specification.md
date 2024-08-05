# Selective Editing Specification

## 1.0 Meta

* Support Area - Methodology - Editing & Imputation
* Method Theme - Editing
* Status – Fully tested

## 2.0 Terminology

* Contributor reference - A respondent identified by a unique
  identifier.
* Adjusted return - The most recent unedited returned data
  value (for a given variable) in the current period, t.
* Predicted value - The first predictor value (for a given
  variable) for the current period adjusted return.
* Auxiliary predicted value - This is the (secondary)
  auxiliary predictor (for a given variable) for the current
  period adjusted return.
* Standardising factor - The domain group estimate used to
  standardise scores within a given domain group.
* Design weight - An a-weight, usually generated in another
  method.
* Selective Editing domain group - States which Selective
  Editing domain group, a given respondent belongs to.
* Selective Editing threshold - Unique threshold, to compare
  to the score, for each domain group.
* Combining function - If the method is applied to more than
  one variable, then the scores can be combined in the
  following ways: Average, Sum, Max, Weighted mean, Minkowski.

## 3.0 Summary

Selective Editing is an internationally recognised editing method
where potential errors are prioritised according to their expected
impact on key outputs, for one time period. Only respondents that
are having a significant impact on published estimates will be
recontacted for validation.

Selective Editing works by assigning a score to each important
variable for a contributor where, the score reflects the impact that
editing the respondent will have on the estimates. Only contributors
with a score above a predetermined threshold are flagged for manual
review to be validated, low scoring contributors pass
through unchecked.

## 4.0 Assumptions

* All data inputs required by the method are available and are on a
  standardised basis.
* The predicted variable and auxiliary predicted value should both
  be good predictors of the returned data for time t.
* The predicted value in the score calculation must have been cleaned
  and free from errors. This is not limited to genuine returns, and
  it may be an imputed or constructed value if a clean response is
  not available.
* Each respondent is clearly classified into one mutually exclusive
  domain group.
* The thresholds specified are valid and appropriate (>0)
* If the "weighted mean score" is selected to combine score, then the
  sum of the weights provided should total to 1.
  
## 5.0 Method input and output

All field names in this document are not definitive, the actual field
names must be configurable, and the method used to configure these
names is an implementation detail and thus out of scope of this
document.

### 5.1 Input records

Input records must include the following fields of the
correct types:

* Reference - Any
* Question list - List of strings
* Adjusted return - Numeric
* Predicted value - Numeric
* Auxiliary predicted value - Numeric
* Standardising factor - Numeric
* Design weight - Numeric
* Threshold - Numeric

When using the SML code, it will automatically assign
adjusted return (ar), predicted value (pv), auxiliary predicted
value (apv) and standardising factor (sf) based on the column names
of the inputs. Please see User notes for further
explanations.

Unless otherwise noted, fields must not contain Null values. All other
fields shall be ignored.

### 5.2 Output records

Output records shall always contain the following fields with the
following types:

* Reference - Any
* Score1 - Numeric
* ScoreM - Numeric
* Final_Score - Numeric
* Selective Editing marker - Boolean
* Predicted marker, seen as '_pm' on adjusted return - Boolean

## 6.0 Method

### 6.1 Score calculation

A selective editing score is calculated for each reporting unit i in
time t. The selective editing score is calculated by multiplying the
design weight by the modulus of the adjusted return for reporting unit
i at time t by the predicted value for reporting unit i at time t,
divided by the standardising factor, and all multiplied by 100 as shown
by the equation below.

The predicted value is equal to a clean response for (i.e. free from
error adjusted return) for reporting unit i at time t-1. However, if
a clean response for reporting unit i at time t-1 is not available
then imputed or constructed previous period data is used. If this
value isn't available, then the auxiliary predicted value for reporting
unit i at time t is used.

If the predicted value is the adjusted return for reporting unit i at
time t-1 then the method output will contain a column with the ending
"_pm" for predictive marker which will be set to 'True'.

If the predicted value is the auxiliary predicted value for reporting
unit i at time t then the method output will contain a column with the
ending "_pm" for predictive marker which will be set to 'False'.

The standardising factor is the weighted domain estimate for a given
variable at time t-1, which is used as a means to determine a
respondent's potential impact on key output estimates.

The Selective Editing calculation is as follows:

```asciimath
Score = 100*{{a-weight*|current value-predicted value|}/Standardising factor}
```

### 6.2 Combining Scores

This is only required if selective editing is applied to more than one
variable. The score calculation is used for each variable required and
a score is produced. The method of combining scores is then chosen and
the final score is compared to the threshold

The overall final scores may be combined in the following ways:

* Maximum score: The maximum score of all the variables.
* Weighted mean score: Where each score is multiplied by a weight, where
  all weights will add up to 1.
* Mean score: Where all scores are added up and divided by the number of
  scores.
* Minkowski distance: The final score is equal to each score multiplied to
 the power of p, all the scores are then summed and then multiplied to the
 power of 1/p. Where p >= 1 and is an integer.
  
### 6.3 Generate selective editing marker

For each final score compare it to the threshold which are associated with
the respondent's domain group.

If the final score is greater than or equal to the selective editing
threshold, then output the selective editing marker for the reporting
unit as False for failed selective editing and the respondent requires
validation.

If the final score is less than the selective editing threshold, then
output the selective editing marker as True for passed selective
editing and does not require validation.

For Copyright information, please see LICENCE.
