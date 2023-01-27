# Ratio of Means Specification

## 1.0 Description

Ratio of means is a standard imputation method used for business
surveys. Due to its robust nature it does not use any form of trimming
or outliering.

This document specifies the type-specific aspects  for Ratio of Means
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
used for link calculations. As such it is assumed that the grouping used
groups similar contributors together whilst providing a sufficient number of
contributors within each group for robust link calculation.

## 3.0 Link Calculation Function

The link calculation function for Ratio of Means imputation for a given
period and grouping in the dataset is as follows.

For `n` matched pairs of observations for a given group and period, let:

* `x` be the responses in the target period
* `y` be the predictive variable:
  * For forward and backward links, this is the responses in the predictive
    period
  * For construction links, this is the responders' auxiliary variable for
    the target period

The general link formula for a link `l` is:

`l = (sum_(k=1)^n x_k)/(sum_(k=1)^n y_k)`

In general the observation count for a link is `n`. If `sum_(k=1)^n y_k = 0`
then `l = 1` and the observation count is 0. If `n = 0` then `l = 1` and the
observation count is null.
