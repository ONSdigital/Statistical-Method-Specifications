# Totals and Components Specification
## 1.0 Description

The automatic editing method for totals and components correction is currently used in ONS business surveys to ensure fixed relationships between variables are satisfied. For example, when a total (e.g. total employment) is collected along with the component breakdown (e.g., full-time male, full-time female, part-time male, part-time female).

The primary use of the method is to automatically detect and correct errors in respondent data where fixed relationships have not been satisfied to improve the efficiency of the editing process, reduce the burden on respondents and survey validators and improve overall data quality.

This method can also be used to ensure fixed relationships between variables are satisfied in other data types such as imputed data to improve overall data quality.

## 2.0 Terminology

* Contributor – A member of the sample; identified by a unique identifier
* Record – A set of values for each contributor and period
* Target Period – The period currently undergoing data validation
* Target Variable – The variable of interest that the method is working on, the total or components as determined by the Amend Total variable
* Target Record – A contributor's record in the target period
* Predictive Variable – A value used as a predictor for a contributor's target variable
* Predictive Record – The record containing a contributor's predictive value
* Predictive Period – The period containing predictive records; defined relative to the target period.
* Auxiliary variable – The variable used as a predictor for a contributor’s target variable, where the predictive value is not available
* Responder – A contributor who has responded to the survey within a given period

## 3.0 Technical Assumptions

* Predictive and auxillary variables are well correlated with the target variable
* At least one of the target or auxillary variables must be populated
* Thresholds determine the need for correction
* The method can only observe only one set of components and total at any given time
* Target period values exist

## 4.0 Data records

The following field types will be present in the input and output records, for more details see the methodology specification.

### 4.1 Input records

* Unique Identifier – Any e.g. Business Reporting Unit
* Period – String in "YYYYMM" format
* Total Variable – Target period total, numeric – nulls allowed
* Components Variable – Corresponding list of Total variable's components, numeric – nulls allowed
* Amend Total – Select whether Total Variable should be automatically corrected, Boolean. FALSE = correct components, TRUE = correct total
* Predictive Variable – Previous or current period total, numeric
* Predictive Variable Period – String in “YYYYMM” format
* Auxiliary Variable – optional, numeric – nulls allowed

### 4.2 Output records 

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* Absolute Difference – Numeric, nulls allowed
* Low Percent – Numeric, nulls allowed
* High Percent – Numeric, nulls allowed
* Final Total Variable – Numeric
* Final Components Variable – Numeric
* TCC Marker – To indicate the result of the Totals Components Correction method, string
## 5.0 Process Overview

The following is a key of useful formula definitions/assumptions

* y_(total, t) => total
* y_{total, predictive} => predicted (the previous/comparison total which can come from a good return or an imputed value)
* A => do_amend_total
* x_{absolute} => threshold_absolute
* x_{percent} => threshold_percent
* y_{derived} => component sum
* y_{c} => is the individual component (e.g c_1 would be y_{1})

We start with a data record which is parsed to our process.

### 5.1 Validate Data Input (Stage 1)

We firstly, check to see if total, components, predictive variable, auxillary variable (if specified), absolute difference threshold (if specified) or percentage difference threshold (if specified) in the data record is not a number value. If any of these values are not a number then we return an error message '{var or vars} not a Number' and stop the method.

Then, we must see if 

```
    x_{absolute} ≠ None
```
 and

```
    x_{percent} ≠ None
```

holds. If this is ```True``` we flag an exception with the message "One or both of absolute/percentage difference thresholds must be specified" and the method stops.

If it is ```False``` then we continue to stage 2.

### 5.2 Check Predictive Errors (Stage 2)

The next step is to check if the predictive value is ```None```. If this is the case, then the auxillary value is used instead. This only applies if the user has provided an auxillary value. 

Hence, if the auxiliary value is also ```None``` then we use the TCC marker = S and write the output. If we have a value then we set the predictive value equal to the auxiliary value and go to the next stage.

### 5.2 Check Zero Errors (Stage 3)

We must now check zero error conditions. 

