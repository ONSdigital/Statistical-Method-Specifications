# Ratio of Means Specification

## Meta

* Support Area - Methodology - Editing & Imputation
* Support Contact - <Editing.and.Imputation.expert.group@ons.gov.uk>
* Method Theme - Imputation
* Method Classification - Ratio Imputation
* Status - Partially tested, draft (not published)

## Terminology

* Target variable - The variable of interest that the method
    is working on.
* Group - How the data has been broken into subsets. Also know as Imputation
    Class.
* Contributor - A member of the sample, identified by a unique identifier.
* Periodicity - the length of a period within the dataset
* Record - A set of values for each contributor and period
* Target period - The period currently undergoing imputation.
* Predictive period - The period directly preceeding or succeeding the
    target period with respect to the periodocity of the dataset.
* Target record - A contributor's record in the target period.
* Predictive record - A contributor's record in the predictive period.
* Responder - A contributor who has responded to the survey within a given
    period.
* link - A ratio used as part of the imputation process.

## Introduction

Ratio of means is a standard imputation method used for business
surveys. Due to its robust nature it does not use any form of trimming
or outliering.

The method imputes for a single numeric target variable within each group within the
dataset and outputs a separate dataset containing the imputed target variable and
other information necessary to use the imputed variable.

## Overall Method

The imputation method consists of a number of processes as detailed below.
The method ends only when either there are no more missing values within the
target variable or no more values can be imputed. The latter case
constitutes an error condition and will be handled according to the error
handling behaviour defined below.

The method uses a link, in combination with a predictive value, to calculate
an imputed value for a target record. This predictive value can either be
the target variable value from the contributor's predictive record or an
auxiliary variable.

The following imputation processes comprise the complete method:

* Forward imputation from response
* Backward imputation
* Construction
* Forward imputation from construction

All link and imputation calculations must be performed treating each group
in the dataset separately.

### Link Calculation

#### Responder filtering

By default the method will consider all responders when calculating links. However the method must
also accept an optional expression for filtering responders. If provided,
link calculations will only consider responders matching this filter. This
filter will only apply to link calculations.

#### Formulae

The following formulae shall be used to calculate links:

```text
p = A period
p_target = the target period
p_predictive = the predictive period
D = The dataset under consideration
responses(p, D) = [ [ c for c in D ], exists(c[target]) and c[period] == p]
R_target = responses(p_target, D)
R_predictive = responses(p_predictive, D)
matched_responses(p_target, p_predictive) = [
    r_pair(r_target, r_predictive), for r in (R_target, R_predictive),
    identical(r_pair[identifier]) and identical(r_pair[group])
]
link(p_target) = [
    sum[ r for r in matched_responses(p_target, p_predictive) ] r_target
    / sum[ r for r in matched_responses(p_target, p_predictive) ] r_predictive,
    p_target <> p_predictive;
    sum[ r for r in responses(p_target)] r_target
    / sum[ r for r in responses(p_target)] r_auxiliary,
    p_target == p_predictive
]
```

#### Pre-calculated links

It must also be possible to pass pre-calculated link columns to the method.
In this case all three types of links must be provided; this requirement is
to avoid any assumptions within the method as to the relationship between
provided links.

### Imputation

Using the link definitions, The general imputation formula is:

```text
nonresponders(p, D) = [ [ c for c in D ], not exists(c[target]) c[period] == p]
N_target = nonresponders(p_target, D)
N_predictive = nonresponders(p_predictive, D)
matched_nonresponders(p_target, p_predictive) = [
    n_pair(n_target, n_predictive) for n in (N_target, N_predictive),
    identical(n_pair[identifier]) and identical(n_pair[group])
]
impute(p_target, p_predictive) = [
    [ m for n in matched_nonresponders(p_target, p_predictive) ]
    m_predictive[target] * link(p_target, p_predictive),
    p_target <> p_predictive;
    [ n for n in nonresponders(p_target) ]
    n[auxiliary] * link(p_target, p_predictive),
    p_target == p_predictive
]
impute_forward(p_target) = [ impute(p_target), p_predictive = p - 1 ]
impute_backward(p_target) = [ impute(p_target), p_predictive = p + 1 ]
impute_construction(p_target) = [ impute(p_target), p_predictive = p ]
```

##### Forward imputation from response

In this type of imputation, only predictive records which are either
responses or forward imputes from responses can be used. Records imputed
using this imputation will be marked `FIR`.

##### Forward imputation from construction

In this type of imputation, only predictive records which are imputes from
construction can be used. Records imputed using this imputation will be marked
`FIC`.

##### Backward imputation

In this type of imputation, only predictive records which are responses can
be used. Records imputed using this imputation will be marked `BI`.

Backward imputation from construction must not occur.

#### Construction

In scenarios where neither forward or backward imputation can be performed
due to a lack of a predictive record or link for a contributor, the method
will use the following predictive value definition to perform construction
imputation.

This type of imputation must only be performed where the target record has a
value for its auxiliary variable. Records imputed using this imputation will
be marked `C`.

## Back data

In order to correctly handle the first period of data, the method must
accept a dataset containing back data. This dataset must contain the period
directly preceeding the first period in the main dataset. This back data
must not appear in the output.

## Technical Information
1. Unique Identifier - String
2. Period - String
3. Strata - String
4. Target Variable - Numeric
5. Auxiliary Variable - Numeric
6. Forward Link - Numeric (Optional)
7. Backward Link - Numeric (Optional)
8. Construction Link - Numeric (Optional)

The method will always return the following data:

1. Unique Identifier - String
2. Period - String
3. Returned/Imputed Target Variable - Numeric
4. Imputation Marker - String
5. Forward Link - Numeric
6. Backward Link - Numeric
7. Construction Link - Numeric

## Error Handling

In the case of errors occuring the method must not emit any output records.
In addition a suitable error must be emitted.

## Assumptions

This method assumes that the auxiliary variable is a good predictor of the
target variable. This method also assumes that the contributor's target
variable value in the predictive period is a good predictor of the target
variable in the target period. This same assumption is also made for matched
pairs' target variable values.

## Notes

Typically a register-based
variable such as frozen turnover or frozen employment would be used as a
contributor's auxiliary variable.

## References

**De Waal, T., Pannekoek, J. and Scholtus, S.** (2011) Handbook of Data
Editing and Imputation. New York: Wiley and Sons.

