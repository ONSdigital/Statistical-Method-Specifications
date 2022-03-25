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
* Strata - How the data has been broken into subsets.
    Also know as Imputation Class.
* Contributor - A member of the sample, identified by a unique identifier.
* Record - A set of values for each contributor and period
* Target record - The record currently being imputed for due to its target
    variable being missing.
* Target period - The period currently undergoing imputation.
* Predictive period - The period directly preceeding or succeeding the
    target period.
* Responder - A contributor who has responded to the survey within a given
    period.
* matched Pairs - Responders which are present in both the target and
    predictive periods.
* Link - A ratio of the means of the matched pairs.

## Introduction

Ratio of means is a standard imputation method used for business
surveys. Due to its robust nature it does not use any form of trimming
or outliering.

The method willimpute for a single numeric target variable within the
dataset.
It will output a separate dataset containing the imputed target variable and
information necessary to identify the records. Other input variables will not
be passed through to the output.

## Assumptions

This method assumes that the auxiliary variable is a good predictor of the
target variable. This method also assumes that the contributor's target
variable value in the predictive period is a good predictor of the target
variable in the target period. This same assumption is also made for matched
pairs' target variable values.

## Overall Method

The imputation method consists of a number of processes as detailed below.
The method ends only when either there are no more missing values within the
target variable or no more values can be imputed. The latter case
constitutes an error condition and will be handled according to the error
handling behaviour defined below.

The method uses a link, in combination with a predictive value, to calculate
an imputed value for a target record. This predictive value can either be
the target variable value from the contributor's record in the predictive
period or an auxiliary variable.

There are six types of imputation performed by this method:

* Forward imputation from response
* Backward imputation
* Construction
* Forward imputation from construction
* Average imputation

## Link Calculation

### Responder filtering

By default the method will consider all responders when calculating links. However the method must
also accept an optional expression for filtering responders. If provided,
link calculations will only consider responders matching this filter. This
filter will only apply to link calculations.

### Responder pair matching

In order to calculate links, matched pairs of responders must be used. These
matched pairs comprise of responders in both the target and predictive
periods with the same unique identifier.

### Forward and backward link calculation

Forward and backward links will be calculated using the formula:

```text
link(target_period) = sum(target_responses)/sum(predictive_responses)
```

where:

* `target_responses` contains all the responses for the target variable for
    matched pairs in the target period
* `predictive_responses` contains all the responses for the target variable for
    matched pairs in the predictive period

When calculating the forward link, the previous period will be used as the
predictive period, whereas for the backward link the next period relative to
the target period will be used.

### Construction link calculation

The construction link will be calculated using the following formula:

```text
link(target_period) = sum(target_responses)/sum(target_auxiliaries)
```

where:

* `target_responses` contains all responses for the target variable in the
    target period
* `target_auxiliaries` is the auxiliary variable values for contributors
    whos values are present in `target_responses`

### Pre-calculated links

It must also be possible to pass pre-calculated link columns to the method.
In this case all three types of links must be provided; this requirement is
to avoid any assumptions within the method as to the relationship between
provided links.

## Construction

In scenarios where there is not a previous/consecutive response, the method
will construct an initial value to impute from by using the construction
link and the non-responders auxiliary variable. Typically a register-based
variable such as frozen turnover or frozen employment would be used for this
purpose.

If there is no auxiliary variable for a given record, the method will
calculate an imputed value using
either the mean or median value for the target variable of the responders in
the record's period and strata.

In certain cases a matched pair contributor may wish to be excluded from the link
calculations. The method accepts an optional inclusion marker, that, when it
contains *true* is included and when it contains *false* is excluded from the
calculations. When this marker is not given to the method, all matched pair
contributors are included.

Links can also be passed into the method rather than being calculated by the
method. If this occurs all three types of links must be provided (this is due to
there being a defined relationship between the forward and backward links) and if
they are missing for any contrubutors they will default to 1.

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

## References

**De Waal, T., Pannekoek, J. and Scholtus, S.** (2011) Handbook of Data
Editing and Imputation. New York: Wiley and Sons.
