# Ratio of Means Specification

## 1.0 Meta

* Support Area - Methodology - Editing & Imputation
* Support Contact - <Editing.and.Imputation.expert.group@ons.gov.uk>
* Method Theme - Imputation
* Method Classification - Ratio Imputation
* Status - Partially tested, draft (not published)

## 2.0 Terminology

* Contributor - A member of the sample; identified by a unique identifier.
* Record - A set of values for each contributor and period.
* Target Variable - The variable (A.K.A. field) in the record to be imputed.
* Responder - A contributor whos target variable contains a value (A.K.A.
    response) for a given period.
* Predictive Value - A value used as a predictor for a contributor's target
    variable.
* Predictive Record - The record containing a contributor's predictive
    value.
* Target Period - The period currently undergoing imputation.
* Predictive Period - The period containing predictive records; defined
    relative to the target period and type of imputation being performed.
* Target Record - A contributor's record in the target period.
* Grouping or group - How the data has been broken into subsets also known as
    imputation class.
* Link - A ratio used as part of the imputation process.

## 3.0 Description

Ratio of means is a standard imputation method used for business
surveys. Due to its robust nature it does not use any form of trimming
or outliering.

The method imputes for a single numeric target variable within each grouping
within the dataset and outputs a separate dataset containing the imputed target
variable and other information necessary to use the imputed variable.

## 4.0 Technical Assumptions

All assumptions below cannot be reasonably validated by the method.

This method assumes that the auxiliary variable is a good predictor of the
target variable. This method also assumes that the contributor's target
variable value in the predictive period is a good predictor of the target
variable in the target period. This same assumption is also made for values
used for link calculations. As such it is assumed that the grouping used
groups similar contributors together whilst providing a sufficient number of
contributors within each group for robust link calculation.

## 5.0 Method Input and Output

All field names in this document are not definitive; the actual field names
must be configurable and the method used to configure these names is an
implementation detail and thus out of scope of this document.

### 5.1 Input Records

Input records must include the following fields of the correct types:

* Unique Identifier - Any
* Period - String in "YYYYMM" format
* Grouping - Any
* Target Variable - Numeric - Nulls Allowed
* Auxiliary Variable - Numeric
* Forward Link (Optional) - Numeric
* Backward Link (Optional) - Numeric
* Construction Link (Optional) - Numeric

Unless otherwise noted, fields must not contain null values. All other
fields shall be ignored.

### 5.2 Output Records

Output records shall always contain the following fields with the following
types:

* Unique Identifier - Any
* Period - String in "YYYYMM" format
* Imputed Variable - Numeric
* Imputation Marker - String
* Forward Link - Numeric
* Backward Link - Numeric
* Construction Link - Numeric
* Forward Link Observation Count - Numeric
* Backward Link Observation Count - Numeric
* Construction Link Observation Count - Numeric

Fields of type "Any" shall be of the same type as the corresponding input
fields as the values shall be the same in both input and output records.

### 5.3 Back Data

In order to correctly handle the first period of data, the method must
accept a dataset containing back data. This dataset must contain the period
directly preceding the first period in the main dataset and must not appear
in the output.

Back data records shall always contain the following fields:
* Unique Identifier
* Period
* Grouping
* Imputed Variable
* Imputation Marker
* Auxiliary Variable

These fields must have the same types as their counterparts in the Input and
Output records.

## 6.0 Overall Method

The imputation method consists of a number of processes as detailed below.
The method ends only when either there are no more missing values within the
target variable or no more values can be imputed. The latter case
constitutes an error condition and will be handled according to the error
handling behaviour defined below.

The method uses a link, in combination with a predictive value, to calculate
an imputed value for a target record. This predictive value can either be
the target variable value from the contributor's predictive record or an
auxiliary variable.

The following imputation types comprise the complete method:

* Forward imputation from response
* Backward imputation
* Construction
* Forward imputation from construction

All link and imputation calculations must be performed treating each group
in the dataset separately. In addition, since all contributors must have a
populated auxiliary variable, failure to fully populate the target variable
by this method shall constitute an error.

## 7.0 Link Calculation

### 7.1 Responder Filtering

By default the method will consider all responders when calculating links.
However the method must also accept an optional expression for filtering
responders. If provided, link calculations will only consider responders
matching this filter. This filter will only apply to link calculations.

### 7.2 Pre-Calculated Links

It must also be possible to pass pre-calculated link columns to the method.
In this case all three types of links must be provided; this requirement is
to avoid any assumptions within the method as to the relationship between
provided links.

### 7.3 Responder Matching

In link calculations, only responders present in both target and predictive
periods and in the same group shall be used to calculate the ratios. The
relevant link's Observation Count field shall be set to the number of
matched responders.

### 7.4 Link Calculations

For forward and backward links, the general ratio is the sum of the target period's
responses divided by the sum of the predictive period's responses. In the
case of the forward link, the predictive period is the previous period
whereas for the backward link it is the next period. If the predictive
period is not present in the dataset, or the value of the denominator is 0,
then the link shall default to 1.

