# Mean of Ratios Specification

## 1.0 Description

The automatic editing method for totals and components correction is currently used in ONS business surveys to ensure fixed relationships between variables are satisfied. For example, when a total (e.g., total employment) is collected along with the component breakdown (e.g., full-time male, full-time female, part-time male, part-time female).

The primary use of the method is to automatically detect and correct errors in respondent data where fixed relationships have not been satisfied to improve the efficiency of the editing process, reduce the burden on respondents and survey validators and improve overall data quality.

This method can also be used to ensure fixed relationships between variables are satisfied in other data types such as imputed data to improve overall data quality.

## 2.0 Technical Assumptions

* Target value, predictive and register based auxiliary value, if required, are well correlated
* Thresholds determine the need for correction
* The method can only observe only one set of components and total at any given time
* Target period values exist

## 3.0 Additional outputs

In addition to the fields and records specified in the outputs section of the methodology, the following field types will be present in the output records.

* Percentage difference
* Error correction calculation

## 4.0 Process Overview

The following is a key of useful forumla definitions/assumptions

*  Sum of components `y` at time `t` is `y_{derived}`
* `y_{derived}` does not equal 0
* `y_{t}` is a target period total value at time `t`
*  The sum of the target period total `y_{total, t}`
* `y_{total, predictive}` is the absolute target period total for the predictive period
* `A` is the amend value
* `x_{absolute}` is the predefined threshold for the absolute difference.
* `x_{percent}` is the predefined percentage threshold represented as a decimal.

We start with a data record which is parsed to our process.

### Stage 1

We firstly, check to see if the target period total is equal to the sum of target period components (see 4.1.1). If this is true then no correction is applied.

### Stage 2 

The next stage (stage 2) is to check if the predicitve periods data is missing form the data record. If this is the case then we muust check if the frequency of the survey is annual. If this is not the case we do not produce an putput. However if it is an annual survey we will use an auxillary variable in our detection method as our predicitve periods data.

We now apporach the section of our process which is threefold. If the first two of the following conditions are not met along with the third breaking error detection 2 (see 4.1.4), then we require manual editing (notification to the user needs to be presented). Else we proceed to the next stage.

The conditions are as followss

Condition 1 - Is the absolute difference between the target period total and the components less than or equal to the absolute difference threshold (see 4.1.2).

Condition 2 - Is the target period total within or equal to the absolute percentage of the sum of the components (see 4.1.3)

Condition 3 - Is the absolute difference between the target period total and the components greater than to the absolute difference threshold and the total is within or equal to the absolute percentage of the sum of the components (see 4.1.4)
### Stage 3

The next stage (stage 3) is firstly dependent on the target period total or the sum of the components being equal to zero. If this is not true then we go to stage 3a else we goto stage 3b.

### Stage 3a

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

### Stage 3b

There are now two conditions the first one is if

```
    y_{derived} = 0
    y_{total, t} > 0 
```
If this is true we stop the correction, returning TCC MArker = S.

If this is not true we check that 

```
    y_{derived} > 0
    y_{total, t} = 0 
```

If this is trueand we have the amend value `A` is also true then we return TCC = T. In other words the totals correction is applied.

If this is false we mark TCC = C. In other words the sum correction is applied.

The process then ends.

### 4.1 Error detection

Below is a list of formulas to assist with error detetcion and undertstanding the above process overview.

### 4.1.1 Inital error handling 

This left hand side of this formula shows the sum of the sum of the target period components and the right hand side shows the sum of the target period components.

```
    y_{1, t} + ... + y_{n, t} = y_{total, t}
```

### 4.1.2 Error detection condition 1

The absolute difference between the target period total and the components less than or equal to the absolute difference threshold can be visualised using the following formula. 

```
    |y_{total, t} - y_{total, predictive}| =< x_{absolute}.
```
If the predicitve value is missing you may need to use an auxillary value. If this value is not available we need to use the marker TCC Marker = M

### 4.1.3 Error detection condition 2

The target period total within or equal to the absolute percentage of the sum of the components can be seen below.


```
    y_{derived} - (y_{derived} * x_{percent}) =< y_{total} =< y_{derived} + (y_{derived} * x_{percent})
```

### 4.1.4 Error Detection condition 3


The following is for condition 3  where if satisfied. Then we continue to the next stage (i.e checking if the target period total or sum of components is  equal to zero). Else we require manual intervention.

The condition is satisfied if 

```
    |y_{total, t} - y_{total, predictive}| > x_{absolute}.
```

or if

```
    |y_{total, t} - y_{total, predictive}| =< x_{absolute}.
```

    and 
```
    y_{derived} - (y_{derived} * x_{percent}) =< y_{total} =< y_{derived} + (y_{derived} * x_{percent})
```
