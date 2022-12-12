# Thousand Pounds Correction Specification
## 1.0 Meta 
* Support area – Methodology – Processing, Editing & Imputation 
* Method theme – Automatic Editing 
* Status – draft, not tested 


## 2.0 Terminology
* Contributor - A member of the sample; identified by a unique identifier.
* Record - A set of values for each contributor and period.
* Target Period - The period currently undergoing data validation.
* Principal Variable – Variable that the method is working on and will determine if the remaining monetary variables, if any, will be automatically corrected.  
* Target Variable(s) – List of all monetary variables that may be automatically corrected, excluding the principal variable.
* Target Record - A contributor's record in the target period.
* Predictive Variable - The corresponding value used as predictor for the principal variable for each contributor.
* Predictive Variable Type – Type of data e.g., response, impute etc. Not required to run the method but required for method review purposes. 
* Predictive Record - The record containing a contributor's predictive value.
* Predictive Period - The period containing predictive records; defined relative to the target period.
* Auxiliary variable - The variable used as a predictor for a contributor's principal variable, where the predictive value is not available for a given contributor (i.e. where the contributor was not sampled in the predictive period). This should only be populated if the user has a suitable variable that they wish to use if the predictive value is not available. Else, the method should not run.
* Responder - A contributor who has responded to the survey within a given period.


## 3.0 Introduction
The thousand pounds correction is commonly used across business surveys. It is a generic rule that uses user defined thresholds to automatically detect and correct thousand pounds errors. This is when the respondent should have reported values in thousands of pounds but has reported in actual pounds e.g., returned a value of £56,000 instead of correctly submitting 56.


## 4.0 Assumptions
* A ratio inside of the upper or lower thresholds is due to a thousand pounds error 
* If the principal variable is found to be a thousand pounds error, then it is assumed that all other monetary values are also thousand pounds errors 
* The principal variable and predictive value are well correlated and are intended to be reported in the same denomination (i.e., thousand pounds) 
* The principal variable and auxiliary value are well correlated and intended to be reported in the same denomination (i.e., thousand pounds) 
* The auxiliary variable is known and available for all contributors processed by the method 
* Thresholds set are a good indication of whether a value should be corrected  


## 5.0 Method Input and Output - need DST input to finalise
### 5.1 Input Records
Input records must include the following fields of the correct types: 

* Unique Identifier – Any e.g., Business Reporting Unit 
* Period – String in "YYYYMM" format 
* Principal Variable – Single variable, numeric 
* Target Variable(s) – Can be a list, numeric – nulls allowed - COLUMN PER TARGET VAR, RATHER THAN A LIST? OR DOES WRANGLER HANDLE THIS IF LIST READ IN?
* Predictive Variable – Single variable, numeric – nulls allowed if unavailable 
* Predictive Variable Type – e.g., return, impute, etc., numeric – nulls allowed 
* Auxiliary Variable – Register based variable – optional, numeric – nulls allowed 
* Upper Limit – Single variable, numeric, must be greater than Lower Limit
* Lower Limit – Single variable, numeric, must be less than Upper Limit  

Unless otherwise noted, fields must not contain null values. All other fields shall be ignored. 

The method requires at least one of the following as the previous period predictive variable, in the given priority order: 
1. Returned, cleaned response
2. Imputed or constructed value 
3. Auxiliary variable e.g., registered annual turnover 

Although predicted and auxiliary are both optional, at least one must be provided for the method to work. Else a method error is emitted. 
Note that the predictive variable is indirectly defined as the principal variable in the predictive period.

### 5.2 Output Records
Output records shall always contain the following fields with the following types: 

* Unique Identifier – Any e.g., Business Reporting Unit 
* Period – String in "YYYYMM" format 
* Original Principal Variable – Numeric
* Original Target Variables – Can be a list, numeric – nulls allowed
* Final Principal Variable – Numeric 
* Final Target Variables – Can be a list, numeric – nulls allowed 
* TPC Marker –  To indicate the result of the Thousand Pounds Correction method, string 
* Error Description – String

Fields of type "Any" shall be of the same type as the corresponding input fields as the values shall be the same in both input and output records. 

The TPC Marker must be one of the following: 
* C = Correction applied 
* N = No correction applied 
* E = Method error


## 6.0 Method

### 6.1 Overall Method
A principal variable must be specified as a priority indictor for whether a thousand pounds error has occurred. The method checks the principal variable for a given contributor to determine whether the ratio of the principal variable by the predictive variable is within a fixed set of upper and lower thresholds. If the ratio lies within these thresholds, then a thousand pounds correction is automatically applied to the principal variable and the rest of the target variables, if any, are automatically corrected.

If the predictive period’s data is missing, then the method is not applied, unless it is appropriate to use a register-based variable that is well correlated with the target variable. The register based auxiliary variable should not be read into the data if the user does not require it. 

