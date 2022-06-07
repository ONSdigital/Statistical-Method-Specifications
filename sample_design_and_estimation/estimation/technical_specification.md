# Horvitz-Thompson Estimation

## 1.0 Meta

* Support Area - Methodology - Sample Design & Estimation
* Support Contact - <Sample.Design.Estimation.Business@ons.gov.uk>
* Method Theme - Estimation
* Method Classification - Estimation
* Status - Prototype, peer review, meets coding standards, unit tested

## 2.0 Terminology

* a-weight - design weight
* g-weight - calibration factor
* Birth -
* Death -

## 3.0 Description

Horvitz-Thompson is an estimation method used for business surveys.

Estimates weights for population using sample and outputs a-weight and g-weight.

## 4.0 Technical Assumptions


**Birth-death and out of scope adjustment**

Currently the birth-death adjustment’s parameter, :math:`H`, only takes
values 0 or 1.

:math:`H = 0` : assume no births. Sampled deaths will respond a real 0
and the estimate will reflect the deaths, no birth adjustment is needed
as we are assuming there are none.

:math:`H = 1` : assume births = deaths. Use of birth-death adjustment
also assumes that all deaths have been recorded correctly at the time of
Estimation. The real 0 responses from the dead businesses are reflected
in the estimate, the birth death adjustment increases the estimate
slightly to reflect births in the population. This assumption can only
be made in appropriate circumstances (e.g. if an industry were dying it
would not be appropriate to make this assumption).

Note that these parameters also affect the out of scope adjustments;
when these are used, the *H* parameter is not a pure birth-death
adjustment.

See treatment of special cases for :math:`H \neq 0` and
:math:`H \neq 1`.

**Ratio estimation**

For ratio estimation, there must be a suitably strong relationship
between the variable and the auxiliary variable – determined by
Methodology. Improper use of auxiliary data and ratio estimation will
result in poor quality estimates. In the case where there is no suitably
strong relationship between the variable and available auxiliary
variables, expansion estimation can be used.


## 5.0 Method Input and Output

