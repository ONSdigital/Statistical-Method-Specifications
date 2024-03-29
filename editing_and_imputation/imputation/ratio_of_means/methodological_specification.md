# Ratio of Means Specification

## 1.0 Meta

* Support Area - Methodology - Editing & Imputation
* Method Theme - Imputation
* Status - Partially tested - weighted imputation is not available

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
variable to inform the imputed value. Due to its robust nature, it does
not use any form of trimming or outliering.

As imputation can be carried out for multiple periods simultaneously, the
method can apply forward, backward or construction imputation. The type of
imputation used will vary for each non-respondent in each period depending
on whether data is available in the predictive period.

The generic formula for using ratio of means imputation is the imputation link
multiplied by the variable of interest (auxiliary variable in the case of
construction) for the non-respondent from a previous/consecutive/current period
for forwards/backwards/construction imputation respectively.

## 4.0 Assumptions

* The contributor's target variable value in the predictive period and the
auxiliary variable are well correlated (i.e. a good predictor) with the
target period.
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
* Target period link weight (Optional) - Numeric
* Previous year forwards imputation link (Optional) - Numeric
* Previous year backwards imputation link (Optional) - Numeric
* Previous year construction link (Optional) - Numeric
* Link Filter Columns (Optional) - This can be any number of
columns of any type that are to be used by the link filter
(7.0) to prevent responders being used in the imputation
link calculations.

Unless otherwise noted, fields must not contain null values. All other
fields shall be ignored.

Note that the predictive variable is indirectly defined as the target
variable in the predictive period.

### 5.2 Output Records

Output records shall always contain the following fields with the following
types:

* Unique Identifier - Any
* Period - String in "YYYYMM" format
* Final Target Variable - Numeric
* Imputation Marker - String
* Forward Link - Numeric
* Backward Link - Numeric
* Construction Link - Numeric
* Forward Link Observation Count - Numeric
* Backward Link Observation Count - Numeric
* Construction Link Observation Count - Numeric

Fields of type "Any" shall be of the same type as the corresponding input
fields as the values shall be the same in both input and output records.

The imputation marker must be one of the following:

* FIR = Forwards imputation from response
* FIC = Forwards imputation from construction
* BI = Backwards imputation
* C = Construction imputation from auxiliary variable
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
* Forward Link - Numeric
* Backward Link - Numeric
* Construction Link - Numeric

These fields must have the same types as their counterparts in the Input
and Output records.

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

Forward imputation imputes data for non-responders in the target period by
multiplying a link to the predictive period data, where the predictive period
is the period that immediately precedes the target period. It should be noted
that the same link is used for forwards imputation from a response and from a
construction.

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
non-responder (ie. newly sampled) then construction imputation will be applied.

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

### 8.4 Weighted imputation links

In some cases, it may be appropriate to use an imputation link which is an
average of imputation links for more than one period. In the simplest case
this could be the average of two links. Two further parameters would need
to specified: the time lag (k) and the weight (w) given to each period. In
this case the imputation link is calculated as:

```asciimath
Weighted imputation link = w*{sum y_{i, t}}/{sum x_{i, t}}
          + (1-w)*{sum y_{i, t-k}}/{sum x_{i, t-k}}
```

### 8.5 User specified imputation links

In some instances, a user may want to specify that the imputed value for a
given target variable is constructed using links that have been calculated
for another variable named by the user within a corresponding imputation
class. A user also may want to specify that a variable is backwards or
forwards imputed using links that have been calculated for another variable
named by the user within a corresponding imputation class or use a link of
1.

### 8.6 Imputation rules

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

![imputation_types1](https://user-images.githubusercontent.com/87982871/167370091-bd18e5bb-fef5-4d46-9b1e-a452040d9e16.png)

For Copyright information, please see LICENCE.