### 6.2 Error Detection
The error detection calculation is applied to each contributor and calculates the ratio of the principal variable and predictive variable at the contributor level. The principal variable is the current period data value, and the predictive variable is the corresponding previous period data value, if the contributor was previously sampled. Previous period data can be a clean response, imputed or constructed data value. If there is no predictive value available (i.e., the contributor was not sampled in the previous period), then an auxiliary variable such as IDBR selection turnover can be used if required by the user. Note that the auxiliary variable should be recorded in the same denomination as the target variable. 

If the ratio is within the predefined upper and lower thresholds, then a thousand pounds error is detected.  

If the previous period’s value is zero, then the method does not continue. A thousand pounds error is neither detected nor corrected. 

### 6.3 Error Correction
A detected thousand pounds error will be automatically corrected by dividing the principal variable (i.e., suspicious returned value) by 1000 then rounding to the nearest whole number.  

All other monetary questions, the target variables excluding the principal variable, on the form will be automatically corrected as described without checking the returned or corresponding previous values. 

### 6.4 Method Error Handling
In the case of the method experiencing processing issues, the method will still create output tables with a suitable error description in the table. 


## 7.0 Calculations
### 7.1 Error Detection Calculation 
If a predictive value for the principal question *q* is available for contributor *i* at time *t-1*, then a thousand pounds error is detected if the following ratio lies within the defined lower or upper thresholds, *L<sub>Lower</sub>* or *L<sub>Upper</sub>*.

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=\large L_{Lower} < \frac{y_{i, q, t}}{y_{i, q, t-1}} < L_{Upper}">
</p>

```asciimath
L_{Lower} < \frac{y_{i, q, t}}{y_{i, q, t-1}} < L_{Upper}
```

Where *y<sub>i, q, t</sub>* is the returned value for the principal question *q* and *y<sub>i, q, t-1</sub>* is its corresponding previous period value. Previous period data may be a clean response, impute or constructed data. 

If the ratio lies within, and not equal to, the limits, then a thousand pounds error is detected; else a thousand pounds error has not been identified. 

 

If a cleared, predictive value does not exist and the user has chosen to input an auxiliary variable, then the error detection calculation is as follows:  

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=\large L_{Lower} < \frac{y_{i, q, t}}{x_{i, q, t}} < L_{Upper}">
</p>

```asciimath
L_{Lower} < \frac{y_{i, q, t}}{x_{i, q, t}} < L_{Upper}
```

Where *x<sub>i, q, t</sub>* is the auxiliary register based selected turnover for contributor *i* and appropriately converted to the same denomination as the principal variable, if necessary.

If the ratio lies within the limits, then a thousand pounds error is detected; else a thousand pounds error has not been identified. 


### 7.2 Error Correction Calculation
A detected error for question *q* is automatically corrected for contributor *i* at time *t* by: 

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=\large y^{*}_{i, q, t} =  < \frac{y_{i, q, t}}{1000}">
</p>

```asciimath
y^{*}_{i, q, t} =  < \frac{y_{i, q, t}}{1000}
```

Where *y<sup>\*</sup><sub>i, q, t</sub>* is the corrected value and rounded to the nearest whole number.  

Once the principal target variable has been corrected, all other monetary values, the remaining target variables, for a given contributor will be automatically corrected by the same method as described above. 


## 8.0 Worked Example
For this example, let the lower and upper limits equal 250 and 1350 respectively as these are currently the best practice parameters used in ONS business surveys. The user has opted to use auxiliary data in the absence of the preferred predictive variable.  

| Contributor | *y<sub>i, q, t</sub>* | *y<sub>i, q, t-1</sub>* | *x<sub>i, q, t</sub>* | Detection Ratio | Pass/Fail |*y<sup>\*</sup><sub>i, q, t</sub>*|
|---|---|---|---|---|---|---|
| 1 | 2000M | 2M | N/A | 1000 | Fail | 2M |
| 2 | 5000M | 100M | N/A | 50  | Pass | 5000M |
| 3 | 1350M |  | 1M | 1350 | Pass | 1350M |
| 4 | 1250M | 0 | N/A | N/A | N/A | 1250M |

Contributor 1 shows the detection method using the target and predictive variables. The ratio is within the thresholds and therefore an error is detected, and a correction is applied. 

Contributor 2 shows the detection method using the target and predictive variables. The ratio is outside the thresholds and therefore an error is not detected, and a correction is not applied. 

Contributor 3 shows the detection method using the target variable, but the predictive variable is missing so the auxiliary variable is used. The ratio is equal to the upper threshold and therefore an error is not detected, and a correction is not applied. 

Contributor 4 has both the target and predictive variables available, but the predictive variable is equal to 0 and therefore the method is not applied. 


## 9.0 Future Enhancements 

At present, method thresholds are fixed across all surveys, size bands and industry. A future enhancement could allow different thresholds to be set for cases that as particular industries that are more likely to experience higher fluctuations or sporadic data. The enhancement could also allow the method to detect and correct for cases where question values are requested in pounds but have incorrectly been returned in thousands.  
 
