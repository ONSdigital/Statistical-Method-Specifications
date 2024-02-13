# Winsorisation

## 1.0 Meta

* Support Area - Methodology – sample design & estimation
* Method Theme - Estimation
* Status - One-sided winsorisation is implemented.
Two-sided winsorisation is out of scope.

## 2.0 Terminology

## 3.0 Introduction

In business surveys, some responses can be very large and can distort
estimates when such a business is selected. Consequently, it is sometimes
desireable to reduce the effect of these businesses. This is known as outlier
treatment. This method applies a technique known as one-sided winsorisation.
The objective of the method is to introduce a small bias, while reducing the
variance. This is intended to reduce the mean squared error of the total, a
measure of overall accuracy. The method works for stratified expansion
estimation and combined ratio estimation, both of which are commonly used
in business surveys.

The method uses a pre-calculated parameter, 'L-value' that must be supplied
to the method, to calculate a threshold
for each return.  The threshold calculated
depends upon whether expansion or ratio
estimation is used.

If a target value is deemed influential, greater than the calculated threshold, an
outlier weight is calculated which will reduce the target value to the most
extreme retained value when subsequently grossed.

Two-sided winsorisation is out of scope for this specification.

## 4.0 Assumptions

* Data has been cleaned and appropriately processed (e.g using editing and/
or imputation) prior to the running of this method.
* An appropriate L-value is computed for each target variable based on the
source data and passed into the method.
* An appropriate design-weight is provided that corresponds well with the
data being processed.
* Auxiliary and Calibration Weight values are provided that correcpond well
with the data being processed when ratio estimation is required.
* A unique reference value is provided to the method, duplicate reference
values will be treated as an error.
* Values from the period to be treated are organised into groups using
appropriate characteristics (e.g for business surveys, data from the same
industry sector, area or size may be considered a group)

## 5.0 Method Input and Output

### 5.1 Input Records

Input records must include the following fields of the correct types:

* Reference - Any - a unique identifier, e.g Business Reporting Unit
* Period - String - "YYYY" or "YYYYMM" format, the period to be wisorised
* Cell or Group - Numeric - a number representing a stratum (e.g Standard
Industrial Classification)
* Target Variable - Numeric - Nulls allowed (Check?), the value to be treated
* Design Weight - Numeric - Nulls allowed (Check?), a supplied weight that
reflects the sampling design
* Calibration Weight - Numeric - Nulls allowed, a weight that maintains the
estimated calibration totals. Mandatory for ratio estimation.
* Auxiliary - Numeric - Nulls allowed, a secondary value used for prediction
in ratio estimation. Mandatory for ratio estimation.

The presence of Auxiliary and Calibration Weight indicates that ratio
estimation must be performed rather than expansion estimation.

### 5.2 Output Records

Output records must include the the following fields of the correct types:

* Reference - Any - a unique identifier, e.g Business Reporting Unit
* Period - String - "YYYY" or "YYYYMM" format, the period to be wisorised
* Outlier Weight - Numeric - A value between 0 and 1 which reflects the
reduction in a target variable due to winsorisation
* Marker - String - Indicates the result of the winsorisation method for
each target value
  * **W** - Winsorised. Marked against any value that could be winsorised
  * **NW_FE** - Not Winsorised Fully Enumerated. Marked when design weight is
  1 for expansion estimation.
  * **NW_AG** - Not Winsorised a*g. Marked
  in ratio estimation when the product of
  design weight and calibration weight is < 1.

## 6.0 Method

### 6.1 Overall Method

The winsorisation method performs the following steps to find outliers and
calculate outlier weights based on either expansion or ratio estimation.

#### Parameter Validation

Input parameters are validated to ensure that there are no missing values and that
each reference is unique. Non-unique references will cause the method to error.

#### Assess Validity for Winsorisation

Target values must be checked to determine if they can be marked as not winsorised,
fully enumerated (NW_FE):

