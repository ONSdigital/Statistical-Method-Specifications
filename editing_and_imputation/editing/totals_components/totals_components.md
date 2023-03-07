# Mean of Ratios Specification

## 1.0 Description

The automatic editing method for totals and components correction is currently used in ONS business surveys to ensure fixed relationships between variables are satisfied. For example, when a total (e.g., total employment) is collected along with the component breakdown (e.g., full-time male, full-time female, part-time male, part-time female).

The primary use of the method is to automatically detect and correct errors in respondent data where fixed relationships have not been satisfied to improve the efficiency of the editing process, reduce the burden on respondents and survey validators and improve overall data quality.

This method can also be used to ensure fixed relationships between variables are satisfied in other data types such as imputed data to improve overall data quality.

For a workflow of the process please see the [documentation]().

## 2.0 Technical Assumptions

* Target value, predictive and register based auxiliary value, if required, are well correlated
* Thresholds determine the need for correction
* The method can only observe only one set of components and total at any given time
* Target period values exist

## 3.0 Additional outputs

In addition to the fields and records specified in the outputs section of the methodology, the following field types will be present in the output records.

* Percentage difference
* Error correction calculation

## 4.0 Error detection and handling calculations

### 4.1 Error detection

If the formula shown below is satisfied then the method stops. Otherwise we continue.

* `y` be the component n value at time t

```
    y_{1, t} + ... + y_{n, t} = y_{total, t}
```

### 4.1.2 Absoulte Difference

If the sum of 'y' is not equal to zero and the above was true. Then we apply the following 

```
    |(y_{1, t} + ... + y_{n, t}) - y_{total, predictive}| =< x_{absolute}.
```
If the predicitve value is missing you may need to use an auxillary (see workflow).

### 4.1.3 Percentage difference

If 4.1 is not true and:

* Sum of 'y' is y_derived 
* y_derived does not equal 0

```
    y_{derived} - (y_{derived} * x_{percent}) =< y_{total, predictive} =< y_{derived} + (y_{derived} * x_{percent})
```

### 4.1.4 Combined error detection

Using a combination of the two above is possible starting with absolute difference. I.e if 

```
   |(y_{1, t} + ... + y_{n, t}) - y_{total, predictive}| =< x_{absolute}
```

Then the method stops and the correction is applied.

However if


```
    x_{absolute} > |(y_{1, t} + ... + y_{n, t}) - y_{total, predictive}|
```

Then the percentage check is applied and we flag the contributor for manual checking.

### 4.2 Error correction calculation

Assuming the above are satisfied and

* A is the amend value

If A = 0 

```
    y_{1, t} + ... + y_{n, t} = y_{total, t}
```

Else if A = 1 

```
    y_{total, t} = y_{1, t} + ... + y_{n, t}
```

Else if A is neither of these values we flag this data for manual correction.
