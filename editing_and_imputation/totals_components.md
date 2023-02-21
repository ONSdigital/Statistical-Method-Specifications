# Totals Components Correction Specification

## 1.0 Meta

* Support area – Methodology – Processing, Editing & Imputation
* Method theme – Automatic Editing
* Status – draft, not tested

## 2.0 Terminology

* Contributor – A member of the sample; identified by a unique identifier.
* Record – A set of values for each contributor and period.
* Target Period – The period currently undergoing data validation.
* Target Variable – The variable of interest that the method is working on, the total or components as determined by the Amend Total variable.
* Target Record – A contributor's record in the target period.
* Predictive Variable – A value used as a predictor for a contributor's target variable.
* Predictive Record – The record containing a contributor's predictive value.
* Predictive Period – The period containing predictive records; defined relative to the target period.
* Auxiliary variable – The variable used as a predictor for a contributor’s target variable, where the predictive value is not available (e.g., where the contributor was not sampled in the predictive period). This should only be populated if the user has a suitable variable that they wish to use if the predictive value is not available. Else, the method should not run.
* Responder – A contributor who has responded to the survey within a given period.

## 3.0 Introduction

The automatic editing method for totals and components correction is currently used in ONS business surveys to ensure fixed relationships between variables are satisfied. For example, when a total (e.g., total employment) is collected along with the component breakdown (e.g., full-time male, full-time female, part-time male, part-time female).

The primary use of the method is to automatically detect and correct errors in respondent data where fixed relationships have not been satisfied to improve the efficiency of the editing process, reduce the burden on respondents and survey validators and improve overall data quality.

This method can also be used to ensure fixed relationships between variables are satisfied in other data types such as imputed data to improve overall data quality.

## 4.0 Assumptions

* Target value and predictive value are well correlated
* Target value and register based auxiliary value, if required, are well correlated
* Thresholds set are a good indication of whether a value should be corrected
* The method can only observe and satisfy one fixed relationship at a time (i.e., only one set of components and total)
* Both the total value and corresponding components are populated for the target period

## 5.0 Method Input and Output - TO BE FINALISED WITH DST

### 5.1 Input Records

Input records must include the following fields of the correct types:

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* Total Variable – Target period total, numeric – nulls allowed
* Components Variable – Corresponding list of Total variable's components, numeric – nulls allowed
* Detection Method – Numeric, see below
* Amend Total – Select whether Total Variable should be automatically corrected, Boolean. 0 = False (correct components), 1 = True (correct total)
* Predictive Variable – Previous or current period total, numeric
* Predictive Variable Type – e.g., return, impute, etc., numeric
* Predictive Variable Period – String in “YYYYMM” format
* Auxiliary Variable – Register based variable – optional, numeric – nulls allowed
* Absolute Difference Threshold – Numeric
* Percentage Threshold – Numeric

The Amend Total variable determines whether the Total or Components variable will be corrected through application of the method. The chosen variable is then defined as the Target Variable.

If the current period total is used as the predictive variable, then the Predictive Variable and the Total Variable are the same.

There are multiple options for the detection method. The user may choose from the following: 
1. Use absolute difference only
2. Use a percentage change only
3. Use an absolute difference, and then a percentage change

### 5.2 Output Records

Output records shall always contain the following fields with the following types:

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* Original Total Variable – Numeric
* Original Components Variable – Numeric
* Final Total Variable – Numeric
* Final Components Variable – Numeric
* TCC Marker – To indicate the result of the Totals Components Correction method, string
* Error Description

Fields of type "Any" shall be of the same type as the corresponding input fields as the values shall be the same in both input and output records.

The TCC marker must be one of the following:

* T = Total corrected
* C = Components corrected
* N = No correction required, i.e., the total is equal to the components sum
* M = Manual editing required. This marker will identify contributors where the discrepancy between the total and component is deemed too large for automatic correction.

## 6.0 Overall Method

This method firstly identifies if a given totals and components relationship has not been satisfied. If so, the method will determine whether the difference observed is within an acceptable tolerance. This tolerance is determined in three different ways:
1. If the absolute difference between the sum of the components and its corresponding total for the predictive period is less than or equal to the predefined threshold
2. If the predictive total is within an acceptable percentage of the components sum and an absolute difference is not considered
3. If the absolute difference described above is greater than the predefined threshold but the predictive total is within an acceptable percentage of the components sum

If the above is satisfied, an automatic correction will be made depending on the Amend Total variable. If the above is not satisfied and the method is being applied to returned data, then the responder is contacted for confirmation. If the method is applied to data that is not returned data e.g., imputed data, then the thresholds can be set to a maximum value, allowing the method to automatically correct all cases where the total does not equal the sum of the components.

The rationale for automatically correcting totals/components can vary by data type. For example, with respondent data, it may be preferred to only automatically correct data if there is a small difference between the predictive and target variables in the target period as it is a cost-effective approach to data editing. However, if a difference greater than a set threshold is observed between the predictive variable and the target variable in the target period, then it may be preferred to not automatically correct data in favour of re-contacting the respondent to correct the data directly.

