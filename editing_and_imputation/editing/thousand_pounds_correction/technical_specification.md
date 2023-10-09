# Thousand Pounds Correction Specification

## 1.0 Description

The thousand pounds correction is commonly used across business surveys.
It is a generic rule that uses user defined thresholds to automatically
detect and correct thousand pounds errors.

This is when the respondent should have reported values in thousands of
pounds but has reported in actual pounds e.g., returned a value of
£56,000 instead of correctly submitting 56.

A principal variable must be specified as a priority indictor for whether
a thousand pounds error has occurred.

The method checks the principal variable for a given contributor to
determine whether the ratio of the principal variable by the predictive
variable is within a fixed set of upper and lower thresholds.

If the ratio lies within these thresholds, then a thousand pounds
correction is automatically applied to the principal variable and the rest
of the target variables, if any, are automatically corrected.

## 2.0 Terminology

* Contributor - A member of the sample; identified by a unique identifier.
* Record - A set of values for each contributor.
* principal Variable – Variable that the method is working on and will
determine if the remaining monetary variables, if any, will be
automatically corrected.
* Target Variable(s) – List of all monetary variables that may be
automatically corrected, excluding the principal variable.
* Target Record - A contributor's target record.
* Predictive Record - The record containing a contributor's predictive value.
* Auxiliary variable - An alternative variable used as a predictor for a
contributor's principal variable, where the predictive value is not available
for a given contributor
* Responder - A contributor who has responded to the survey

## 3.0 Technical Assumptions

* Data classes are used to provide structure where required
* All data class datasets are immutable upon creation
* Missing/null values are not adjusted (they are placed in the output dataset
'as-is' regardless of calculations)
* Uses built-in python libraries only
* Assumes that all processing can be dealt with 'in-memory' and output is
provided as single dataset
* Errors are caught internally within the method and are converted into managed
output errors (TPC Marker of "S")
* A ratio inside of the upper or lower thresholds is due to a thousand
pounds error
* If the principal variable is found to be a thousand pounds error, then it is
assumed that all other monetary values are also thousand pounds errors
* The principal variable and predictive value are well correlated and are
intended to be reported in the same denomination (i.e., thousand pounds)
* The principal variable and auxiliary value are well correlated and intended
to be reported in the same denomination (i.e., thousand pounds)
* The auxiliary variable is known and available for all contributors processed
by the method
* Thresholds set are a good indication of whether a value should be corrected
* Although predictive and auxiliary are both optional, at least one has to be
provided for the calculation, else a method error is produced and the TPC
marker "S" is returned in the output.
* The unique_identifier is unused directly by the method and is passed-through
as-is into the output dataset. This attribute is provided to allow a user
context to be provided as required.
* If no target_variables are provided only the principal_variable may
be adjusted

## 4.0 Data records

The following field types will be present in the input and
output records, for more details see the methodology specification.

### 4.1 Input records

* Unique Identifier – String
* principal Variable – Single variable, numeric
* Target Variable(s) – List of monetary variables that may be automatically corrected
* Predictive Variable – Single variable, numeric – nulls allowed
* Auxiliary Variable – Optional, numeric – nulls allowed
* Upper Limit – Single variable, numeric, must be greater than Lower Limit
* Lower Limit – Single variable, numeric, must be less than Upper Limit

### 4.2 Output records

* Unique Identifier – Business Reporting Unit
* TPC Ratio – Numeric
* Final principal Variable – Numeric
* Final Target Variables – Can be a list, numeric – nulls allowed
* TPC Marker – To indicate the result of the Thousand Pounds Correction
method, string

## 5.0 Process Overview

The following is a key of useful formula definitions/assumptions

* unique_identifier: (String) - Unique identifier
* principal_variable: (Float) - Numeric value that the method is working on
* predictive: (Float) - Optional - Numeric value used for comparison.
A previous 'valid' value (i.e. Returned/Imputed/Constructed)
* auxiliary: (Float) - Optional - Alternative numeric` value used when
a predictive value is not available and required by the user
* upper_limit: (Float) - Upper bound of 'error value' threshold
* lower_limit: (Float) - Lower bound of 'error value' threshold
* target_variables: (List of Variables) - Optional - List of linked
question and values to potentially be adjusted

### 5.1 Validate Data Input (Stage 1)

The methodology specification says that the method checks if
both predictive and auxiliary are absent. If this is the case validation
will raise an thousand pounds exception
and method processing will stop. This is seen by the formulas below

```bash
    x_{predictive} = None
```

and

```bash
    x_{auxiliary} = None
```

we can alternatively display this as below

```bash
    predictive = None
```

and

```bash
    auxiliary = None
```

If the predictive and auxiliary are None then we return a tailored
error message. However, if both are 0 or predictive is 0 and
auxiliary is none then and stop the method with an "S" TPC marker.

If principal variable, lower limit, upper limit or question values
within an array are None or the upper limit is less than or equal to
the lower limit. Then we return a tailored error exception messages.

We then move onto stage 2.

### 5.2 Check Predictive Value (Stage 2)

The predictive value is used in later calculations. Hence, if the
predictive value is the None and the auxiliary exists then the auxiliary
is used in it's place.

We then move onto stage 3.

### 5.3 Calculate error detection ratio (Stage 3)

We now calculate the error ratio.

A ratio is determined by the ratio of the latest returned principal value.
The comparison value is determined by either the predictive variable.

If the comparison value is not zero the methodology specification defines
that we must check that:

$$ l_{lower\_limit} < \frac{y_{principal\_value}}
{y_{comparison\_value}} < l_{upper\_limit}
$$

Which can be viewed as,

```bash
    lower_limit < (principal_value / comparison_value) < upper_limit
```

Likewise, if a thousand pounds error determines that no correction
is needed then the principal variable and any linked variables will
not be adjusted and instead returned as their original values.
We will then return the original value "N" TPC marker in the output
to signify no correction has been made.

We then move onto stage 4.

### 5.3 Error Correction (Stage 4)

Here we have determined that a thousand pounds error has been detected.

When a thousand pounds error has been detected we apply the following
correction to the principal value and all linked values (e.g. target variables)
as defined by the methodological specification shown below:

$$ y_{adjusted\_value} = \frac{y_{principal_value}}
{1000}
$$

Which can also be seen in the following format

```bash
    adjusted_value = principal_value / 1000
```

The method then returns a corrected "C" TPC marker.

Note: To prevent loss of accuracy values are not rounded to 2.d.p
like monetary values are. Instead the full Decimal value is returned.
