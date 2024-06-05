# Ratio of Means Specification

## 1.0 Meta

* Support Area - Methodology - Editing & Imputation
* Method Theme - Imputation
* Status - Fully tested

## 2.0 Terminology

* Contributor - A member of the sample; identified by a unique identifier.
* Record - A set of values for each contributor and period.
* Target Period - The period which requires imputation to be applied.
* Target Variable - The variable of interest that requires data values
    to be imputed.
* Target Record - A contributor's record in the target period.
* Imputation Class - The variable used to group the data for the calculation of
    imputation links.
* Predictive Value - A value used as a predictor for a contributor's target
    variable.
* Predictive Record - The record containing a contributor's predictive
    value.
* Predictive Period - The period containing predictive records; defined
    relative to the target period.
* Auxiliary variable - The variable used as a predictor for a contributor's
    target variable, where the predictive value is not available (i.e. where
    the contributor was not sampled in the predictive period).
* Responder - A contributor who has responded to the survey within a given
    period.
* Link - A ratio used as part of the imputation process. Commonly known as
    the imputation link.
* Previous year link - This is the imputation link for the previous year
for a given contributor. This should be input into the method if the user
wishes to apply weighted imputation.
* Target period link weight -  If using weighted imputation, this value
represents the weight of the target period imputation link as a proportion
of 1. If = 1 or missing, then weighted imputation is not applied.
* Matched Pair - A contributor that has returned, cleaned values in
both the target and predictive period.

## 3.0 Introduction

Ratio of means is a standard imputation method used for business
surveys. It can be used to impute value for unit (complete) non-response or
item (partial) non-response. The method imputes for each non-responding
contributor a single numeric target variable within the dataset for
multiple periods simultaneously. It uses the relationship between the
target variable of interest and a predictive value and/or auxiliary
variable to inform the imputed value. Typically, the predictive variable
can be:

* a previous value from the variable of interest for the non-responder,
  used for forwards imputation.
* a consecutive value from the variable of interest for the non-responder,
  used for backwards imputation.

Typically, the auxiliary variable can be:

* a known register based variable for the non-responder that is well
  correlated with the variable of interest, used for construction
  imputation.

As imputation can be carried out for multiple periods simultaneously, the
method can apply forward, backward or construction imputation. The type of
imputation used will vary for each non-respondent in each period depending
on whether data is available in the predictive period.

The generic formula for using ratio of means imputation is the imputation link
multiplied by the variable of interest (auxiliary variable in the case of
construction) for the non-respondent from a previous/consecutive/current period
for forwards/backwards/construction imputation respectively.

Due to its robust nature, it does not use any form of trimming or outliering.

## 4.0 Assumptions

* The contributor's target variable value in the predictive period and the
auxiliary variable are well correlated (i.e. a good predictor) with the
target period.
* The auxiliary variable is populated by the user for every contributor when
performing construction imputation.
* The imputation classes group similar contributors together whilst providing
a sufficient number of contributors within each class enabling a robust
link calculation.
* Matched pairs must comprise of clean, respondent data for both
the target and predictive period.

## 5.0 Method Input and Output

All field names in this document are not definitive; the actual field names
must be configurable, and the method used to configure these names is an
implementation detail and thus out of scope of this document.

### 5.1 Input Records

Input records must include the following fields of the correct types:

* Unique Identifier - Any
* Period - String in "YYYYMM" format
* Imputation Class - Any
* Target Variable - Numeric - Nulls Allowed
* Auxiliary Variable - Numeric
* Forward Link (Optional) - Numeric
* Backward Link (Optional) - Numeric
* Construction Link (Optional) - Numeric
* Manually Constructed Value – Numeric – Optional – Nulls
 allowed – This column is used at target variable level to
 input manually constructed values into the method
 (section 8.4 and 8.8)
* Link Filter Columns (Optional) - This can be any number of
columns of any type that are to be used by the link filter
(7.1) to prevent responders being used in the imputation
link calculations.

