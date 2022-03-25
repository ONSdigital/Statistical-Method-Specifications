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
* Record - A set of values for each contributor and period
* Target period - The period currently undergoing imputation.
* Predictive period - The period directly preceeding or succeeding the
    target period.
* Target record - A contributor's record in the target period.
* Predictive record - A contributor's record in the predictive period.
* Responder - A contributor who has responded to the survey within a given
    period.
* link - A ratio used as part of the imputation process.

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
the target variable value from the contributor's predictive record or an auxiliary variable.

There are six types of imputation performed by this method:

* Forward imputation from response
* Backward imputation
* Construction
* Forward imputation from construction
* Average imputation

All link and imputation calculations must be performed treating each group
in the dataset separately. For brevity the calculations are shown assuming a
single group is being used.

### Link Calculation

#### Responder filtering

By default the method will consider all responders when calculating links. However the method must
also accept an optional expression for filtering responders. If provided,
link calculations will only consider responders matching this filter. This
filter will only apply to link calculations.

#### Responder pair matching

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

#### Forward and backward link calculation

Forward and backward links will be calculated using the formula:

```text
link(target_period) = sum(matched_responses(target_period))/sum(matched_responses(predictive_period))
```

When calculating the forward link, the previous period will be used as the
predictive period, whereas for the backward link the next period relative to
the target period will be used.

#### Construction link calculation

The construction link will be calculated using the following formula:

```text
link(target_period) = sum(responses(target_period))/sum(auxiliaries(target_period))
```

#### Pre-calculated links

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

#### Forward and Backward imputation

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

This type of imputation can only be performed where the target record has a
value for its auxiliary variable.

If there is no auxiliary variable for a given record, the method will
calculate an imputed value using
either the mean or median value for the target variable of the responders in
the record's period and strata.

If a contributor that has been sampled is rotated out and then later on is rotated
back in to the sample, the method must not calculate accross this gap because they
are not non-responders, the contributors will not have any data assosiated with
them for those periods as they are not sampled.

### Order Of Calculation Priority And Imputation Markers

The method prioretises calculating the non-responder's target variable using the
following links and marks that value with the calculation used:

0. Response, no calculation - R
1. Forward Imputation from a Response - FIR
2. Backward Imputation from a Response - BI
3. Construct initial values - C
4. Forward Imputation from a Constructed Value - FIC

## The Calculations

### Forward Imputation Link

Calculated using matched pairs for the strata, in the current and
previous periods.

```latex
$\text{Forward link} = \frac{\text{
sum(variable of interest within strata and current period)}}{\text{
sum(variable of interest within strata and previous period)}}$
```

Then simple take a non-responders previous period value and multiply it by
the link.

### Backward Imputation Link

Calculated using matched pairs for the strata, in the current and
consecutive periods.

```text
Backward Link = sum(current period's target variables)/sum(consecutive period's
target variables)
```

```latex
$\text{Backward link} = \frac{\text{
sum(variable of interest within strata and period of interest)}}{\text{
sum(variable of interest within strata and consecutive period)}}$
```

Then simple take a non-responders consecutive period value and multiply it by
the link.

NOTE: For a contributor, the backward link at period t and the forward link at
t+1 are related. (This is the reason if links are provided and not calculated,
all links should be provided)

```text
Backward_Link(t) == 1/Forward_Link(t+1)
```

### Construction Link

Calculated using current period responses for the strata and their corresponding
auxiliary values.

```text
Construction Link = sum(current period's target variables)/
sum(current period's auxiliary variables)
```

```latex
$\text{Construction link} = \frac{\text{
sum(variable of interest within strata and current period)}}{\text{
sum(aux variable within strata and current period)}}$
```

Then simple take a non-responders auxiliary value and multiply it by the link.

## Exceptions

[This Section Is Not Implemented](#unimplemented-features)

Please note the following exceptions to the method's standard
behaviour:

1. In some cases it may be appropriate to use an imputation link
   which is an average of imputation links for more than one. In the simplest
   case this could be the average of two links. Two further parameters would need
   to be specified: the lag ($k$) and the weight ($w$) given to each period. In
   this case the imputation link is calculated as:

```text
Link = (weight * sum(current var 1) / sum(current var 2)) +
((1 - weight) * (sum(previous var 1) / sum(previous var 2)))
```

```latex
$\text{imputation link} = w * \frac{\sum_{j\in{impclass}}{y_{j,t}}}
{\sum_{j\in{impclass}}{x_{j,t}}} + (1 - w)*\frac{\sum_{j\in{impclass}}
{y_{j,t-k}}}{\sum_{j\in{impclass}}{x_{j,t-k}}}$
```

## Special Cases

1. In certain scenarios it may be that to avoid the first period of non-responses
   all being constructed that the method will need to accept periods of back data.
2. This data will be used to calculate links and forward impute from but will not
   be returned on the output. Care must be taken to ensure that the back data does
   mess with the prioreties mentioned above.

## Technical Information

The current implementation of the method is Python-PySpark and can be found here
at [Statistical Methods Library - Ratio Of Means Code](https://github.com/ONSdigital/statistical-methods-library/blob/main/statistical_methods_library/imputation.py).

The method uses the following input data as part of the input_df (the back_data input
requires similar data except that instead of the target variable it will have the
returned/imputed target variable and imputation marker columns for the periods it
contains):

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

## Unimplemented Features

1. Strata currently allows only a single column.
   Will be adjusted in the futre to allow a list of columns.
2. Weighted Links.
3. Imputation link Inclusion Marker column.
4. Output number of matched pairs used when calculating links.
5. Periodicity options of Quarterly and Annually.
6. Mean/Median when missing auxiliary values for Construction.

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

