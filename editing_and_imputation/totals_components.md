# Totals Components Correction Specification
## 1.0 Meta 
* Support area – Methodology – Processing, Editing & Imputation 
* Support contact – Editing.and.Imputation.expert.group@ons.gov.uk
* Method theme – Automatic Editing 
* Method classification – Totals Components Correction 
* Status – draft, not tested 


## 2.0 Terminology
* Contributor – A member of the sample; identified by a unique identifier. 
* Record – A set of values for each contributor and period. 
* Target Period – The period currently undergoing data validation. 
* Target Variable – The variable of interest that the method is working on, the total or components as determined by the Amend Total variable. 
* Target Record – A contributor's record in the target period. 
* Predictive Value – A value used as a predictor for a contributor's target variable. 
* Predictive Record – The record containing a contributor's predictive value. 
* Predictive Period – The period containing predictive records; defined relative to the target period. 
* Auxiliary variable – The variable used a predictor for a contributor’s target variable, where the predictive value is not available (i.e., where the contributor was not sampled in the predictive period). This should only be populated if the user has a suitable variable that they wish to use if the predictive value is not available. Else, the method should not run. 
* Responder – A contributor who has responded to the survey within a given period. 

## 3.0 Introduction
The totals/components correction method is currently used in ONS business surveys to ensure fixed relationships between variables are satisfied. For example, when a total (e.g., total employment) is collected along with the component breakdown (e.g., full-time male, full-time female, part-time male, part-time female).  

The primary use of the method is to automatically detect and correct errors in respondent data where fixed relationships have not been satisfied to improve the efficiency of the editing process, reduce the burden on respondents and survey validators and improve overall data quality.  

This method can also be used to ensure fixed relationships between variables are satisfied in other data types such as imputed data to improve overall data quality. 

The method uses two cleared consecutive periods of data for a given respondent: the target and predictive periods. 

## 4.0 Assumptions
* Target value and predictive value are well correlated  
* Target value and register based auxiliary value, if required, are well correlated 
* Thresholds set are a good indication of whether a value should be corrected  
* The method can only observe and satisfy one fixed relationship at a time (i.e., only one set of components and total)
* Both the total value and corresponding components are populated for the target period 
* 

## 5.0 Method Input and Output
### 5.1 Input Records
Input records must include the following fields of the correct types:  

* Unique Identifier – Any e.g., Business Reporting Unit  
* Period – String in "YYYYMM" format  
* Total Variable – Target period total, numeric – nulls allowed  
* Components Variable – Corresponding list of Total variable's components, numeric – nulls allowed 
* Amend Total – Select which variable should be amended, Boolean. 0 = False, Components amended and 1 = True, Total amended 
* Predictive Variable – Previous period total, numeric  
* Predictive Variable Type – e.g., return, impute, etc., numeric   
* Auxiliary Variable – Register based variable – optional, nulls allowed 
* Threshold – Numeric

### 5.2 Output Records
Output records shall always contain the following fields with the following types:  

* Unique Identifier – Any e.g., Business Reporting Unit  
* Period – String in "YYYYMM" format  
* TCC Marker – To indicate the result of the Totals Components Correction method, string 
* Final Total Variable – Numeric  
* Final Components Variable – Numeric  

Fields of type "Any" shall be of the same type as the corresponding input fields as the values shall be the same in both input and output records. 

The TCC marker must be one of the following: 

* T = Total corrected 
* C = Components corrected 
* N = No correction required, i.e., the total is equal to the components sum. 
* M = Manual editing required. This marker will identify contributors where the discrepancy between the total and component is deemed too large for automatic correction

## 6.0 Overall Method
This method firstly identifies if any fixed relationships (i.e., totals/components) have not been satisfied. If so, the method then calculates the absolute difference between the sum of the components for a given question and its corresponding total, collected separately, for the previous period. If this difference is non-zero and less than a specified threshold, then either the total or components variables will be automatically corrected, depending on which is selected. If the absolute difference is greater than or equal to a threshold and the method is being applied to returned data, then the responder is recontacted for confirmation. If method is applied to data that is not respondent data e.g., imputed data, then the threshold can be set to a maximum value, allowing the method to automatically correct all cases where the total does not equal the sum of the components.  

The rationale for automatically correcting totals/components can vary by data type. For example, with respondent data, it may be preferred to only automatically correct data if there is a small difference between the predictive and target variables in the target period as it is a cost-effective approach to data editing. However, if a difference greater than or equal to a set threshold is observed between the predictive variable and the target variable in the target period, then it may be preferred to not automatically correct data in favour of re-contacting the respondent to correct the data directly. 

As mentioned above, thresholds can conversely be set to be very large, such that data will always be automatically edited if they have met the required criteria. This approach can be applied when it is not appropriate to re-contact a respondent (e.g., apply to imputed data).

If the predictive period’s data is missing, then the method is not applied, unless it is appropriate to use a register-based variable that is well correlated with the target variable. The register based auxiliary variable should not be read into the data if the user does not require it. 

### 6.1 Error Detection
If a total for the target period does not equal the sum of its corresponding components in the target period, then the method checks if the absolute difference between the sum of components for the target period and the total for the predictive (previous) period is less than a set threshold.  

