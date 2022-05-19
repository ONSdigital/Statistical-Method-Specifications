# Selective Editing Specification

## 1.0 Meta

* Support Area - Methodology - Editing & Imputation
* Support Contact - <Editing.and.Imputation.expert.group@ons.gov.uk>
* Method Theme - Editing
* Method Classification - Selective Editing
* Status - Partially tested

## 2.0 Description

Selective Editing is a method for highlighting respondants which have a large
impact on further data processing. By applying this method the efficiency of
micro-editing is greatly improved.

## 3.0 Method Input and Output

All field names in this document are not definitive; the actual field names
must be configurable and the method used to configure these names is an
implementation detail and thus out of scope of this document.

### 3.1 Input Records

Input records must include the following fully populated fields of the correct
types:

* Unique Identifier - Any
* design weight - Numeric
* Threshold - Numeric
* Minkowski Parameter - Numeric - Optional, only if Minkowski is being used.

For each single score:
* adjusted return - Numeric
* Auxiliary Value - Numeric
* Standardising factor - Numeric

#### 3.1.1 Validation

The input values must satisfy the following conditions:

* Threshold > 0
* Design Weight >= 1
* Minkowski value is an integer >= 1

### 3.2 Output Records

Output records shall always contain the following fields with the following
types:

* Unique Identifier - Any
* Score - Numeric
* Predictive Marker - String
* Combined Score - Numeric - Optional, only if combined score is used.
* Selective Editing Marker - Boolean

Fields of type "Any" shall be of the same type as the corresponding input
fields as the values shall be the same in both input and output records.

## 4.0 Calculations

For each score:
* Select predicted value. Use previous period data if exists otherwise use
    auxiliary. Input Flag as P or A.
* Score = (100 * design_weight * modulus(adjusted_return - predicted_value))/standardising_factor

If multiple scores then either:

* Get max of the scores
* Weighted mean of scores = sum(weight*score)/sum(weight)
* Mean score (The above but weight = 1)
* Minkowski distance = (sum(score^p))^(1/p)

Then check score against threshold. Selective editing marker is the result of
the expression `score < threshold`.