For construction links, the ratio uses the sum of the responses in the target
period divided by the sum of the responders' auxiliary values for the target
period. As above, if the denominator is 0 then the link shall default to 1.
For the purpose of this definition, the predictive period for this link
is the target period.

## 8.0 Imputation

### 8.1 Overview

Imputation uses a predictive value for a contributor and multiplies that by
the appropriate link. Both the link and predictive value used depend on the
type of imputation being performed. Imputation can only take place on
records with no value for the target variable and where the appropriate
predictive value and link are present or can be calculated. In particular,
imputation of multiple contiguous periods must be performed in the correct
order since imputations for a given contributor chain together. In all
cases, the predictive period for a type of imputation is the same as that
of the link being used.

### 8.2 Forward Imputation

In this method there are multiple types of forward imputation performed. In
all cases the forward link is used and the predictive value is the value for
the target variable for the predictive record.

#### 8.2.1 Forward Imputation From Response

In this type of imputation, only predictive records which are either
responses or forward imputes from responses can be used. Records imputed
using this imputation will be marked `FIR`.

#### 8.2.2 Forward Imputation From Construction

In this type of imputation, only predictive records which are imputes from
construction can be used. Records imputed using this imputation will be marked
`FIC`.

### 8.3 Backward Imputation

In this type of imputation, the backward link is used and the predictive value
is the value for the target variable for the predictive record. Only predictive
records which are responses can be used. Records imputed using this imputation
will be marked `BI`.

Backward imputation from construction must not occur.

### 8.4 Construction

In this type of imputation the construction link is used and the predictive
value is the auxiliary variable from the target record. Records imputed
using this imputation will be marked `C`.

## 9.0 Error Handling

In the case of errors occuring the method shall not result in any output records.
Instead a suitable error shall be emitted.

## 10.0 Calculations

In order to calculate a fully imputed target variable, one possible formulation
is as follows:

Let:

* `p` be a period
* `p_(target)` be the target period
* `p_(predictive)` be the predictive period
* `c` be a contributor record
* `f((c))` be a function which tests c against a set of conditions

The responses `r` in the dataset are found by:
`r((c)) = c if exists c_(target) and c_(period) = p and f((c)) forall c`.

The target responses `r_(target)` are found by setting `p = p_(target)` and
the predictive responses `r_(predictive)` are found by setting `p =
p_(predictive)`.

Matched pairs of responses `m_r` are found by:

```asciimath
m_r((c)) = ((r_(target), r_(predictive))) forall ((r_(target((c))), r_(predictive((c)))))
    if r_(target)_(identifier) = r_(predictive)_(identifier)
    and r_(target)_(group) = r_(predictive)_(group)
```

The link `l` for a target and predictive period is:

```asciimath
l((c)) = {
    (
        sum_(m_r((c))) m__r_(target) / sum_(m_r((c))) m__r_(predictive)
        if p_(target) != p_(predictive) and sum_(m_r((c))) m_(predictive) != 0
    ","),
    (
        1 if p_(target) != p_(predictive)
        and sum_(m_r((c))) m_(predictive) != 0
    ","),
    (
        sum_(m_r((c))) m_r_(target) / sum_(m_r((c))) m_r_(auxiliary)
        if p_(target) = p_(predictive) and sum_(m_r((c))) m_r_(auxiliary) != 0
    ","),
    (
        1 if p_(target) = p_(predictive)
        and sum_(m_r((c))) m_r_(auxiliary) != 0
    )
:}
```

Non-responders `n` can be found by `n((c)) = c if c notin r forall c`.
As above, `n_(target)` is found by `p = p_(target)` and `n_(predictive)`
is found by `p = p_(predictive)`.

Matched pairs of non-responders `m_n` are found by:

```asciimath
m_n((c)) = ((n_(target), n_(predictive))) forall ((n_(target)((c)), n_(predictive)((c))))
    if n_(target)_(identifier) = n_(predictive)_(identifier)
    and n_(target)_(group) = n_(predictive)_(group)
```

Thus, where `p_(sequence)` is a contiguous sequence of all periods covering
the full range of periods in `c` in ascending order, the variable `v`
resulting from a single type of imputation is calculated as:

```asciimath
v((c)) = {
    (m_n_(predictive)((c)) * l((c)) if p_(target) != p_(predictive) ","),
    (m_n_(auxiliary) * l((c)) if p_(target) = p_(predictive))
:} forall m_n((c)) forall ((p_(target), p_(predictive))) in p_(sequence)
```

Based on the above definition, we can define the types of imputation as:

* `v_(forward)((c)) = v((c)) "where" p_(predictive) = p_(target) - 1`
* `v_(backward)((c)) = v((c)) "where" p_(predictive) = p_(target) + 1`
* `v_(construction)((c)) = v((c)) "where" p_(predictive) = p_(target)`

Thus a completely imputed target variable `o` is:

```asciimath
o((c)) = v_(forward)((
    v_(construction)((
        v_backward((
            v_forward)((c))
        ))
    ))
))
```
