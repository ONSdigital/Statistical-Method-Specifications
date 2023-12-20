# One Sided Winsorisation Method Specification

## 1.0 Terminology

* Contributor - A member of the sample identified by an identifier.
* Record - A set of values for each contributor and period. At most one
  record for a contributor can be present for a given period.
* Target Variable - The variable (A.K.A. field) in the record to be winsorised.
* Grouping or group - How the data has been broken into subsets.

## 2.0 Description

This document specifies the one sided winsorisation method. The method
outliers data and outputs a separate dataset containing if winsoration
has been applied and the matching outlier weight.

## 3.0 Data Records

All field names in this document are not definitive; the actual field names
must be configurable and the method used to configure these names is an
implementation detail and thus out of scope of this document. In addition,
different uses of winsorisation may require additional fields as per the
type specific specifications.

### 3.1 Input Records

Unless otherwise noted, fields specified in this section must be fully
populated.

Input records must include the following fields of the correct types:

* Reference - Any
* Period - Any
* Grouping - Any
* Target Variable - Numeric
* Design Weight Variable - Numeric

Optionally the following fields can also be passed:

* L Value - Numeric
* Calibration Factor - Numeric
* Auxiliary Variable - Numeric

A reference must be unique within a given period and group and the method
must support the same identifier appearing in different groups within the
same period. This is to allow multiple independent groups of data for a
given contributor to be outliered as part of the same sample.

### 3.2 Output Records

Output records shall always contain the following fields with the following
types:

* Reference - Any
* Period - Any
* Grouping - Any
* Outlier Weight - Numeric
* Outlier Marker - String

Fields of type "Any" shall be of the same type as the corresponding input
fields as the values shall be the same in both input and output records.
Unless otherwise specified, the fields above must be fully populated.

## 4.0 Method

The complete winsorisation method consists of a single
process which fills any missing optional columns with 1.

One of three things can occur, winsorised 'W' with a calculated outlier weight,
not winsorised because it is fully enumerated 'NW_FE' or not winsorised because
of bad weight/factor 'NW_AG'.

### 4.1 Separate Out Values Not To Be Winsorised

Calculate design_calibration = design weight * calibration factor.
Set marker = 'NW_FE' where design weight and calibration factor = 1
Set marker = 'NW_AG' where design weight * calibration factor <= 1
Set weight = 1 for both scenarios.

### 4.2 Modify Target Variable

modifier = (sum(target*design)/sum(aux*design)) + (l_value/(design_calibration-1))
If target > modifier
Then (target/design_calibration) + (modifier - (modifier/design_calibration))
Otherwise leave unmodified.

### 4.3 Calculate Weight

Set weight = modified_target/target or where target = 0, set weight = 1
Set marker = 'W'

For Copyright information, please see LICENCE.
