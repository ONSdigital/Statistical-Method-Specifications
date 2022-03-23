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

In certain cases a matched pair contributor may wish to be excluded from the link
calculations. The method accepts an optional inclusion marker, that, when it
contains *True* is included and when it contains *False* is excluded from the
calculations. When this marker is not given to the method, all matched pair
contributors are included.

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
5. Mean or Median initial calculation - MNI or MDI (Not yet written)
6. Forward Imputation from Mean or Median - FIMN or FIMD (Not yet written)

## The Calculations

### Forward Imputation Link

Calculated using matched pair contributors for the strata, in the current and
previous periods.

```
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

```
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
```
Backward_Link(t) == 1/Forward_Link(t+1)
```

### Construction Link

Calculated using current period responses for the strata and their corresponding
auxiliary values.

```
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

Please note the following exceptions to the method's standard
behaviour:

1. (Not written) In some cases it may be appropriate to use an imputation link
2. which is an average of imputation links for more than one. In the simplest
3. case this could be the average of two links. Two further parameters would need
4. to be specified: the lag ($k$) and the weight ($w$) given to each period. In
5. this case the imputation link is calculated as:

```
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
2. all being constructed that the method will need to accept periods of back data.
3. This data will be used to calculate links and forward impute from but will not
4. be returned on the output. Care must be taken to ensure that the back data does
5. mess with the prioreties mentioned above.

## Technical Information

https://github.com/ONSdigital/statistical-methods-library/blob/main/statistical_methods_library/imputation.py

## References

**De Waal, T., Pannekoek, J. and Scholtus, S.** (2011) Handbook of Data
Editing and Imputation. New York: Wiley and Sons.