Unless otherwise noted, fields must not contain null values. All other
fields shall be ignored.

Note that the predictive variable is indirectly defined as the target
variable in the predictive period.

There are additional parameters, separate to the inputs, that the user
must populate. These are:

* Periodicity to inform the method of the appropriate time lags for the
  growth factor calculations and for weighted imputation.
* Whether zeros will be included when finding valid matched pairs for
  forward and backward growth factor calculations.
* Target period link weight must be specified to implement weight
  imputation links.
* If using weighted imputation, time lag, *k*, informs which previous
  period imputation links should be used in addition to the target period
  links.

### 5.2 Output Records

Output records shall always contain the following fields with the following
types:

* Unique Identifier - Any
* Period - String in "YYYYMM" format
* Imputation Class - Any
* Final Target Variable - Numeric
* Imputation Marker - String
* Forward Link - Numeric
* Backward Link - Numeric
* Construction Link - Numeric
* Forward Link Observation Count - Numeric
* Backward Link Observation Count - Numeric
* Construction Link Observation Count - Numeric
* Forward Link Default Marker – Boolean
* Backward Link Default Marker – Boolean
* Construction Link Default Marker – Boolean

If Responder Filtering is performed, then output records should also contain:

* Post Filter Inclusion Marker Previous – Boolean – Nulls Allowed
* Post Filter Inclusion Marker Target – Boolean – Nulls Allowed
* Post Filter Inclusion Marker Next – Boolean – Nulls Allowed

If weighted imputation is performed, then the output record should also
contain:

* Unweighted Forward Link - Numeric - Optional - Nulls Allowed
* Unweighted Backward Link - Numeric - Optional - Nulls Allowed
* Unweighted Construction Link - Numeric - Optional - Nulls Allowed

Fields of type "Any" shall be of the same type as the corresponding input
fields as the values shall be the same in both input and output records.

Output records labelled 'Optional' shall only be populated in the ouput
dataset if the corresponding functionality was used e.g., weighting.

The imputation marker must be one of the following:

* FIR = Forwards imputation from response
* FIC = Forwards imputation from construction
* FIMC = Forwards imputation from manual construction
* BI = Backwards imputation
* C = Construction imputation from auxiliary variable
* MC = Manual construction
* R = Response. This value is cleared of errors or warnings

### 5.3 Back Data

In order to correctly handle the first period of data, the method must
accept a dataset containing back data. This dataset must contain the period
directly preceding the first period in the main dataset. This data shall be
the result of a prior imputation run and must not appear in the output.

Back data records shall always contain the following fields:

* Unique Identifier - Any e.g. Business Reporting Unit
* Period - String in "YYYYMM" format
* Imputation Class
* Final Target Variable - Numeric
* Imputation Marker - String

These fields must have the same types as their counterparts in the Input
and Output records.

If weighted imputation is being implemented, then the back data should
include the necessary periods for the specified time lag (e.g., the
previous year) to allow this.

The back data for weighting should also contain:

* Unweighted Forward Link - Numeric
* Unweighted Backward Link - Numeric
* Unweighted Construction Link - Numeric

These fields must have the same types as their weighted counterparts in
the Output records.

## 6.0 Method

### 6.1 Overall method

The imputation method consists of a number of processes as detailed below.
The method ends only when either there are no more missing values within the
target variable or no more values can be imputed. The latter case
constitutes an error condition and will be handled according to the error
handling behaviour defined below.

For each non-responding contributor, the method calculates a link, which
corresponds to the imputation class the non-responder sits within and
is then multiplied by the predictive value, to calculate an imputed value for
a target record. This predictive value can either be the target variable value
from the contributor's predictive record (if available) or an auxiliary
variable. When the imputation link is multiplied by the predictive record, the
predictive record can take any response type (i.e. response, imputed or
constructed value).

The following imputation types comprise the complete method:

