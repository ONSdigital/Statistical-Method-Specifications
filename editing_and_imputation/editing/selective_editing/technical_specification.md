# Selective Editing Specification

## 1.0 Meta

* Support Area - Methodology - Editing & Imputation
* Support Contact - <Editing.and.Imputation.expert.group@ons.gov.uk>
* Method Theme - Editing
* Method Classification - Selective Editing
* Status - Partially tested

## 2.0 Description

Selective Editing is a method for highlighting respondants which have a
large impact on further data processing. By applying this method the
efficiency of micro-editing is greatly improved. Note that selective editing
does not actually alter the input values.

## 3.0 Terminology

* Contributor - A respondent identified by a unique identifier.
* Record - The set of input fields corresponding to a single contributor.
* Question - A single question on a form for a given contributor.
* Adjusted return - The unedited returned data value in the current period for
    a given question.
* Predicted value - The value in the previous period for a given question
    (may be the result of imputation).
* Auxiliary value - A secondary predictive value from the current period for
    a given question.
* Standardising factor - The domain group estimate used to standardise scores
    within a given domain group.
* Design weight - The sample design weight.

## 4.0 Method Input and Output

All field names in this document are not definitive; the actual field names
must be configurable and the method used to configure these names is an
implementation detail and thus out of scope of this document.

### 4.1 Input Records

Input records must include the following fully populated fields of the correct
types:

* Unique Identifier - Any
* Design Weight - Numeric
* Threshold - Numeric
* Minkowski Parameter - Numeric - Optional, only if Minkowski Distance is
    being used.

For each question which must be scored:

* Adjusted Return - Numeric
* Auxiliary Value - Numeric
* Standardising Factor - Numeric
* Weight - Numeric - Optional, only if Weighted Mean is used.

#### 4.1.1 Validation

The input values must satisfy the following conditions:

* Threshold > 0
* Design Weight >= 1
* Minkowski value is an integer  >= 1

### 4.2 Output Records

Output records shall always contain the following fields with the following
types:

* Unique Identifier - Any
* Combined Score - Numeric - Optional, only if combined score is used.
* Selective Editing Marker - Boolean

For each question specific score:

* Score - Numeric
* Predicted Marker - Boolean

Fields of type "Any" shall be of the same type as the corresponding input
fields as the values shall be the same in both input and output records.

## 5.0 Algorithm

For each question specific score `s_i` first select the predicted value `p_i`.
Use the previous period data for the respondant if it exists otherwise use the
respondant's auxiliary value for the current period.

Set the Predicted Marker to true in the case that the previous period data
exists, false otherwise.

Given this, `s_i = (100 * a_i * abs(r_i - p_i))/f_i`.
Where:

* `a_i` is the design weight for the respondant
* `r_i` is the adjusted return for the respondant
* `f_i` is the standardising factor for the respondant

For `n` question specific scores (where `n > 1`), the combined score `s`
is one of:

* Maximum question specific score `s = max(lim_(i=1)^n s_i)`
* Weighted mean of scores `s = (sum_(i=1)^n (s_i * w_i)) / (sum_(i=1)^n w_i)`
    where `w_i` is the weight for the question specific score `s_i`.
* Mean score `s = (sum_(i=1)^n s_i) / n`
* Minkowski distance `s = (sum_(i=1)^n (s_i^m))^(1/m)`
    where `m` is the Minkowski parameter.

Otherwise there is only one question specific score `s`.

The resultant score is then compared against the respondant's threshold `t`.
The selective editing marker is the result of the boolean expression `s < t`.

## 6.0 Error Handling

In the case of errors occuring the method shall not result in any output records.
Instead a suitable error shall be emitted.
