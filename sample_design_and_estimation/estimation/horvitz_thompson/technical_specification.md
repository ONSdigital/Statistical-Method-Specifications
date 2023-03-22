# Horvitz-Thompson Specification

## 1.0 Terminology

* Design Weight - 
* Calibration Factor - 
* Birth - 
* Death - 

## 2.0 Description

This document specifies the Horvitz-Thompson estimation method. The method
calculates weights for a universe and sample population, then it outputs a
dataset containing weights.

## 3.0 Data Records

All field names in this document are not definitive; the actual field names
must be configurable and the method used to configure these names is an
implementation detail and thus out of scope of this document. In addition,
different functions of this method may require additional fields as per the
type specific specifications.

### 3.1 Input Records

Input records must include the following fields of the correct types:

* Identifier - Any
* Period - String in "YYYYMM" format
* Strata - Any
* Sample Marker - Boolean
* Adjustment Marker (Optional) - String one of "I", "D", "O"
* H Value (Optional) - Boolean
* Auxiliary (Optional) - Numeric
* Calibration Group (Optional) - Any

Unless otherwise noted, fields must not contain null values.

### 3.2 Output Records

Output records shall always contain the following fields with the following
types:

* Period - String in "YYYYMM" format
* Strata - Any
* Design Weight - Numeric
* Unadjusted Design Weight (Optional) - Numeric
* Calibration Group (Optional) - Any
* Calibration Factor (Optional) - Numeric

Fields of type "Any" shall be of the same type as the corresponding input
fields as the values shall be the same in both input and output records.

## 4.0 Method

The complete estimation method consists of several processes many of which are
optional. This includes Birth-Death adjustment, Out-Of-Scope adjustment and
Calibration Factor calculation.

### 4.1 Expansion Estimation

Expansion calculates an unadjusted design weight and then can be optionally be
run in one of four ways to adjust it:

1. No further changes
2. Birth-Death adjusted
3. Fully Out-Of-Scope adjusted
4. Partially Out-Of-Scope adjusted

For all options the unadjusted design weight must be able to be optionally
output in addition to the final design weight.

For the maths, sample_marker and h_value boolean true/false is used as 1/0.

```asciimath
unadjusted_design_weight = sum(sample_marker)/count(sample_marker)
```

If neither adjustment marker or h_value are provided, these column should be
filled with defaults "I" and 0 respectively for the maths to function
appropriately.

```asciimath
death_marker = count(adjustment_marker) where adjustment_marker = "D"
```

```asciimath
out_of_scope_marker = count(adjustment_marker) where adjustment_marker = "O"
```

If partially out of scope is being used the out_of_scope_marker is only on the
denominator and the numerator should be 0.

```asciimath
design_weight = unadjusted_design_weight * (1 + (h_value *
(death_marker + out_of_scope_marker_numerator)/
(sum(sample_marker) - death_marker - out_of_scope_marker_denominator)))
```

### 4.2 Ratio Estimation

After running expansion this method can further run one of two types of ratio
estimation to produce a calibration factor:

1. Separate Ratio
2. Combined Ratio

The calculation is the same for both but separate uses the same strata column
as expansion to group the data and combined uses the calibration group column
instead.

First you need to calculate the aux_design
```asciimath
aux_design = auxiliary * unadjusted_design_weight * sample_marker
```

Then you group by period and grouping to perform the following calculation.
```asciimath
calibration_factor = sum(auxiliary) / sum(aux_design)
```

For Copyright information, please see LICENCE.