* Forward imputation from response
* Backward imputation
* Construction
* Forward imputation from construction
* Forward imputation from a manual construction

Forward imputation imputes data for non-responders in the target period by
multiplying a link to the predictive period data, where the predictive period
is the period that immediately precedes the target period. It should be noted
that the same link is used for forwards imputation from a response, from a
construction and from a manual construction.

Backwards imputation imputes data for non-responders in the target period by
multiplying a link to the predictive period data, where the predictive period
is the period that immediately follows the target period.

Construction imputation imputes data for non-responders in the target period
where no data is available in the predictive period and therefore, an auxiliary
variable is used which relates to the target period and is then multiplied by a
link to create a constructed value.

All link and imputation calculations must be performed treating each imputation
class in the dataset separately. In addition, since all contributors
must have a populated auxiliary variable, failure to fully populate the target
variable by this method shall constitute an error.

Typically, a register-based variable such as frozen turnover or frozen
employment would be used as a contributor's auxiliary variable.

## 7.0 Link Calculation

### 7.1 Responder Filtering

When calculating imputation links, it is essential that matched pairs of
clean respondent data (data that are not in error) are used. More specifically,
only clean respondents that are present in both the target and predictive
periods should be used. In the case of construction imputation, only clean
respondent present in the target period and corresponding auxiliary
variable should be used.

By default, the method will consider all responders when calculating links.
However, the method must also accept an optional expression for filtering
responders as there are specific requirements for the data that should be used
when calculating particular links. If provided, link calculations will only
consider responders matching this filter (commonly known as matched pairs).
This filter will only apply to link calculations.

Respondent filtering should only be used to remove extreme or influential
responses. Respondent filtering should be seen as a last resort.

### 7.2 Pre-Calculated Links

It must also be possible to pass pre-calculated link columns to the method.
In this case all three types of links must be provided; this requirement is
to avoid any assumptions within the method as to the relationship between
provided links.

### 7.3 Responder Matching

For forward and backward link calculations, only contributors that have
responded in both the target and predictive period and are in the same
imputation class for both periods shall be used to calculate the ratios.

For construction link calculations, only contributors that have responded in
the target period and have an available auxiliary variable shall be used to
calculate the ratio.

### 7.4 Link Calculations

For forward and backward links, within each imputation class, the ratio is
the sum of the target period's responders divided by the sum of the predictive
period's responders. Note that contributors must have responded in both period
(matched pairs) and in the same imputation class for both periods. In the
case of the forward link, the predictive period is the previous period whereas
for the backward link it is the next period. If the predictive period is not
present in the dataset, or the value of the denominator is 0 then the link
shall default to 1.

```asciimath
Forwards imputation link = {sum x_{i, t}}/{sum x_{i, t-1}}
```

```asciimath
Backwards imputation link = {sum x_{i, t}}/{sum x_{i, t+1}}
```

For construction links, within each imputation class, the ratio uses the sum
of the responses in the target period divided by the sum of the responders'
auxiliary values for the target period. As above, if the denominator is 0
then the link shall default to 1. For the purpose of this definition, the
predictive period for this link is the target period.

```asciimath
Construction imputation link = {sum x_{i, t}}/{sum y_{i, t}}
```

### 7.5 Error Handling

In the case of errors occurring the method shall not result in any output records.
Instead, a suitable error shall be emitted.

### 7.6 Defaulted links

#### 7.6.1 Unable to calculate links

When the imputation link cannot be calculated the imputation link will be
defaulted to 1. To distinguish that this link cannot be calculated the matched
pairs will be missing.

#### 7.6.2 Zero in link calculations

When the denominator of the imputation link calculation is 0 the link is
defaulted to 1. To distinguish this link from the above default the matched
pairs will be set to 0.

## 8.0 Imputation