* A target value with a corresponding design weight of 1 is marked as NW_FE and
given an outlier weight of 1. This should
occur regardless of whether:
  * Other target values in the cell or group have a design weight not equal to 1.
  * Expansion or Ratio estimation were to be performed.

Target values considered for ratio estimation (when
auxiliary and calibration weight are present) must
be checked to determine their eligibility for
winsorisation. This assessment is based on the
product of the design weight and calibration weight (NW_AG).

* If design weight multiplied by calibration weight
is less than or equal to 1 the target value is not
winsorised, the associated oultier weight is set to
1 and the marker set as NW_AG.

* _Do we need the caveat in the original for design
weight = 1 && calibration != 1 and design weight
multiplied by calibration weight is < 1 ?_ It seems
this is already captured by the above constraints?

Any target value that has been marked as NW_FE or
NW_AG must be removed from winsorisation calculations

Target values that are not marked as NW_FE or NW_AG
are winsorised, the outlier weights calculated and
marked as W.

#### Determine Estimation Method

This method employs two types of estimation based
upon the available inputs associated with the traget
values under consideration.

##### Expansion Estimation

If calibration weight and auxiliary values **are
not** supplied then **expansion estimation** is used.

For expansion estimation a _weight_ is calculated from the input design weight.

The _mean target value_ is calculated for those target values that are in the same
period and group and are to be considered for winsorisation.

An estimation _threshold_ is then calculated using the _mean target value_,
L-value and weight.
This threshold is used to determine whether the target value is considered an outlier.

If the target value exceeds the _threshold_, then a modified value of the target
is calculated using the original value, _weight_ and _threshold_.

If the target value is less than or equal to the _threshold_
the target value remains unchanged.

An _outlier weight_ is calculated by using the original target value divided by the
_modified target value_. In the case of a target value that was not modified the
_outlier weight_ will be 1.

Finally, regardless of whether the target value was modified or not the marker will
be set to indicate the value was considered for winsorisation ("W")

##### Ratio Estimation

If calibration weight and auxiliary values **are**
supplied then **ratio estimation** is used.

For ratio estimation a _weight_ is calculated from the product of the supplied
calibration weight and the design weight.

The product of the supplied design weight with the target value is used to calculate
a _weighted target value_.

A _weighted auxiliary value_ is calculated using the product of the design weight
and the supplied auxiliary value.

The sum of the _weighted target values_ divided by the sum of the _weighted
auxiliary values_ are used to calculate a
_weighted ratio_.

This _weighted ratio_ is then used to calculate a _predict unit value_ associated
with the target value by multiplting the provided auxiliary by the computed
_weighted ratio_.

A _threshold_ is then calculated based on this _predict unit value_, supplied L-value
and the _weight_ that was calculated from the calibration weight and design weight.

If the target value exceeds the _threshold_, then a modified value of the
target is calculated using the original value, _weight_ and _threshold_.

If the target value is less than or equal to the _threshold_
the target value remains unchanged.

An _outlier weight_ is calculated by using the original target value divided by the
_modified target value_. In the case of a target value that was not modified the
_outlier weight_ will be 1.

Finally, regardless of whether the target value was modified or not the marker will
be set to indicate the value was considered for winsorisation ("W")

## 7.0 Calculations

**$i$** - an index denoting a single observation / business / row

**$h$** - an index denoting a stratum (is this equivalent to group?)

**$j$** - an index denoting a calibration group (a collection of strata)

**$y_i$** - a target value.  This is an obsereved value for a given variable for
a given business.

**$n_h$** or **$n_j$** - the sample sizefor stratum $h$ or calibration group $j$.
These values are inputs to the calculation of the _design weight_ that is passed
to Winsorisation.  
**$N_h$** or **$N_j$** - the population size for stratum $h$ or calibration group
$j$. These values are inputs to the calculation of the _design weight_ that is
passed to Winsorisation.

**$w_i$** is the _weight_ which, for business $i$, is calculated as:
$w_i = a_i$ when **expansion estimation** is used
$w_i = a_i g_i$ when **ratio expansion** is used