The predictive period value can either be a cleaned return, an impute or a construction. If this data is not available, then an auxiliary register-based variable should only be used when it is populated by the user. For example, ONS business surveys use a register-based auxiliary variable only for surveys with annual periodicity, when there is no predictive variable available. The predictive variable should take priority over the register-based auxiliary. Otherwise, the method should not run if there is no previous period data available. 

### 6.2 Error Correction
If the condition is satisfied, i.e., the absolute difference is less than the threshold, then the target variable will be corrected, depending on which priority variable has been selected: 

1. If Target Variable 1 is selected, then the total is automatically corrected for the target period to be equal to the sum of the components in the target period.  
2. If Target Variable 2 is selected, then the sum of the components is automatically corrected for the target period to be equal the total in the target period. 

However, if the absolute difference is greater than or equal to the threshold, then no correction is applied, and the responder is recontacted for confirmation, if the target variable is a returned value. 

### 6.3 Method Error Handling
In the case of the method experiencing processing issues, the method shall not result in any output records. Instead, a suitable error description shall be emitted. 

## 7.0 Calculations - EQUATIONS NOT WORKING AT PRESENT
### 7.1 Error Detection Calculation
The method checks whether the following is satisfied for a given contributor: 

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=\large |(y_{1, t} + \dots + y_{n, t}) - y_{total, t-1}| < x">
</p>

Alt: 
<p align="center">
 <i>|(y<sub>1, t</sub> + ... + y<sub>n, t</sub>) - y<sub>total, t-1</sub>| < x</i>
</p>
 
 ```asciimath
|(y_{1, t} + \dots + y_{n, t}) - y_{total, t-1}| < x
```

Where *y<sub>1, t</sub>* to *y<sub>n, t</sub>* are the *n* corresponding components of the total *y<sub>total, t</sub>*, *y<sub>total, t-1</sub>* is the total for the predictive period and *x* is the predefined threshold.  

If the predictive value is missing, then *y<sub>total, t-1</sub>* should be replaced with the register based auxiliary variable, if required by the user.  

### 7.2 Error Correction Calculation
If the above is satisfied and the priority variable is the total (target variable 1), then: 

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=\large y_{total, t} = y_{1, t} + \dots + y_{n, t}">
</p>

Alt:
<p align="center">
 <i>y<sub>total, t</sub> = y<sub>1, t</sub> + ... + y<sub>n, t</sub></i>
</p>

```asciimath
y_{total, t} = y_{1, t} + \dots + y_{n, t}
```  

If the condition in section 7.1 is satisfied and the priority variable is the list of components (target variable 2), then: 

<p align="center">
 <img src="https://render.githubusercontent.com/render/math?math=\large y_{1, t} + \dots + y_{n, t} = y_{total, t}">
</p>

Alt:
<p align="center">
 <i>y<sub>1, t</sub> + ... + y<sub>n, t</sub> = y<sub>total, t</sub></i>
</p>

```asciimath
y_{1, t} + \dots + y_{n, t} = y_{total, t}
```
 
Where *y<sub>1, t</sub>* to *y<sub>n, t</sub>* are prorated to equal *y<sub>total, t</sub>*. 

## 8.0 Worked Example 
In this example, the contributor is required to provide a total value and its corresponding four component variables. The user has opted to use auxiliary data in the absence of the preferred predictive variable.  

| Contributor | Priority Variable | Target Variable 1 | Target Variable 2 | Predictive Variable | Auxiliary Variable | Threshold | Correction Applied | Corrected Priority Variable |
|---|---|---|---|---|---|---|---|---|
| 1 | 1 | 50 | 14, 20, 17, 13 | 54 | N/A | 10 | N | N/A |
| 2 | 2 | 175 | 78, 64, 17, 8 | 180 | N/A | 20 | Y | 82, 67, 18, 8 |
| 3 | 1 | 11 | 10, 5, 4, 0 |   | 15 | 5 | Y | 19 |
| 4 | 1 | 30 | 12, 8, 14, 9 | 38 | N/A | 99999 | Y | 43 |

Contributor 1: The absolute difference between the sum of the components, target variable 2, and the predictive variable is equal to the threshold. Therefore, no automatic correction is applied, and the contributor is recontacted for confirmation. 

Contributor 2: The absolute difference between the sum of the components, target variable 2, and the predictive value is less than the threshold. Therefore, the correction is applied to target variable 2, the selected priority variable. The proportions of target variable 2 are corrected to equal target variable 1.  

Contributor 3: The predictive value is missing but the user has populated the optional auxiliary variable. The absolute difference between the sum of the components, target variable 2, and the auxiliary variable is less than the threshold. Therefore, target variable 1, the selected priority variable, was corrected to equal the sum of target variable 2. 

Contributor 4: The absolute difference between the sum of the components, target variable 2, and the predictive value is less than the threshold which has been set to the maximum value. Therefore, target variable 1, the selected priority variable, was corrected to equal the sum of target variable 2. 

## 9.0 Future Enhancements 
Future iterations of this method can be extended to other variable constrains and relationship such as inequalities (e.g., exports must be less than or equal to the total turnover).  

If multiple variable constraints must be applied to the same variable, then a priority order can be established.  