Imputation uses a predictive value for a contributor and multiplies that by
the appropriate link. Both the link and predictive value used depend on the
type of imputation being performed. Imputation can only take place on
records with no value for the target variable and where the appropriate
predictive value and link are present or can be calculated. In particular,
imputation of multiple contiguous periods must be performed in the correct
order since imputations for a given contributor chain together. In all
cases, the predictive period for a type of imputation is the same as that
of the link being used.

For a given period and non-responder, the method will aim to apply forwards
imputation wherever possible. If this is not feasible, backward imputation will
be used if applicable. If a predictive record does not exist for the
non-responder (i.e. newly sampled) then construction imputation will be applied.

Note that when imputation is not required for a given contributor (i.e.
responder) then the output imputation marker is set to R.

### 8.1 Forward Imputation

Forward imputation imputes data for non-responders in the target period by
multiplying a link to the predictive data, where the predictive period is the
peiod that immediately precedes the target period. It should be noted that the
same link is used for forwards imputation from a response and from a
construction.

#### 8.1.1 Forward Imputation from Response

For forwards imputation from response, the predictive value must either be a
responded value or forward imputed values from a response. Records imputed
using this imputation will be marked FIR.

#### 8.1.2 Forward Imputation from Construction

For forwards imputation from construction, the predictive value must only
come from imputed values from construction imputation or forward imputed
values from a construction. Records imputed using this imputation will be
marked FIC.

### 8.2 Backward Imputation

Backward imputation imputes data for non-responders in the target period by
multiplying a link to the predictive period data, where the predictive
period is the period that immediately follows the target period (i.e.
consecutive period). Backward imputation from construction must not occur.
Records imputed using this imputation will be marked BI.

### 8.3 Construction

Construction imputation imputes data for non-responders in the target
period where no data is available in the predictive period and therefore,
an auxiliary variable is used from the target period and is then
multiplied by a link to create a constructed value. Records imputed using
this imputation will be marked C.

### 8.4 Manual Construction Imputation

Although not part of the statistical method itself, the method must accept
and appropriately handle cases where a value has been overridden by manual
intervention. Using subject matter knowledge, the values are calculated
externally to the method and then manually input. Records using this
approach will be mark MC.

Please see section 8.8 for more details.

#### 8.4.1 Forward Imputation from Manual Construction

For forward imputation from manual construction, the predictve value must
only come from manually constructed values or forward imputed values from
a manual construction. Records using this imputation will be marked FIMC.

### 8.5 Weighted imputation links

In some cases, it may be appropriate to use an imputation link which is an
average of imputation links for more than one period. In the simplest case
this could be the average of two links. Two further parameters would need
to specified: the time lag (k) and the weight (w) given to each period. In
this case the imputation link is calculated as:

```asciimath
Weighted imputation link = w*{sum y_{i, t}}/{sum x_{i, t}}
          + (1-w)*{sum y_{i, t-k}}/{sum x_{i, t-k}}
```

### 8.6 User specified imputation links

In some instances, a user may want to specify that the imputed value for a
given target variable is constructed using links that have been calculated
for another variable named by the user within a corresponding imputation
class. A user also may want to specify that a variable is backwards or
forwards imputed using links that have been calculated for another variable
named by the user within a corresponding imputation class or use a link of
1.

### 8.7 Imputation rules

Ratio of means imputation follows a set of rules to ensure that it is used
correctly, these rules are in the same order as the flow chart below:

* If there is no response available and the contributor is being sampled for
    the first time, calculate a constructed link using the auxiliary data
    for that contributor (C).
* If there is no response available for a second period, forwards impute
    based off the constructed value (FIC).
* If a response is available for the previous period but not the current
    period, then perform forwards imputation (FIR).
* If a clean response is available for the second period but not the third,
    fourth or current, then perform rolling forwards imputation from the
    second period for all missing periods (FIR).
* If a respondent doesn't respond for the first two periods it is sampled (see
    bullet point 2) however does respond for the third period and the response
    is clean, then overwrite periods 1 and 2 with backwards imputation (BI).