**$L$** is the Winsorisation parameter supplied to the method - in ONS this is
calculated from historic data using a bespoke method based on the survey
requirements. Calculation of the L value is
beyond the scope of this specification.

**$a_i$** is the _design weight_ that is supplied to Winsorisation. The calculation
 of this is outside the scope of the
Winsorisation method itself but an example of the calculations used in ONS are:

$a_i = a_h = \frac{N_{h}}{n_{h}}$, e.g the birth death adjusted design weight

$a_i = a_h = \ \frac{N_{h}}{n_{h}}\left(1 + H_{h}\left( \frac{d_{h}}{n_{h} - d_{h}}
 \right) \right)$, business $i \in$ stratum $h$, chosen during estimation

**$g_i$** is the _calibration weight_ that is supplied to Winsorisation. The
calculation of this is outside the scope of the Winsorisation method itself but
an example of the calculation for calibration weight for business $i \in$
calibration group $j$ used in ONS is:

$g_{i} = g_{j} = \ \frac{\sum_{i = 1}^{N_{j}}x_{i}}{\sum_{i = 1}^{n_{j}}{a_{i}x_{i}}}
$ where $a_i = \frac{N_{h}}{n_{h}}$

(regardless of whether birth death adjusted design weight was chosen during estimation)

**${\overline{y}_h} = \frac{\sum_{i = 1}^{n_{h}}y_{i}}{n_{h}}$** is the
_mean target value_, the sample mean for
stratum $h$ for the period to be Winsorised

**$x_i$** is the _auxiliary variable_ for a given sampled _target value_

**${\widehat{\mu}}_{i}$** is the _predict unit value_ used in **ratio estimation**
and is calculated using the data to be Winsorised for each observation and the
sum of the _weighted target values_ divided by the sum of the _weighted auxiliary
values_ for each calibration group $j$ multiplied by the supplied auxiliary value

${\widehat{\mu}}_{i} = x_{i}\frac{\sum_{i = 1}^{n_{j}}{a_{i}y_{i}}}{\sum_{i = 1}^{n_{j}}{a_{i}x_{i}}}$

### Winsorisation

**$o_i$** Once a target value has been identified for Winsorisation the outlier
weight is calculated as:

$o_{i} = \left\{ \begin{matrix}
1 & \text{if }y_{i} \text{is not an outlier} \\
\frac{y_{i}^{*}}{y_{i}} = \frac{\text{modified y}}{\text{original y}} & \text
{if }y_{i} \text{is an outlier} \\
\end{matrix} \right.$

Where

${y_{i}}^{*} = \left\{ \begin{matrix}
\text{y}_{\text{i }}\text{if }y_{i} \text{is not an outlier,}   i.e.  y_{i} \leq
 k_{i}\text{} \\
\frac{1}{w_{i}}y_{i} + \left( 1 - \frac{1}{w_{i}} \right)k_{i}\text{}\text{if }
y_{i} \text{is an outlier,}   i.e. y_{i} > k_{i}  \\
\  \\
\end{matrix} \right.$

and where $k_i$, the _threshold_ for Winsorisation is defined in the following sections.

### Threshold Calculation

The threshold calculation for Winsorisation is different based on whether
**expansion estimation** or **ratio estimation** is used.

#### Expansion Estimation

The threshold for a unit, $i$, in a given stratum, $h$ is:
$$k_{i} = k_{h} = \overline{y_{h}} + \frac{L}{a_{i} - 1}$$

Apart from parameter $L$, which is calculated with a bespoke
method using historic data, this is calculated using the data from the
period to be Winsorised. Because $a_{i} = \ a_{h}$, all businesses
in a given stratum will have the same threshold $k_{h}$

#### Ratio Estimation

The unique threshold for a unit, $i$, is:
$$k_{i} = \ {\widehat{\mu}}_{i} + \frac{L}{a_{i}g_{i} - 1}$$
Apart from parameter $L$, which is calculated with a bespoke
method using historic data, this is calculated using the data from the
period to be Winsorised.