To do this our first step is to verify if the following is true.


```
    y_{derived} = 0
```
and
```
    y_{total, predictive} > 0 
```

If this is the case then we have TCC Marker = S and the method stops. We then write an output.

If however the above is false we move onto stage 4

### 5.3 Check Sum(components) and Predictive Value (Stage 4)

Stage 4 is where we check the sum of the components and the predictive value. 

The initial part requires us to see if the following is true


```
    |y_{total, predictive} - y_{derived}| = x_{absolute}.
```

If this is the case then we store the absolute difference for use in the output.

We now determine if

```
    y_{1, t} + ... + y_{n, t} = y_{total, predictive}
```

If true, we have a TCC Marker = N meaning we have no correction and the method stops with an output written.

Else we move onto the next stage which is where we determine an error detection method.

### 5.4 Determine Error Detection Method (Stage 5)

If 

```
    x_{absolute} ≠ None
```

 we go to stage 5a found below. Else we go to section 5.4.3.

### 5.4.1 Check Absolute Difference Threshold (Stage 5a)

The absolute difference between the target period total and the components less than or equal to the absolute difference threshold can be visualised using the following formula. 

```
    x_{absolute} <= |y_{total, t} - y_{total, predictive}|
```

If this is true we mark 

```
    satisfied = TRUE
``` 

else we mark 

```
    satisfied = FALSE
```

we return the satisfied boolean and go to stage 6 in section 5.5 (if boolean is ```TRUE```), if boolean is ```FALSE``` we move to section 5.4.3.

Note: Before we leave this stage this point we need to check that zero error condition 3 is satisfied

```
    y_{total, t} = 0
```

,

```
y_{derived, t} > 0
```

and amend total = ```True```.

If so apply the correction to override the components with zeros if the difference observed is within the tolerances determined by the detection method.

We then set TCC = M for manual checking.
### 5.4.2 Check Percentage Difference Threshold (Stage 5b)

If the total for the predictive period is within the low and high percentage as shown by the formula below 

```
    y_{derived} - (y_{derived} * x_{percent}) =< y_{total, predictive} =< y_{derived} + (y_{derived} * x_{percent})
```

If this is true we mark 

```
    satisfied = TRUE
``` 

else we mark 

```
    satisfied = FALSE
```

we return the satisfied boolean and go to stage 6 in section 5.5 (if boolean is ```TRUE```), if boolean is ```FALSE``` we return a TCC = M marker meaning manual editing is required and write an output.

### 5.4.3 Percentage Difference Threshold

This stage is to check if 

```
    x_{percent} ≠ None
```

If this is true we run stage 5b (see section 5.4.2 above), else we check that

```
    x_{absolute} ≠ None
```
or

```
    x_{percent} ≠ None
```

If it is true we return a TCC = M marker meaning manual editing is required and we write an output.

### 5.5 Error Correction (Stage 6)

This section covers the error correction aspect of the process.

At this point we rely on the amend value `do_amend_total`. This is where `y_derived` or `y_total` are > 0.

If 

```
    do_amend_total = TRUE
```

Then the total is automatically corrected for the target period to equal the sum of the components in the target period.

Hence we set the following

```
    y_{derived} = y_{total, t}
```

and the final values for all the components match their originals. 

We would then have a TCC = T marker and write the output.

However, if

```
    do_amend_total = FALSE
```

Then the components are automatically corrected for the target period to equal the total in the target period.

```
    y_{c} = (\frac{y_{c} + \dots + y_{derived}}) * y_{total, predictive}
```

and the final values for all the components match their originals. 

If any of the components are not corrected when needed then we repeat the formula above. Else we set

```
    y_{total, t} = y_{total, predictive}
```

and return TCC = C marker and write the output.

If we have the situation as described above we will need to check the fourth zero case. This can be achieved by checking if

```
    y_{total, t} = 0
```

,

```
y_{derived, t} > 0
```

and the amend total is ```False```.

If so we override the components with zeros if the difference observed is within the tolerances determined by the detection method.

We then set TCC = M for manual checking.