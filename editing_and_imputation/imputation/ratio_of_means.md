# Ratio of Means Specification

## Meta

* Support Area - Methodology - Editing & Imputation
* Support Contact - <Editing.and.Imputation.expert.group@ons.gov.uk>
* Method Theme - Imputation
* Method Classification - Ratio Imputation
* Status - Partially tested, draft (not published)

## Terminology

* Target Variable - The variable of interest that the method
    is working on.
* Group - How the data has been broken into subsets. Also know as Imputation
    Class.
* Contributor - A member of the sample, identified by a unique identifier.
* Record - A set of values for each contributor and period
* Target Period - The period currently undergoing imputation.
* Predictive Period - The period directly preceeding or succeeding the
    target period.
* Target Record - A contributor's record in the target period.
* Predictive Record - A contributor's record in the predictive period.
* Responder - A contributor who has responded to the survey within a given
    period.
* Link - A ratio used as part of the imputation process.

## Introduction

Ratio of means is a standard imputation method used for business
surveys. Due to its robust nature it does not use any form of trimming
or outliering.

The method willimpute for a single numeric target variable within the
dataset.
It will output a separate dataset containing the imputed target variable and
information necessary to identify the records. Other input variables will not
be passed through to the output.

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

There are seven types of imputation performed by this method:

* Forward imputation from response
* Backward imputation
* Construction
* Forward imputation from construction
* Average imputation
* Forward imputation from average imputation

All link and imputation calculations must be performed treating each group
in the dataset separately. For brevity the calculations are shown assuming a
single group is being used.

### Link Calculation

#### Responder Filtering

By default the method will consider all responders when calculating links. However the method must
also accept an optional expression for filtering responders. If provided,
link calculations will only consider responders matching this filter. This
filter will only apply to link calculations.

#### Responder Pair Matching

In order to calculate links, matched pairs of responders must be used. These
matched pairs comprise of responders in both the target and predictive
periods with the same unique identifier.

#### Definitions

The following definitions apply to formulae within this section:

* `target_period` = the target period
* `predictive_period` = the predictive period
* `responses(period)` = Responses for the target variable in the given period
* `matched_responses(period)` = Responses for matched pairs for the given period
* `auxiliaries(period)` = Values for the auxiliary variable for responders
    within a given period

#### Forward And Backward Link Calculation

Forward and backward links will be calculated using the formula:

```text
link(target_period) = sum(matched_responses(target_period))/sum(matched_responses(predictive_period))
```

When calculating the forward link, the previous period will be used as the
predictive period, whereas for the backward link the next period relative to
the target period will be used.

#### Construction Link Calculation

The construction link will be calculated using the following formula:

```text
link(target_period) = sum(responses(target_period))/sum(auxiliaries(target_period))
```

#### Pre-Calculated Links

It must also be possible to pass pre-calculated link columns to the method.
In this case all three types of links must be provided; this requirement is
to avoid any assumptions within the method as to the relationship between
provided links.

### Imputation

Using the link definitions, The general imputation formula is:

```text
impute(target_record) = link(target_period) * predictive_value(target_period)
```

where:

* `predictive_value(period)` = The applicable predictive value for the type
    of imputation for the given period.

Imputation must only be applied to records with missing values in the target
variable.

#### Forward And Backward Imputation

For this type of imputation the following definition is used:

```text
predictive_value(target_period) = target_value(predictive_period)
```

where:

* `target_value(period) = The value for the target variable in the
    predictive record.

If there is no predictive record for a given contributor then these types of
imputation must not be performed for this contributor.

For forward imputation the forward link will be used and for backward imputation
the backward link will be used. The predictive period for the type of imputation
being performed must be the same as that for the link being used.

##### Forward Imputation From Response

In this type of imputation, only predictive records which are either
responses or forward imputes from responses can be used. Records imputed
using this imputation will be marked `FIR`.

##### Forward Imputation From Construction

In this type of imputation, only predictive records which are imputes from
construction can be used. Records imputed using this imputation will be marked
`FIC`.

##### Forward Imputation From Average Imputation

In this type of imputation, only predictive records which are imputes from
average imputation can be used. Records imputed using this imputation will
be marked with `FI` with the applicable average imputation marker appended with no separator.

##### Backward Imputation

In this type of imputation, only predictive records which are responses can
be used. Records imputed using this imputation will be marked `BI`.

Backward imputation from construction must not occur.

#### Construction

In scenarios where neither forward or backward imputation can be performed
due to a lack of a predictive record or link for a contributor, the method
will use the following predictive value definition to perform construction
imputation.

```text
predictive_value(target_period) = auxiliary(target_period)
```

where:

* `auxiliary(period)` = The value of the contributor's auxiliary variable
for a given period

This type of imputation must only be performed where the target record has a
value for its auxiliary variable. Records imputed using this imputation will
be marked `C`.

#### Average Imputation

If there is no auxiliary variable for a given record, the method will
calculate an imputed value using the following formula:

```text
impute(target_period) = average(responses(target_period))
```

where:

* average(responses) = An average value for a given set of responses

In the case of mean imputation the average used will be the mean and imputed
records will be marked `MNI`. In the case of median imputation the median
will be used and imputed records will be marked `MDI`.

## Back Data

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

