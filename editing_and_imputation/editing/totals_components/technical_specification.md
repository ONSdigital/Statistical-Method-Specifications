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
 with the target variable
* At least one of the target or auxillary variables
must be populated
* Thresholds determine the need for correction
* The method can only observe one set of components and total
at any given time
* Total cannot be None

## 4.0 Data records

The following field types will be present in the input and
output records, for more details see the methodology specification.

### 4.1 Input records

* Unique Identifier – Any e.g. Business Reporting Unit
* Total Variable – Target total, numeric – nulls allowed
* Components – Corresponding list of Total
variable's components, numeric – nulls allowed
* Amend Total – Select whether Total Variable should be
automatically corrected, Boolean. FALSE = correct components,
TRUE = correct total
* Predictive Variable – Previous or current total, numeric
* Auxiliary Variable – optional, numeric – nulls allowed
* Absolute Difference Threshold - Numeric (non-negative)
* Percentage Difference Threshold - Numeric (non-negative)
* Precision - integer value (between 0 and 29)

### 4.2 Output records

* Unique Identifier – Any e.g., Business Reporting Unit
* Absolute Difference – Numeric, nulls allowed
* Low Percent – Numeric, nulls allowed
* High Percent – Numeric, nulls allowed
* Precision - integer value (between 0 and 29)
* Final Total Variable – Numeric
* Final Components Variable – Numeric
* TCC Marker – To indicate the result of the Totals Components
Correction method, string

## 5.0 Process Overview

The following is a key of useful formula definitions/assumptions

* total_value - this is the expected total value for the
 sum_of_components and is used to correct components if
 automatic correction is applied
* predictive_value - this is the expected total_value
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
original ist of components
* precision - The precision value determines the level of
accuracy for our sum of components and component 
correction floating point calculations

We start with an input record which is passed to our method.

### 5.1 Validate Data Input (Stage 1)

We firstly, check to see if total, components, predictive
variable, auxillary variable (if specified), absolute
difference threshold (if specified), percentage difference
threshold (if specified) and precision (if specified) in the 
data input are numeric values. 

If precision is not specified (i.e None) then we default 
the value to 28. If it is less than zero or greater then 28 we
raise a value error. Auxiliary and predictive can be None.

If any of the other values are not a number then we return a
tailored error message and stop the method with an "S"
tcc marker.

Then, we must verify if

```bash
    absolute_difference_threshold = None
```

and

```bash
    percent_difference_threshold = None
```

holds. If this is true we flag an exception with the message
"One or both of absolute/percentage difference thresholds must
be specified" and the method stops.

If it is false then we continue to stage 2.

### 5.2 Check Predictive Errors (Stage 2)

The next step is to check the predictive, auxiliary and total.

Otherwise, there are five ways in which the method can behave 
based on these values. This includes the following

1. When total value is present, predictive value is None
and Auxiliary value is None then the decision whether an
automatic correction can be made will be based off of the
total value and any recalculation of the components will
use the total value.

2. - When total value is present, predictive value is present 
and Auxiliary value is None then the decision whether an 
automatic correction can be made will be based off of the 
predictive value and any recalculation of the components 
will use the total value.

3. - When total value is present and predictive value is 
present and Auxiliary value is present then the decision 
whether an automatic correction can be made will be based 
off of the predictive value and any recalculation of the 
components will use the total value.

4. - When total value is present and predictive value is 
None and Auxiliary value is present then the decision 
whether an automatic correction can be made will be 
based off of the auxiliary value and any recalculation 
of the components will use the total value.

### 5.2 Check Zero Errors (Stage 3)

We must now check zero error conditions, this is to guard cases
when the component sum is zero and the total value is set. When
component sum is zero we do not want to make a correction to either
the total or components.

To do this our first step is to verify if the following is true.

```bash
    sum_of_components = 0
```

and

```bash
    predictive_value > 0
```

If this is the case then we have TCC Marker = S and the method
stops. We then write an output.

If however the above is false we move onto stage 4

### 5.3 Check Sum(components) and Predictive Value (Stage 4)

Stage 4 is where we check the sum of the components and the
predictive value to determine whether there is a difference that
may require correction.

The initial part requires us determine the absolute difference
between the total and sum of the components.

```bash
    |predictive_value - sum_of_components| = absolute_difference_threshold.
```

Note: The computed absolute difference needs to be available
so that it can be output when results are returned. It is also
important to understand we use the python
Decimal() method to apply the defined precision value
to the floating values for this stage.

We now determine if the absolute difference between the sum of
the components and the predictive is zero then the method stops
with an indication of no correction.

If true, we have a TCC Marker = N meaning we have no correction
and the method stops with an output written.

Else we move onto the next stage which is where we determine
an error detection method.

### 5.4 Determine Error Detection Method (Stage 5)

Stage 5 determines whether the detected difference in the
provided components and totals falls into the absolute or percentage
difference thresholds specified and therefore needs to be
automatically correct or the values need to be manually corrected.

When only Absolute Difference Threshold is set to 0 or above
the threshold is checked against the absolute value calculated
in step 4. We complete this action in section 5.4.1.

When only Percentage Difference Threshold is set to 0 or above
the threshold is checked against the predictive total. The percentage
threshold is the sum of the received components plus and minus the
Percentage Difference Threshold specified. If this threshold is not
satisfied then we require manual editing and the method stops.

### 5.4.1 Check Absolute Difference Threshold (Stage 5a)

The absolute difference between the target total and
the components must be less than or equal to the absolute difference threshold.

When the Absolute Difference Threshold check indicates the
difference needs to be automatically corrected the method
continues to stage 6 in section 5.5.

When the Absolute Difference Threshold check indicates the
correction needs to be manually applied the method will check
 if a Percentage Difference Threshold needs to be checked.

Note: Before we leave this stage this point we need to check
that zero error condition 3 is satisfied i.e. If
target total = 0 and components sum > 0 and amend total = TRUE:
The total should be corrected if the difference observed is within
the tolerances determined by the detection method (see section 5.5).
Else, the difference should be flagged for manual checking.

### 5.4.2 Check Percentage Difference Threshold (Stage 5b)

If the total for the predictive is within the low and high
percentage then we go to stage 6 in section 5.5.
Otherwise, we require manual editing and stop the method.

### 5.5 Error Correction (Stage 6)

This section covers the error correction aspect of the process.
When the method has reached this part of the processing earlier checks
have determined that either the total or the components must
be automatically corrected.

When the input parameter amend_total indicates that the total
must be amended we automatically correct the total.

Where the amend_total indicates the components need to corrected,
we use the total value and the precision for the component
automatic correction calculations.

Expanding on this if we correct the total then we set the final
total in the output data equivalent to the total, and the
final values for all the components match their originals. We would now
return a totals corrected marker.

However, if the components are corrected to match the received
total based on the weighting of the original input component values,
then we return a components corrected marker.

In the case where the total is set to zero and the amend_total
indicates that the components need to be adjusted this step of the method
will ensure that each component is reset to zero to match the expected total.
