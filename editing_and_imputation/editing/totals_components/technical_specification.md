# Totals and Components Specification

## 1.0 Meta

* Support Area - Methodology - Totals and Components
* Support Contact - <Kirsty.Mackenzie@ons.gov.uk>(to be changed to teams email)
* Method Theme - Totals and Components
* Method Classification - Totals and Components
* Status - Planning 
## 2.0 Description

The automatic editing method for totals and components correction is currently used in ONS business surveys to ensure fixed relationships between variables are satisfied. For example, when a total (e.g., total employment) is collected along with the component breakdown (e.g., full-time male, full-time female, part-time male, part-time female).

The primary use of the method is to automatically detect and correct errors in respondent data where fixed relationships have not been satisfied to improve the efficiency of the editing process, reduce the burden on respondents and survey validators and improve overall data quality.

This method can also be used to ensure fixed relationships between variables are satisfied in other data types such as imputed data to improve overall data quality.

### 3.0 Terminology

* Contributor – A member of the sample; identified by a unique identifier.
* Record – A set of values for each contributor and period.
* Target Period – The period currently undergoing data validation.
* Target Variable – The variable of interest that the method is working on, the total or components as determined by the Amend Total variable.
* Target Record – A contributor's record in the target period.
* Predictive Variable – A value used as a predictor for a contributor's target variable.
* Predictive Record – The record containing a contributor's predictive value.
* Predictive Period – The period containing predictive records; defined relative to the target period.
* Auxiliary variable – The variable used as a predictor for a contributor’s target variable, where the predictive value is not available
* Responder – A contributor who has responded to the survey within a given period.

## 4.0 Technical Assumptions

* Predictive and auxillary variables are well correlated with the target variable
* At least one of the target or aux variables must be populated.
* Thresholds determine the need for correction
* The method can only observe only one set of components and total at any given time
* Target period values exist

## 5.0 Data records

The following field types will be present in the input and output records, for more details see the methodology spec.

### 5.1 Input records

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* Total Variable – Target period total, numeric – nulls allowed
* Components Variable – Corresponding list of Total variable's components, numeric – nulls allowed
* Amend Total – Select whether Total Variable should be automatically corrected, Boolean. FALSE = correct components, TRUE = correct total
* Predictive Variable – Previous or current period total, numeric
* Predictive Variable Period – String in “YYYYMM” format
* Auxiliary Variable – optional, numeric – nulls allowed

### 5.2 Output records 

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* Absolute Difference – Numeric, nulls allowed
* Low Percent – Numeric, nulls allowed (see 7.1.2)
* High Percent – Numeric, nulls allowed (see 7.1.2)
* Final Total Variable – Numeric
* Final Components Variable – Numeric
* TCC Marker – To indicate the result of the Totals Components Correction method, string
## 7.0 Process Overview

The following is a key of useful formula definitions/assumptions

* `y_{total, t}` is a target period total value at time `t`
* The sum of the target period total `y_{sum, t}`
* `y_{total, predictive}` is the target period total for the predictive period
* `A` is the amend total which is the total in the total in the target period
* `x_{absolute}` is the predefined threshold for the absolute difference
* `x_{percent}` is the predefined percentage threshold represented as a decimal
* Sum of components at time `t` is to `y_{derived}`

We start with a data record which is parsed to our process.

### 7.1 Stage 1

We firstly, check to see if the target period total is equal to the sum of target period components (see 8.0.1). If this is true then no correction is applied.

### 7.2 Stage 2 

The next step is to check if the predictive value is missing. If this is the case, then the auxillary value is used instead. This only applies if the user has provided an auxillary value. Hence, if this is not present then we stop the method and use the TCC marker = S

We now approach the section of our process which is threefold. If the first two of the following conditions are not met along with the third breaking error detection 2 (see 8.0.4), then we require manual editing (notification to the user needs to be presented). Else we proceed to the next stage.

The conditions are as follows

Condition 1 - Is the absolute difference between the target period total and the components less than or equal to the absolute difference threshold (see 8.0.2).

Condition 2 - Is the target period total within or equal to the absolute percentage of the sum of the components (see 8.0.3)

Condition 3 - Is the absolute difference between the target period total and the components greater than to the absolute difference threshold and the total is within or equal to the absolute percentage of the sum of the components (see 8.0.4)

We now apply the correction (see 9.0).

### 8.0 Error detection

Below is a list of formulas to assist with error detection and understanding the above process overview.

### 8.0.1 Initial error handling 

The left hand side of this formula shows the sum of the target period components and the right hand side shows the total for time 't'. We expect them to be the equal.

```
    y_{1, t} + ... + y_{n, t} = y_{total, t}
```
### 8.0.2 Error detection condition 1

The absolute difference between the target period total and the components less than or equal to the absolute difference threshold can be visualised using the following formula. 

```
    |y_{total, t} - y_{total, predictive}| =< x_{absolute}.
```
If the predictive value is missing you may need to use an auxillary value. If this value is not present then we need to use the marker TCC Marker = S

Meanwhile, if the difference is greater then the thresholds, then TCC Marker = M.

### 8.0.3 Error detection condition 2

The target period total within or equal to the absolute percentage of the sum of the components can be seen below.

```
    y_{derived} - (y_{derived} * x_{percent}) =< y_{total} =< y_{derived} + (y_{derived} * x_{percent})
```

### 8.0.4 Error Detection condition 3

The following is for condition 3  where if satisfied. Then we continue to the next stage (i.e checking if the target period total or sum of components is  equal to zero). Else we require manual intervention.

The condition is satisfied if 

```
    |y_{total, t} - y_{total, predictive}| <= x_{absolute}.
```

or if

```
    |y_{total, t} - y_{total, predictive}| > x_{absolute}.
```

    and 
```
    y_{derived} - (y_{derived} * x_{percent}) =< y_{total} =< y_{derived} + (y_{derived} * x_{percent})
```

## 9.0 Error Correction

This section covers the error correction aspect of the process.

The stage is firstly dependent on the target period total or the sum of the components being equal to zero. If this is not true, then we go to 9.0.1 else we go to stage 9.0.2.

### 9.0.1 Stage 3a

At this point we rely on the amend value `A`.

If 

```
    A = TRUE
```

Then the total is automatically corrected for the target period to equal the sum of the components in the target period.

```
    A = FALSE 
```

Then the components are automatically corrected for the target period to equal the total in the target period.

### 9.0.2 Stage 3b

There are now two conditions the first one is if

```
    y_{derived} = 0
```
and
```
    y_{total, t} > 0 
```
If this is true we stop the correction, returning TCC Marker = S.

If this is not true we check that 

```
    y_{derived} > 0
```
and
```
    y_{total, t} = 0 
```

If this is true and we have the amend value `A` is also true then we return TCC = T. In other words the totals correction is applied.

If this is false we mark TCC = C. In other words the sum correction is applied.

The process then ends.