If the predictive period’s data is missing, then the method is not applied, unless it is appropriate to use a register-based variable that is well correlated with the target variable. The register based auxiliary variable should not be read into the data if the user does not require it.

### 6.1 Error Detection

If a total for the target period does not equal the sum of its corresponding components in the target period and the components sum is greater than zero, then depending on the approach chosen, the method checks whether the differences observed are within an acceptable tolerance. This tolerance can be determined by an absolute difference, a percentage difference or a combination of both, as described in section 6.

If components sum is equal to zero and the user has selected to amend the total variable (Amend Total = 1), then the correction should not be applied.

The predictive period value can either be a cleaned return, an impute or a construction. If this data is not available, then an auxiliary register-based variable should only be used when an appropriate auxiliary variable exists, and is populated by the user. For example, when checking employment data, ONS business surveys use a register-based annual auxiliary variable only for surveys with annual periodicity, when there is no predictive variable available. The predictive variable should take priority over the register-based auxiliary. Otherwise, the method should not run if there is no predictive period data available.

### 6.2 Error Correction

If the selected condition(s) as described in section 6.1 are satisfied, then the target variable will be corrected, depending on which target variable has been selected:

1. If Amend Total = 0, then the components are automatically corrected for the target period to be equal to the total in the target period.
2.  If Amend Total = 1, then the total is automatically corrected for the target period to equal the sum of the components in the target period.

However, if the chosen conditions are not satisfied, then no correction is applied, and the responder is recontacted for confirmation, if the target variable is a returned value.

### 6.3 Method Error Handling

In the case of the method experiencing processing issues, the method shall not result in any output records. Instead, a suitable error description shall be emitted.

## 7.0 Calculations

### 7.1 Error Detection Calculations

For all error detection approaches as described in section 6, the method initially identifies contributors where the following is not satisfied for *n* components at time *t*:

$$ \large y_{1, t} + \dots + y_{n, t} = y_{total, t} $$

```asciimath
y_{1, t} + ... + y_{n, t} = y_{total, t}
```
If the above is satisfied, then no error is detected, and the method stops. Else, the error detection process will proceed depending on the chose approach.

#### 7.1.1 Absolute Difference

If the condition described in 7.1 is not satisfied and the sum of the components is not equal to zero, the method then checks whether the following is satisfied for a given contributor:

$$ \large |(y_{1, t} + \dots + y_{n, t}) - y_{total,\ predictive}| \leq x $$

```asciimath
|(y_{1, t} + ... + y_{n, t}) - y_{total, predictive}| =< x
```

Where *y<sub>1, t</sub>* to *y<sub>n, t</sub>* are the *n* corresponding components of the total *y<sub>total, t</sub>*, *y<sub>total, t-1</sub>* is the total for the predictive period and *x<sub>absolute</sub>* is the predefined threshold for the absolute difference.

If the predictive value is missing, then *y<sub>total, t-1</sub>* should be replaced with the register based auxiliary variable, if required by the user.

#### 7.1.2 Percentage Difference

Where the condition described in 7.1 is not satisfied and the sum of the components is not equal to zero, the method then checks whether the following is satisfied for a given contributor:

Let $y_{1,t} +...+ y_{n,t} = y_{derived}$, then:

$$ \large y_{derived} - (y_{derived} * x_{percent}) \leq y_{total, predictive} \leq y_{derived} + (y_{derived} * x_{percent})$$

```asciimath
y_{derived} - (y_{derived} * x_{percent}) =< y_{total, predictive} =< y_{derived} + (y_{derived} * x_{percent})
```

Where *x<sub>percent</sub>* is the predefined percentage threshold represented as a decimal.

If the predictive value is missing, then *y<sub>total, predictive</sub>* should be replaced with the register based auxiliary variable, if required by the user.

#### 7.1.3 Combined Error Detection

As described in section 6, the user may choose to use either the absolute difference or the percentage difference in isolation. If the user chooses to combine both methods, then the absolute difference check should first be applied. If the absolute difference if within the acceptable tolerance, then the method stops, and the correction is applied. If the absolute difference is greater than the tolerance, then the percentage check is applied.

If the percentage check is within an acceptable tolerance, then the correction is applied.

If the percentage check is outside of the acceptable tolerance, then the contributor is flagged for manual checking.

### 7.2 Error Correction Calculation

If the conditions described in the subsections of 7.1 are satisfied and the Amend Total variable = 0 (amend the components), then:

$$ \large y_{1, t} + \dots + y_{n, t} = y_{total, t} $$

```asciimath
y_{1, t} + ... + y_{n, t} = y_{total, t}
```

Where *y<sub>1, t</sub>* to *y<sub>n, t</sub>* are prorated to equal *y<sub>total, t</sub>*.

If the conditions described in the subsections of 7.1 are satisfied and the Amend Total variable = 1 (amend the total), then:

$$ \large y_{total, t} = y_{1, t} + \dots + y_{n, t} $$

```asciimath
y_{total, t} = y_{1, t} + ... + y_{n, t}
```

If the difference between the predictive total and the sum of the components is greater than the thresholds described in the subsections of 7.1, then the data is not automatically corrected, and the data is flagged for manual checking.
