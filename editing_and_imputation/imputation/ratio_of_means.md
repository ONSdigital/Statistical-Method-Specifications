# Ratio of Means Specification

## Meta

* Support Area - Methodology - Editing & Imputation
* Support Contact - <Editing.and.Imputation.expert.group@ons.gov.uk>
* Method Theme - Imputation
* Method Classification - Ratio Imputation
* Status - Partially tested, draft (not published)

## Terminology

* Matched Pair Contributors - Contributors that have responded in both
  the periods being used in a calculation.
* Link - A ratio of the means of the matched pair contributors. (Three
  types are used in this method. Forward links, backward links and
  construction links)
* Target variable - The variable of interest that the method
  is working on.
* Strata - How the data has been broken into subsets.
  Also know as Imputation Class.

## Introduction

Ratio of means is a standard imputation method used for business
surveys. Due to its robust nature it does not use any form of trimming
or outliering.

The method imputes for a single numeric variable of interest for each
contributor/period that the target variable is a non-responder for.

Currently the method only works for monthly data but should a need arise
can be adjusted to quarterly or annually.

## Assumptions

1. The auxiliary variable is a good predictor of the target variable.
2. If the auxiliary variable is not available for a non-responding
   contributor, either there must be other matched pair contributors in
   the same strata as that contributor or the contributor must have a
   response in any previous or consecutive period.

## Summary

The method calcultes links for each strata and period combination in the data,
between the current period and both the previous (forward link) and consecutive
(backward link) periods using matched pair contributors or between the current
periods target variable and an auxiliary variable (construction link). Then for
non-responders it applies (via simple multiplication) these links to
previous/consecutive period data to fill in the blanks.

In scenarios where there is not a previous/consecutive response to apply a link
to, the method will construct an initial value to impute from by using the
construction link and the non-responders auxiliary variable (This should be a
register-based variable such as frozen turnover or frozen employment from IDBR).

If a value can still not be calculated the method will attempt to calculate a
mean or medium value of the responders in the non-responders period/strata.

The method uses rolling imputation to ensure cases where there are multiple
periods of non-response are covered by starting at a
response/constructed/mean/median value and progressing period by period applying
the link until no other non-responses are found.

[Not Implemented](#unimplemented-features)<a name="inclusion"></a>
~~In certain cases a matched pair contributor may wish to be excluded from the link
calculations. The method accepts an optional inclusion marker, that, when it
contains *True* is included and when it contains *False* is excluded from the
calculations. When this marker is not given to the method, all matched pair
contributors are included.~~

Links can also be passed into the method rather than being calculated by the
method. If this occurs all three types of links must be provided (this is due to
there being a defined relationship between the forward and backward links) and if
they are missing for any contrubutors they should default to 1.

If a contributor is rotated out of a sample and then back in at a later date the
method does not calculate accross this gap.

### Order Of Calculation Priority And Imputation Markers

The method prioretises calculating the non-responder's target variable using the
following links and marks that value with the calculation used:

0. Response, no calculation - R
1. Forward Imputation from a Response - FIR
2. Backward Imputation from a Response - BI
3. Construct initial values - C
4. Forward Imputation from a Constructed Value - FIC
5. ~~Mean or Median initial calculation - MNI or MDI~~ <a name="mean"></a>
6. ~~Forward Imputation from Mean or Median - FIMN or FIMD~~ [Not Implemented](#unimplemented-features)

## The Calculations

### Forward Imputation Link

Calculated using matched pair contributors for the strata, in the current and
previous periods.

```text
Forward Link = sum(current period's target variables)/sum(previous period's
target variables)
```

```latex
$\text{Forward link} = \frac{\text{
sum(variable of interest within strata and current period)}}{\text{
sum(variable of interest within strata and previous period)}}$
```

Then simple take a non-responders previous period value and multiply it by
the link.

### Backward Imputation Link

Calculated using matched pair contributors for the strata, in the current and
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
2. [Weighted Links.](#exceptions)
3. [Imputation link Inclusion Marker column.](#inclusion)
4. Output number of matched pairs used when calculating links.
5. Periodicity options of Quarterly and Annually.
6. [Mean/Median when missing auxiliary values for Construction.](#mean)

## References

**De Waal, T., Pannekoek, J. and Scholtus, S.** (2011) Handbook of Data
Editing and Imputation. New York: Wiley and Sons.
