# Mean of Ratios Specification

## 1.0 Description

This document specifies the type-specific aspects  for Mean of Ratios
Imputation with reference to the
[General Imputation Method Specification](../general/technical_specification.md).
Unless specified otherwise, all terminology, definitions and sections listed
there apply to this document.

## 2.0 Technical Assumptions

All assumptions below cannot be reasonably validated by the method.

This method assumes that the auxiliary variable is a good predictor of the
target variable. This method also assumes that the contributor's target
variable value in the predictive period is a good predictor of the target
variable in the target period. This same assumption is also made for values
used for link calculations. It is also assumed that groups of contributors
are sufficiently similar and of sufficient size for robust link calculation.

## 3.0 Link Calculation Function

The processes specified in this section must be applied to filtered
responder data and comprise the complete link calculation function for Mean
of Ratios imputation.

### 3.1 Zero Filtering

By default response values of 0 will be filtered in addition to any other
responder filtering. This feature must be able to be disabled.

### 3.2 Trimming

### 3.3 Calculation Function

The function for calculating links for Mean of Ratios imputation for a given
period and grouping in the dataset after trimming is as follows.

For `n` matched pairs of observations for a given group and period, let:

* `x` be the responses in the target period
* `y` be the predictive variable:
  * For forward and backward links, this is the responses in the predictive
    period
  * For construction links, this is the responders' auxiliary variable for
    the target period

The general link formula for a forward or backward link `l` is:

`l = (sum_(k=1)^n x_k/y_k)/n`

The formula for a construction link `c` is:

`c = (sum_(k=1)^n x_k)/sum_(k=1)^n y_k)`
In general the observation count for a link is `n`. If `sum_(k=1)^n y_k = 0`
then `l = 1` and the observation count is 0. If `n = 0` then `l = 1` and the
observation count is null.

For Copyright information, please see LICENCE.