* You can forwards and backwards impute off of the same respondent. However,
    a forwards impute will always be preferred to a backwards impute.
* If a respondent responds for all periods, then imputation is not needed.
* If a contributor is rotated out of the sample and then rotated back into the
    sample, values that were previously present should not be used to
    forwards impute.
* If a contributor is rotated out of sample and then rotated back into the
    sample, the response from the current period should not be used to
    backwards impute.
* If a contributor's imputation class in the previous period is different
    from its imputation class in the current period (i.e. the imputation
    class has changed), then perform construction imputation.
* If auxiliary data is missing and the contributor is a non-responder for
    all sampled periods then an error will occur.
* Mean or median imputation should be used if there is no auxiliary
    information available (no previous period auxiliary, no consecutive
    period auxiliary and no register-based auxiliary variable). Mean and
    median imputation are not available in this method.

  * Mean imputation: impute a mean value for non-responders based upon
     target period cleared respondents within the same imputation class.

  * Median imputation: impute a median value for non-responders based
     upon target period cleared respondents within the same imputation class.

Please see the image below for further information.

![imputation_types4](https://github.com/ONSdigital/Statistical-Method-Specifications/assets/87982871/eafd5663-8870-4c03-9819-125d1ff38fc4)


### 8.8 Manual Construction Rules

#### 8.8.1 Rules

* Only a return or another manual construction can override a manual
  construction (MC).
* A manual construction can override the following types: constructions,
  forwards imputes from constructions, forward imputes from returns and
  backwards imputes.
* A manual construction can be used as the predictive value when carrying
  out forward imputation (FIMC).
* A forward impute from a manual construction can be used as the
  predictive value when carrying out rolling forward imputation for multiple
  periods of non-response (FIMC).
* A manual construction cannot be used as the predictive value when carrying
  out backward imputation.
* A return can override a forward impute from a manual construction.
* A foward impute from a return can override a forward impute from a manual
  construction.
* A backward impute can override a forward impute from a manual construction.

#### 8.8.2 Scenarios

These scenarios are in the same order as the flow chart above in section 8.7:

* If a manual construction (MC) is currently held for the contributor and then
a responder (R) is returned for the same period, then the returned value should
override the manual construction. Final response pattern: R.
* If the previous period value is a manual construction (MC) and the contributor
is a non-responder for the current period, then forward impute from a manual
construction (FIMC). Final response pattern: MC, FIMC.
* If a manual construction is available for the first period but not the second,
third or current, then perform rolling forwards imputation from a manual
construction from the first period for all missing periods (FIMC). Final
response pattern: MC, FIMC, FIMC, FIMC.
* If a manual construction is held for the contributor in the first period
and rolling forward imputation from a manual construction in the second and third,
then the contributor responded in the fourth period, then override FIMC with BI.
Final response pattern: MC, BI, BI, R.
* If a manual construction is held for the contributor in the first period, and
a response is returned for the second, then do not override the manual construction
with a backward impute. Final response pattern: MC, R.
* If a manual construction is held for the contributor in the first period, and a
forward impute from a manual construction in the second, then the contributor
responded in the third period, then only override the forward impute from a manual
construction in the second period and backward impute. Final response pattern: MC,
BI, R.
* If a manual construction is held for the contributor in the first period, and a
forward impute from a manual construction in the second, then a response is returned
for the second period, then the returned value should override the forward impute
from a manual construction. Final response pattern: MC, R.
* If a manual construction is held for the contributor in the first period, and
rolling forward imputation from a manual construction was performed for the second
and third periods, and then a response is returned for the second period, then the
returned value should override the forward impute in the second period and forwards
imputation from a response should override the third period. Final response pattern:
MC, R, FIR.
* If a value of any type is held in the first period, then manual construction is
input for the second period, then do not backward impute from the manual construction.

For Copyright information, please see LICENCE.
