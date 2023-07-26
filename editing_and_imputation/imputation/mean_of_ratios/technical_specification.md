# Mean of Ratios Specification

## 1.0 Description

Mean of Ratios imputation is a standard imputation method for business
surveys. The method imputes a single numeric (target) variable using the
relationship between the target variable and an appropriate predictive or
auxiliary variable. Since the method can be influenced by extreme values,
the method supports a form of trimming.

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

## 3.0 Additional outputs

In addition to the fields specified for the general method, the following
fields of the following types will be present in output records:

* Forward Growth Ratio – Numeric - Nulls Allowed
* Backward Growth Ratio – Numeric - Nulls Allowed

Optionally if trimming occurs there will also be:

* Trim Inclusion Forward - Boolean - Nulls Allowed
* Trim Inclusion Backward - Boolean - Nulls Allowed

## 4.0 Link Calculation Function

The processes specified in this section comprise the complete link
calculation function for Mean of Ratios imputation.

### 4.1 Zero Filtering

By default response values of `0` will be filtered in addition to any other
responder filtering. It must be possible to disable this filter.

### 4.2 Growth Ratio Calculation

Growth ratios must be calculated for all matched pairs per period and
grouping in the dataset. For `n` matched pairs in a given period and
grouping, let:

* `x` be the responses in the target period
* `y` be the responses in the predictive period

Thus the set of growth ratios `r` is:
`r = (x_k/y_k)_(k=1)^n`

If `y_k = 0` then `r_k = 1`.
Construction links are not calculated using growth ratios.

### 4.3 Trimming

Trimming must only be applied if `n` is above a configurable threshold.

To apply trimming two cut off boundaries must be calculated as follows:

let:

* `t_"upper"` be the upper percentage
* `t_"lower"` be the lower percentage

then the cut off boundaries `b_"upper"` and `b_(lower)` are:

```asciimath
b_(lower) = ceil(n*t_(lower)/100)
b_(upper) = 1+floor(n*(1-(t_(upper)/100)))
```

Thus with ratios in `r` in ascending order, The trimmed set of growth ratios
`g` is given by `g = (r_k)_(k=b_(lower))^b_(upper)` and after trimming `n` is
set as the number of elements in the resulting set.

### 4.4 Calculation Function

Given the above, the formula for a link `l` is:

For forward or backward links `l = (sum g)/n` (that is the
arithmetic mean) and the link's observation count is set to `n`. If `g` is
empty then `l = 1` and the observation count is set to null.

For construction:

let:

* `m` be the number of responders in the target period
* `a` be the responders' auxiliary variable

`l = (sum x)/(sum a)`

If `m = 0` then `l = 1` and the observation count is null. If
`(sum a) = 0` then `l = 1` and the observation count is set to 0.

For Copyright information, please see LICENCE.
