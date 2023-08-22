# Totals and Components Specification

## 1.0 Description

The automatic editing method for totals and
components correction is currently used in
ONS business surveys to ensure fixed
relationships between variables are satisfied.
For example, when a total (e.g., total employment
is collected along with the component breakdown
(e.g., full-time male, full-time female,
part-time male, part-time female)

The primary use of the method is to automatically
detect and correct errors in respondent data where
fixed relationships have not been satisfied to
improve the efficiency of the editing process,
reduce the burden on respondents and survey
validators and improve overall data quality.

This method can also be used to ensure fixed
relationships between variables are satisfied in
other data types such as imputed data to improve
overall data quality.

This method can only be applied if all the components
are of the same type e.g., all returned or imputed.
Maintaining a total and components relationship
where components are a combination of types
(e.g., returned and imputed) is outside of the scope
for this method.

## 2.0 Terminology

* Contributor – A member of the sample; identified by
 a unique identifier
* Record – A set of values for each contributor and
period
* Target Variable – The variable of interest that
the method is working on, the total or components
as determined by the Amend Total variable
* Target Record – A contributor's record in the
target period
* Predictive Variable – A value used as a predictor
for a contributor's target variable
* Predictive Record – The record containing a contributor's
 predictive value
* Auxiliary variable – The variable used as a predictor
for a contributor’s target variable, where the predictive
value is not available
* Responder – A contributor who has responded to the survey
within a given period
* Precision - The precision value determines the level of
accuracy for our floating point calculations

## 3.0 Technical Assumptions

* Predictive and auxillary variables are well correlated
* At least one of the target or auxillary variables
must be populated
* Thresholds determine the need for correction
* The totals and components method will be period agnostic, the
expectation is that the user of the method will ensure the
correct values are passed to the method for evaluation.
Example wrapper code will be written to show how this may work
but there will likely be updates required to the example
depending upon the data formats the user is working with.
* Total cannot be None

## 4.0 Data records

The following field types will be present in the input and
output records, for more details see the methodology specification.

### 4.1 Input records

* Unique Identifier – Any e.g. Business Reporting Unit
* Total Variable – Target total, numeric
* Components – Corresponding list of Total
variable's components, numeric – nulls allowed
* Amend Total – Select whether Total Variable should be
automatically corrected, Boolean. FALSE = correct components,
TRUE = correct total
* Predictive Variable – Previous or current total, numeric
* Auxiliary Variable – optional, numeric – nulls allowed
* Absolute Difference Threshold - Numeric (non-negative)
* Percentage Difference Threshold - Numeric (non-negative)
* Precision - optional integer value (valid values are 1-28)

Note: Either one of Absolute difference threshold or percentage
difference threshold can be null but at least one must be specified.

### 4.2 Output records

* Unique Identifier – Any e.g., Business Reporting Unit
* Absolute Difference – Numeric, nulls allowed
* Low Percent – Numeric, nulls allowed
* High Percent – Numeric, nulls allowed
* Final Total Variable – Numeric
* Final Components Variable – Numerics
* TCC Marker – To indicate the result of the Totals Components
Correction method, string

## 5.0 Process Overview

The following is a key of useful formula definitions/assumptions

* total_value - this is the total value received for the current
period, this value is expected to match the sum_of_components.
When an automatic correction is possible, the total_value is
used to adjust the components when amend_total is false.
* predictive_value - When specified, this is typically a verified
total value from the immediately prior period to the total_value
or equal to the total_value in the case that the current period
is used for both total and predictive values. When the user does
not specify a predictive_value then it may be set to equal the
auxiliary value if present. The predictive_value is used to
determine whether an automatic error correction can take place
when checked against the absolute and or percentage difference thresholds.
* amend_total - this indicates whether the total or
component values should be automatically corrected
* absolute_difference_threshold - this threshold is used
to determine whether automatic correction should take place
 when the difference between the predictive_value and
 sum_of_components is less than or equal to this threshold
* percentage_difference_threshold - an upper and lower threshold
 is calculated from this value using the sum_of_components.
 The upper and lower thresholds are then compared with the
 predictive_value to determine whether automatic
 correction can be applied
* sum_of_components - this is the original component
values summed up
* component_x - an individual component value from the
original list of components
* precision - the precision value determines the level of
accuracy for our sum of components and component
correction floating point calculations

Note: for the technical specification, we will be providing
both mathematical formulas and ascii formula representation.

We start with an input record which is passed to our method.

### 5.1 Validate Data Input (Stage 1)

We firstly, check to see if total, components, predictive
variable, auxillary variable (if specified), absolute
difference threshold (if specified), percentage difference
threshold (if specified) and precision (if specified) in the
data input are numeric values.

If precision is not specified (i.e None) then we default
the value to 28. If it is less than zero or greater than 28 we
raise a value error. Auxiliary and predictive can be None.

If any of the other values are not as we expect then we return a
tailored error message.

The methodology specification says that the method checks if
both absolute_difference_threshold and percent_difference_threshold
are absent. If this is the case validation will raise an exception
and method processing will stop. This is seen by the formulas below

```bash
    x_{absolute} = None
```

and

```bash
    x_{percent} = None
```

we can alternatively display this as below

```bash
    absolute_difference_threshold = None
```

and

```bash
    percent_difference_threshold = None
```

If it is false then we continue to stage 2.

### 5.2 Check Predictive Errors (Stage 2)

The next step is to check the predictive, auxiliary and
total all exist.

Otherwise, there are four ways in which the method can behave
based on these values. This includes the following

1. When total value is present, predictive value is None
and Auxiliary value is None then method stops and the
tcc marker "S" is returned.

2. When total value is present, predictive value is present
and Auxiliary value is None then the decision whether an
automatic correction can be made will be based off of the
predictive value and any recalculation of the components
will use the total value.

3. When total value is present and predictive value is
present and Auxiliary value is present then the decision
whether an automatic correction can be made will be based
off of the predictive value and any recalculation of the
components will use the total value.

4. When total value is present and predictive value is
None and Auxiliary value is present then the decision
whether an automatic correction can be made will be
based off of the auxiliary value and any recalculation
of the components will use the total value.

### 5.3 Check Zero Errors (Stage 3)

We must now check zero error conditions, this is to guard cases
when the component sum is zero and the total value is set. When
component sum is zero we do not want to make a correction to either
the total or components.

To do this our first step according to the methodology is to verify
if the following is true.

```bash
    y_{derived} = 0
```

In other words,

```bash
    sum_of_components = 0
```

the methodology specification also requires

```bash
    y_{total} > 0 
```

which can be visualised as

```bash
    total > 0
```

If this is the case and amend total is false then we have
TCC Marker = S and the method stops. We then write an output.

Otherwise, we move onto stage 4

### 5.4 Check Sum(components) and Predictive Value (Stage 4)

If the absolute difference threshold is defined then we check
the sum of the components and the predictive value to determine
whether there is a difference that may require correction.

The methodology specification requires us to determine
the absolute difference between the predictive and sum
of the components shown below.

```bash
    |y_{predictive} - y_{derived}| = x_{absolute}.
```

alternatively viewed as

```bash
    |predictive_value - sum_of_components| = absolute_difference.
```

Note: It is important that calculations are exact and do not suffer any
rounding errors that can be seen with binary arithmetic.

We now determine if the absolute difference between the sum of
the components and the total_value is zero then the method stops
and returns a tcc marker of "N".

If true, we have a TCC Marker = N meaning we have no correction
and the method stops with an output written.

Else we move onto the next stage which is where we determine
an error detection method.

### 5.5 Determine Error Detection Method (Stage 5)

Stage 5 determines whether the detected difference in the
provided components and totals falls into the absolute or percentage
difference thresholds specified and if so, the values will be
automatically corrected, else they will be marked for manual editing.

When only Absolute Difference Threshold is set to 0 or above,
the threshold is checked against the absolute value.

When only Percentage Difference Threshold is set to 0 or above
the predictive variable is checked against an acceptable range
calculated based on the percentage threshold.

The range is calculated based on a percentage of the components sum
while the percentage is determined by the
percent_difference_threshold.

If the predictive variable is outside of this range, then we require
manual editing and the method stops.

### 5.5.1 Check Absolute Difference Threshold (Stage 5a)

The absolute difference between the predictive variable and
the components must be less than or equal to the absolute difference threshold.

When the Absolute Difference Threshold check indicates either the total or
components can be automatically corrected. The method
continues to stage 6 in section 5.6.

When the Absolute Difference Threshold check indicates the
correction needs to be manually applied the method will check
if a Percentage Difference Threshold needs to be checked.

Note: Before we leave this stage we need to check
that zero error condition 3 is satisfied i.e. If
total_value = 0 and sum_of_components > 0 and amend total = TRUE:
The total should be corrected if the difference observed is within
the tolerances determined by the detection method (see section 5.6).
Else, the difference should be flagged for manual checking.

### 5.5.2 Check Percentage Difference Threshold (Stage 5b)

If the predictive variable is within the low and high
percentage then we go to stage 6 in section 5.6.
Otherwise, we require manual editing and stop the method.

The high and low percent range is calculated by taking the
sum of components and adding or subtracting the
sum of components multiplied by the percentage difference
threshold respectively.

This can defined by the method spec as

$$ \large low_{percent} = y_{derived} - (y_{derived} * x_{percent}) $$

$$ \large high_{percent} = y_{derived} + (y_{derived} * x_{percent}) $$

this can also be viewed as follows

```bash
low_percentage_threshold = sum_of_components - (sum_of_components * percentage_difference_threshold)
```

```bash
high_percentage_threshold = sum_of_components + (sum_of_components * percentage_difference_threshold)
```

the following condition then applies

```bash
    low_{percent} =< y_{total,predictive} =< high_{percent}
```

alternatively seen as

```bash
    low_percentage_threshold <= predictive <= high_percentage_threshold
```

### 5.6 Error Correction (Stage 6)

This section covers the error correction aspect of the process.
When the method has reached this part of the processing earlier checks
have determined that either the total or the components must
be automatically corrected.

When the input parameter amend_total indicates that the total
must be amended the methodology specification requires us to
automatically correct the total as seen below.

```bash
    y_{final_total} = y_{1, t} + ... + y_{n, t}
```

This can also be understood in the following format

```bash
    final_total = sum_of_components
```

Expanding on this if we correct the total then we set the final
total in the output data equivalent to the sum of components, and the
final values for all the components match their originals. We would now
return a totals corrected marker.

Where the amend_total indicates the components need to corrected,
we use the total value and the precision for the component
automatic correction calculations.

If the components are to be corrected, then they will always use the
proportions (weighting) observed in the original components and rescale
to the total variable.

Expanding on this, the methodology spec informs us that if we require
components to be corrected then we use the algorithm where the new
component is equal to the component divided by the sum_of_components
and the result of this is multiplied by the total value as seen below.

$$ y_{final\_component} = \Bigl(\frac{y_{original\_component}}
{y_{derived}}\Bigl) * y_{total}
$$

which is understood as

```bash
    final_component = (original_component / sum_of_components) * total
```

In the case where the total is set to zero and the amend_total
indicates that the components need to be adjusted this step of the method
will ensure that each component is reset to zero to match the expected total.
This can be visualised using the formula below.
