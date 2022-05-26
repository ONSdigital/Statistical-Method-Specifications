# Thousand Pounds Correction Specification
## 1.0 Meta 
* Support area – Methodology – Processing, Editing & Imputation 
* Support contact – Editing.and.Imputation.expert.group@ons.gov.uk
* Method theme – Automatic Editing 
* Method classification –Thousand Pounds Correction 
* Status – draft, not tested 


## 2.0 Terminology
* Contributor - A member of the sample; identified by a unique identifier.
* Record - A set of values for each contributor and period.
* Target Period - The period currently undergoing data validation.
* Target Variable - The variable of interest that the method is working on.
* Target Record - A contributor's record in the target period.
* Predictive Value - A value used as a predictor for a contributor's target variable.
* Predictive Record - The record containing a contributor's predictive value.
* Predictive Period - The period containing predictive records; defined relative to the target period.
* Auxiliary variable - The variable used as a predictor for a contributor's target variable, where the predictive value is not available (i.e. where the contributor was not sampled in the predictive period).
* Responder - A contributor who has responded to the survey within a given period.


## 3.0 Introduction
The thousand pounds correction is commonly used across business surveys. It is a generic rule that uses user defined thresholds to automatically detect and correct thousand pounds errors. This is when the respondent should have reported values in thousands of pounds but has reported in actual pounds e.g., returned a value of £56,000 instead of correctly submitting 56.


## 4.0 Assumptions
* A ratio outside of the upper or lower thresholds is due to a thousand pounds error 
* Returned value (target variable) and predictive value are well correlated and are intended to be reported in the same denomination (i.e., thousand pounds) 
* Returned value (target variable) and auxiliary value are well correlated and intended to be reported in the same denomination (i.e., thousand pounds) 
* The auxiliary variable is known and available for all contributors processed by the method 
* Thresholds set are a good indication of whether a value should be corrected  


## 5.0 Method Input and Output
### 5.1 Input Records
Input records must include the following fields of the correct types: 

* Unique Identifier – Any e.g., Business Reporting Unit 
* Period – String in "YYYYMM" format 
* Question Number(s) – Numeric  
* Target Variable – Numeric, nulls allowed 
* Auxiliary Variable – Numeric 
* Auxiliary Variable Type – Numeric  
* Upper Limit – Numeric  
* Lower Limit – Numeric  

Unless otherwise noted, fields must not contain null values. All other fields shall be ignored. 

Note that the predictive variable is indirectly defined as the target variable in the predictive period.

### 5.2 Output Records
Output records shall always contain the following fields with the following types: 

* Unique Identifier – Any e.g., Business Reporting Unit 
* Period – String in "YYYYMM" format 
* Question Number(s) – Numeric 
* TPC Marker – Numeric, to indicate if thousand pounds correction made as result of the method 
* Original Target Variable – Numeric 
* Corrected Target Variable – Numeric 
* Date Changed – String in “DDMMYYYY” format 
 
Fields of type "Any" shall be of the same type as the corresponding input fields as the values shall be the same in both input and output records.


## 6.0 Method

### 6.1 Overall Method
The method checks the principal question for a given respondent whether the ratio of the target variable by the predictive variable is within a fixed set of upper and lower thresholds. If the predictive variable is not available, then an auxiliary variable is used. If the ratio lies within these thresholds, then a thousand pounds correction is automatically applied to the target variable.    

### 6.2 Error Detection
The error detection calculation is applied to each respondent and calculates the ratio of the target variable and predictive variable at the contributor level. The target variable is the current period respondent data value, and the predictive variable is the corresponding previous period data value if the contributor was previously sampled. Previous period data can be a clean response, imputed or constructed data value. If there is no predictive value available (i.e., the contributor was not sampled in the previous period), then an auxiliary variable should be used; IDBR selection turnover. Note that the auxiliary variable should be recorded in the same denomination as the target variable. 

If the ratio is within the predefined upper and lower thresholds, then a thousand pounds error is detected.  

If the previous period’s value is zero, then the method does not continue. A thousand pounds error is neither detected nor corrected. 

### 6.3 Error Correction
A detected thousand pounds error will be automatically corrected by dividing the target variable (i.e., suspicious returned value) by 1000 then rounding to the nearest whole number.  

The user has the option to allow the method to automatically correct all other monetary values as described above, if the target variable has a detected and corrected thousand pounds error. Else, all remaining values for the target record will remain unchanged.  

### 6.4 Method Error Handling
In the case of the method experiencing processing issues, the method shall not result in any output records. Instead, a suitable error description shall be emitted. 


## 7.0 Calculations
### 7.1 Error Detection Calculation 
If a predictive value for principal question q is available for responder *i* at time *t-1*, then a thousand pounds error is detected if the following ratio lies within the defined lower or upper thresholds, *L<sub>Lower</sub>* or *L<sub>Upper</sub>*.

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=\large L_{Lower} < \frac{y_{i, q, t}}{y_{i, q, t-1}} < L_{Upper}">
</p>

```asciimath
L_{Lower} < \frac{y_{i, q, t}}{y_{i, q, t-1}} < L_{Upper}
```

Where *y<sub>i, q, t</sub>* is the returned value for the principal question *q* and *y<sub>i, q, t-1</sub>* is its corresponding previous period value. Previous period data may be a clean response, impute or constructed data. 

If the ratio lies within, and not equal to, the limits, then a thousand pounds error is detected; else a thousand pounds error has not been identified. 

 

If a cleared predictive value does not exist, then the error detection calculation is as follows:  

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=\large L_{Lower} < \frac{y_{i, q, t}}{x_{i, q, t}} < L_{Upper}">
</p>

```asciimath
L_{Lower} < \frac{y_{i, q, t}}{x_{i, q, t}} < L_{Upper}
```

Where *x<sub>i, q, t</sub>* is the auxiliary register based selected turnover for business *i*. 

If the ratio lies within the limits, then no thousand pounds error is detected; else a thousand pounds error has been identified. 

At present, *L<sub>Lower</sub>* = 250 and *L<sub>Upper</sub>* = 1350. 

### 7.2 Error Correction Calculation
A detected error for question *q* is automatically corrected for responder *i* at time *t* by: 

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=\large y^{*}_{i, q, t} =  < \frac{y_{i, q, t}}{1000}">
</p>

```asciimath
y^{*}_{i, q, t} =  < \frac{y_{i, q, t}}{1000}
```

Where *y<sup>\*</sup><sub>i, q, t</sub>* is the corrected value and rounded to the nearest whole number.  

Once the principal target variable has been corrected, all other monetary values returned will be automatically correct by the same method as described above, if the user has enabled this. Else, no other values are corrected for the responder. 


## 8.0 Future Enhancements 

At present, method thresholds are fixed across all surveys, size bands and industry. A future enhancement could allow different thresholds to be set for cases that as particular industries that are more likely to experience higher fluctuations or sporadic data. The enhancement could also allow the method to detect and correct for cases where question values are requested in pounds but have incorrectly been returned in thousands.  
 
