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

## 4.0 Calculations

For each score:
* Select predicted value. Use previous period data if it exists otherwise use
    auxiliary. Predicted Marker is true in the case that previous period data
    exists, false otherwise.
* `Score = (100 * design_weight * modulus(adjusted_return - predicted_value))/standardising_factor

If multiple scores then either:

* Get max of the scores
* Weighted mean of scores = sum(weight*score)/sum(weight)
* Mean score (The above but weight = 1)
* Minkowski distance = (sum(score^p))^(1/p)

Then check score against threshold. Selective editing marker is the result of
the expression `score < threshold`.
