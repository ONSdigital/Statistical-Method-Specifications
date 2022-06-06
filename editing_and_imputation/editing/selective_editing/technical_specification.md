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

## 3.0 Method Input and Output

All field names in this document are not definitive; the actual field names
must be configurable and the method used to configure these names is an
implementation detail and thus out of scope of this document.

### 3.1 Input Records

Input records must include the following fully populated fields of the correct
types:

* Unique Identifier - Any
* Design Weight - Numeric
* Threshold - Numeric
* Minkowski Parameter - Numeric - Optional, only if Minkowski Distance is
    being used.

For each single score:
* Adjusted Return - Numeric
* Auxiliary Value - Numeric
* Standardising Factor - Numeric
* Weight - Numeric - Optional, only if Weighted Mean is used.

#### 3.1.1 Validation

The input values must satisfy the following conditions:

* Threshold > 0
* Design Weight >= 1
* Minkowski value is an integer  >= 1

### 3.2 Output Records

Output records shall always contain the following fields with the following
types:

* Unique Identifier - Any
* Combined Score - Numeric - Optional, only if combined score is used.
* Selective Editing Marker - Boolean

For each single score:
* Score - Numeric
* Predicted Marker - Boolean

Fields of type "Any" shall be of the same type as the corresponding input
fields as the values shall be the same in both input and output records.

## 4.0 Algorithm

For each single score `s_i` first select the predicted value `p_i`. Use the
previous period data for the respondant if it exists otherwise use the
respondant's auxiliary variable for the current period. The Predicted Marker
is true in the case that the previous period data exists, false otherwise.

Given this, `s_i = (100 * a_i * abs(r_i - p_i))/f_i`.
Where:

* `a_i` is the unadjusted design weight for the respondant
* `r_i` is the date adjusted return for the respondant
* `f_i` is the standardising factor for the respondant

For `n` number of scores (where `n > 1`) then the combined score `s` is one of:

* Maximum single score `s = max(lim_(i=1)^n s_i)`
* Weighted mean of scores `s = sum_(i=1)^n (s_i * w_i) / sum_(i=1)^n w_i`
* Mean score (The above but weight = 1)
* Minkowski distance `s = (sum_(i=1)^n s_i^p)^(1/p)

Otherwise there is only one single score `s`.

The resultant score is then compared against the respondant's threshold `t`.
The selective editing marker is the result of the boolean expression `s < t`.