# Mean of Ratios Specification

## 1.0 Meta

* Support area – Methodology – Processing, Editing & imputation
* Method theme – Imputation
* Status – draft, not tested

## 2.0 Terminology

* Contributor – A member of the sample; identified by a unique identifier.
* Record – A set of values for each contributor and period.
* Periodicity – The frequency of reference period data collection e.g., monthly.
* Target Period – The period which requires imputation to be applied.
* Target Variable – The variable of interest that requires data values to be imputed.
* Target Record – A contributor's record in the target period.
* Imputation Class – The variable used to group the data for the calculation of imputation links.
* Predictive Variable – A value used as a predictor for a contributor's target variable.
* Predictive Record – The record containing a contributor's predictive value.
* Predictive Period – The period containing predictive records; defined relative to the target period.
* Auxiliary variable – The variable used a predictor for a contributor’s target variable, where the predictive value is not available (i.e., where the contributor was not sampled in the predictive period).
* Responder – A contributor who has responded to the survey within a given period.
* Link – A ratio which is calculated for each imputation class and applied to impute data values. Commonly known as the imputation link.
* Previous Year Link – This is the imputation link for the previous year for a given contributor. This should be input into the method if the user wishes to apply weighted imputation.
* Target Period Link Weight – If using weighted imputation, this value represents the weight of the target period imputation link as a proportion of 1. If = 1 or missing, then weighted imputation is not applied.
* Matched Pair – A contributor that has returned, cleaned, non-zero values in both the target and predictive period.

## 3.0 Introduction

Mean of Ratios imputation is a standard imputation method for business surveys. It can be used to impute value for unit (complete) non-response or item (partial) non-response. There is an option to use trimming as the method can be influenced by extreme values. The method imputes a single numeric variable. It uses the relationship between the variable being imputed and an appropriate predictive or auxiliary variable. Typically, the predictive or auxiliary variable can be:

* a previous value from the variable of interest for the non-responder, known as forwards imputation
* a consecutive value from the variable of interest for the non-responder, also known as backwards imputation
* a known register-based variable for the non-responder that is well correlated with the variable of interest, known as construction imputation

Previous or consecutive auxiliary variables either can be returned, imputed or constructed. Forwards and backwards imputed values can be based upon returned, imputed or constructed values from the predictive record.

As imputation can be carried out for multiple periods simultaneously, the method can apply forward, backward or construction imputation. The type of imputation used which will vary for each non-respondent in each period depending on whether data is available in the predictive period.

The generic formula for using Mean of Ratios imputation is the imputation link multiplied by the predictive variable of interest (auxiliary variable in the case of construction) for the non-respondent from either a previous, consecutive or current period for forwards, backwards or construction imputation respectively.

## 4.0 Assumptions

* The predictive variable is a good predictor of the target variable in the target period
* The imputation classes group similar contributors together whilst providing a sufficient number of contributors within each class enabling robust link calculation
* The auxiliary variable is a good predictor of the target variable
* Matched pairs must comprise of clean, respondent non-zero data for both the target and predictive period

## 5.0 Method Input and Output - TO BE FINALISED WITH DST

### 5.1 Input Records

Input records must include the following fields of the correct types:

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* Periodicity – Numeric/string? - [TO CONFIRM]
* Imputation Class – Any
* Target Variable – Numeric – Nulls Allowed
* Predictive Variable – Numeric – Optional
* Auxiliary Variable – Numeric
* Lower Trim – Numeric – Optional
* Upper Trim – Numeric – Optional
* Forward Link – Numeric – Optional 
* Backward Link – Numeric – Optional
* Construction Link – Numeric – Optional
* Target Period Link Weight – Numeric – Optional
* Previous Year Forwards Imputation Link – Numeric – Optional
* Previous Year Backwards Imputation Link – Numeric – Optional
* Previous Year Construction Link – Numeric – Optional
* Exclusion Marker – Boolean. 0 = False, include. 1 = True, exclude – Optional. If marker not populated, then False.

Unless otherwise noted, fields must not contain null values. All other fields shall be ignored.

Note that the predictive variable is indirectly defined as the target variable in the predictive period.

### 5.2 Output Records

Output records shall always contain the following fields with the following types:

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* Imputation Class
* Final Target Variable – Numeric
* Imputation Marker – String
* Error Description – String
* Forward Link – Numeric
* Backward Link – Numeric
* Construction Link – Numeric

Fields of type "Any" shall be of the same type as the corresponding input fields as the values shall be the same in both input and output records.

The Imputation Marker must be one of the following:

* FIR = Forwards imputation from response
* FIC = Forwards imputation from construction
* BI = Backwards imputation
* C = Construction imputation from auxiliary variable
* R = Response. This value is cleared of errors or warnings
* E = Method error

### 5.3 Back Data

In order to correctly handle the first period of data, the method must accept a dataset containing back data. This dataset must contain the period directly preceding the first period in the main dataset. This data shall be the result of a prior imputation run and must not appear in the output.

Back data records shall always contain the following fields:

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* Imputation Class
* Final Target Variable – Numeric
* Imputation Marker – String
* Forward Link – Numeric
* Backward Link – Numeric
* Construction Link – Numeric

These fields must have the same types as their counterparts in the Input and Output records.

## 6.0 Overall Method

The method uses an imputation link in combination with a predictive or auxiliary value to calculate an imputed value for a target record. This predictive value can either be the target variable value from the contributor's predictive record or an auxiliary variable (e.g., a register-based variable).

Initially, a growth ratio is calculated for each matched pair for a given target variable; where a growth ratio is defined as the ratio of the value held for the target period by the value held for the predictive period. Within each imputation class, the set of growth ratios for a given question may undergo trimming to remove extreme, influential ratios. The mean of these ratios that remain after trimming then becomes the imputation link for each target variable in a given imputation class.

Matched pairs must be found using cleaned responses and non-zero data for both the target and predictive periods.

The method ends only when either there are no more missing values within the target variable or no more values can be imputed. The latter case constitutes an error condition and will be handled according to the error handling behaviour defined in section 6.5.

### 6.1 Growth Ratios

#### 6.1.1 Respondent Filtering and Matching

When calculating growth ratios, the method considers all cleaned, non-zero respondent data for both the target and predictive period.

Only respondents present in both the target and predictive period and the same imputation class can form a matched pair.

#### 6.1.2 Growth Ratio Calculations

Growth ratios must be calculated for each matched pair for every target variable by imputation class. The growth ratio will depend on the type of imputation taking place:

* A forwards growth ratio for a matched pair is the value held for the target variable for the target period divided by the value held for the previous period
* A backwards growth ratio for a matched pair is the value held for the target variable for the target period divided by the value held for the consecutive period
* Construction imputation does not require growth ratios to be calculated

A set of growth ratios will exist for each type of imputation and each target variable in a given imputation class. These sets can be ordered for trimming, if required by the user.

#### 6.1.3 Trimming

Trimming is an optional functionality of the Mean of Ratios imputation method which can be used to remove influential growth ratios.

Growth ratios for a given imputation class should be arranged in descending order (largest growth ratio to smallest). A contributor’s ratio should be trimmed if the row number is either less than the upper cut off value or greater than the lower cut off value. See section 7.2 for upper and lower cut off value calculations. Contributors that remain after trimming will be used to calculate imputation links.

For a given target variable, if the number of matched pairs in a given imputation class is less than or equal to 10, then trimming should not take place in the affected imputation class.

It is advised to keep a record of contributors that have been trimmed and the statistics of the imputation class prior to trimming.

Note that trimming is not applied when using construction imputation as growth ratios are not calculated for each contributor.

### 6.2 Imputation

Imputation uses a predictive value for a contributor and multiplies that by the appropriate link. Both the link and predictive value used depend on the type of imputation being performed. Imputation can only take place on records with no value for the target variable and where the appropriate predictive value and link are present or can be calculated. In particular, imputation of multiple contiguous periods must be performed in the correct order since imputations for a given contributor chain together. In all cases, the predictive period for a type of imputation is the same as that of the link being used.

#### 6.2.1 Imputation Links

When applying forwards and backwards imputation for a given target variable, an imputation link is calculated for each imputation class by summing the growth ratios of the (trimmed) data set, then dividing these by the number of ratios in the set (i.e., calculate the average growth ratio after trimming). Note, the number of ratios in the imputation class will only be equal to the total number of contributors in the imputation class if every contributor has formed a matched pair I.e., there are no missing values in the imputation class.

Construction imputation links are calculated using a consistent approach to constructed data as applied in the Ratio of Means imputation method. For given imputation class and target variable, the construction imputation link uses the sum of the clean responses in the target period divided by the sum of the responders' auxiliary values for the target period.

There is an option to use a weighted imputation link to account for growth from the same point in time in the previous year, where necessary. For a given target variable, the weighted imputation link is given by a weighted sum of average ratios from the same imputation class in the current period and the previous year same period.

If there are fewer than 10 matched pairs then no trimming is applied.

 #### 6.2.2 Forwards Imputation

Forward imputation imputes data for non-responders in the target period by multiplying a link to the predictive period data, where the predictive period is the period that immediately precedes the target period. It should be noted that the same link is used for forwards imputation from a response and from a construction.

##### Forwards Imputation from Response

For forwards imputation from response, the predictive value must either be a responded value or forward imputed values from a response. Records imputed using this imputation will be marked FIR.

##### Forwards Imputation from Construction

For forwards imputation from construction, the predictive value must only come from imputed values from construction imputation or forward imputed values from a construction. Records imputed using this imputation will be marked FIC.

#### 6.2.3 Backwards Imputation

Backwards imputation imputes data for non-responders in the target period by multiplying a link to the predictive period data, where the predictive period is the period that immediately follows the target period (i.e., consecutive period). Backwards imputation from construction must not occur. Records imputed using this imputation will be marked BI.

#### 6.2.4 Construction Imputation

Construction imputation imputes data for non-responders in the target period where no data is available in the predictive period and therefore, an auxiliary variable is used from the target period and is then multiplied by a link to create a constructed value. Records imputed using this imputation will be marked C.

Note that trimming cannot be applied when using construction imputation.

#### 6.2.5 Weighted Imputation

If required by the user, weighted imputation can be applied to account for growth or loss observed in a particular time period e.g., the growth in the previous year. This is done by summing a proportion of the imputation link in the current period with a proportion of the imputation link in the chosen time period.

The proportions must sum to 1, where 1 represents 100% i.e., target period link weight = 0.8 means the overall imputations links should be calculated using 80% of the current year link and 20% of the previous year link.  The type of imputation link must also be consistent with both periods e.g., the target period and chosen time period links must both be forwards imputation links.

### 6.4 Exclusion Markers - FINALISE WITH DST

Exclusion markers can be used to remove with influential growth ratios. The use of trimming should remove most influential growth ratios from imputation link calculations however, some influential growth ratios may still exist after trimming. Exclusion markers can be applied to remove these. As trimming is optional, exclusion markers can be used as well as or instead of trimming.

Trimming should be applied first, then additional growth ratios should only be excluded if they are still causing unsuitable imputation links. If it is found that many additional growth ratios are frequently being removed, then the trimming parameters may need to be reviewed.

Any growth ratios removed should be documented and it is advised that descriptive statistics of the imputation class be calculated before and after the removal of growth ratios.

Note, an exclusion marker applies to specific contributors in specific reference periods. If a contributors' record is excluded for the target period, then by design it will also be excluded for the predictive period when using forwards or backwards imputation as a matched pair will not be found.

### 6.5 Error Handling

In the case of errors occurring the method shall not result in any output records. Instead, a suitable error shall be emitted.

## 7.0 Calculations

### 7.1 Growth Ratio Calculations

Let *x<sub>q,i,t</sub>*  be the target variable and its predictive variable be *x<sub>q,i,t−1</sub>* or *x<sub>q,i,t+1</sub>* so that matched pairs for the target and predictive period, where the target variable in the target period, *x<sub>q,i,t</sub>* and the predictive value are both non-zero, responded values and the predictive value has been cleaned of errors or warnings.

Note, growth ratios are not calculated for construction imputation. See section 7.3.2 for further details on how construction imputation links are calculated.

#### 7.1.1 Forwards Imputation

For a given contributor:

$$\large Fr_{q, i, t} = \frac{x_{q, i, t}}{x_{q, i, t-1}}$$

```asciimath
Fr_{q, i, t} = \frac{x_{q, i, t}}{x_{q, i, t-1}}
```

Where *Fr<sub>q,i,t</sub>* is the growth ratio of the clean returned target variable in the target period and corresponding clean returned predictive variable in the previous predictive period for question *q*, contributor *i* and at time *t*. This is the growth ratio of *x<sub>q,i,t</sub>*.

#### 7.1.2 Backwards Imputation

For a given contributor:

$$\large Br_{q,i,t} = \frac{x_{q,i,t}}{x_{q,i,t+1}}$$

```asciimath
Br_{q,i,t} = \frac{x_{q,i,t}}{x_{q,i,t+1}}
```

Where *Br<sub>q,i,t</sub>* is the growth ratio of the clean returned target variable in the target period and corresponding clean returned predictive variable in the consecutive predictive period for question *q*, contributor *i* and at time *t*. This is the growth ratio of *x<sub>q,i,t</sub>*.

### 7.2 Trimming Calculations

For a given variable, once growth ratios for each imputation class are ordered as described in section 6.1.3, define lower and upper trim as *M<sub>lower</sub>*  and *M<sub>upper</sub>*  respectively. These are the top and bottom percent of the data to be removed before imputation links are calculated. From these, calculate the upper and lower cut-offs, *U<sub>q,g,t</sub>*  and *W<sub>q,g,t</sub>*  such that:

$$ \text{Upper cut off} = U_{q,g,t} = N_{q,g,t}*\Bigl(\frac{M_{upper}}{100}\Bigl)$$

$$ \text{Lower cut off} = W_{q,g,t} = N_{q,g,t}*\Bigl(\frac{1−M_{lower}}{100}\Bigl)$$

```asciimath
Upper cut off = U_{q,g,t} = N_{q,g,t}*(\frac{M_{upper}}{100})

Lower cut off = W_{q,g,t} = N_{q,g,t}*(\frac{1−M_{lower}}{100})
```

Where *N<sub>q,g,t</sub>* is the number of rows after the removal of zeros/unmatched pairs in the dataset for question *q*, imputation class *g* and time *t*. A record should be trimmed from the data set if one of the following conditions are met:

$\text{Row number} < U_{q,g,t} , \text{or}$

$\text{Row number} > W_{q,g,t}$

```asciimath
Row number < U_{q,g,t} , or

Row number > W_{q,g,t}
```

For example, if *U<sub>q,g,t</sub>*  is between 4 and 5, then rows 4 and lower will be removed. If *W<sub>q,g,t</sub>*  is between 56 and 57, then rows 57 and above will be removed. If *U<sub>q,s,t</sub>* is exactly 4, then rows 3 and below will be removed. If *W<sub>q,s,t</sub>  is exactly 56, then rows 57 and above will be removed.

### 7.3 Imputation Link Calculations

#### 7.3.1 Forwards and Backward Imputation Links

For simplicity, let *[F, B]r<sub>q,g,t</sub> = r<sub>q,g,t</sub>* depending on whether the type of imputation is either forwards or backwards. For each imputation class, the imputation links are calculated by the mean growth ratio of the (trimmed) dataset:

$$ \large \bar{R}_{q,g,t} = \Sigma_{all \ i \in g \ \text{in dataset after trim}} \Bigl(\frac{r_{q,i,t}}{N_{trimmed,q,g,t}}\Bigl) $$

```asciimath
\bar{R}_{q,g,t} = \Sigma_{all \ i \in g \ \text{in dataset after trim}} (\frac{r_{q,i,t}}{N_{trimmed,q,g,t}})
```

Where $\bar{R}$ is the mean of ratios. *N<sub>trimmed q,g,t</sub>* is the lowered number of items in the dataset post trim. This is equal to *N<sub>q,g,t</sub>*  when *N<sub>q,g,t</sub> ≤ 10* or if trimming was not required.

Recall that all unmatched pairs and zeros were removed before the trimming process, so the average ratio over the imputation group excludes these.

#### 7.3.2 Construction Imputation Links

Construction imputation links are calculated using the Ratio of Means method and does not use trimming. However, exclusion markers can be applied to remove influential contributors.

For each imputation class, the imputation links are calculated by the sum of the cleaned responders in the target period, *x<sub> q, i, t</sub>*, divided by the sum of their corresponding auxiliary variables, *y<sub> q, i, t</sub>*, also held for the target period i.e., the predictive period is also the target period.

If the denominator is 0 then the link shall default to 1.

$$\large CR_{q,g,t} = \frac{\large\Sigma x_{q,i,t}}{\large\Sigma y_{q,i,t}} $$

```asciimath
CR_{q,g,t} = \frac{\Sigma x_{q,i,t}}{\Sigma y_{q,i,t}}
```

Where *CR<sub>q, g, t</sub>* is the construction imputation link for a given variable, *q*, in imputation class, *g*, and in the target period, *t*.

#### 7.3.3 Weighted Imputation Links

Let *L<sub>q,g,t</sub>* be the weighted imputation link given by:

$$ \large L_{q,g,t} = w\bar{R}_{q,g,t} +(1−w) \bar{R}_{q,g,t−12}, \text{for monthly surveys} $$

$$ \large L_{q,g,t} = w\bar{R}_{q,g,t} +(1−w) \bar{R}_{q,g,t−4}, \text{for quarterly surveys} $$

$$ \large L_{q,g,t} = w\bar{R}_{q,g,t} +(1−w) \bar{R}_{q,g,t−1}, \text{for yearly surveys} $$

```asciimath
L_{q,g,t} = w\bar{R}_{q,g,t} +(1−w) \bar{R}_{q,g,t−12}, \text{for monthly surveys}

L_{q,g,t} = w\bar{R}_{q,g,t} +(1−w) \bar{R}_{q,g,t−4}, \text{for quarterly surveys}

L_{q,g,t} = w\bar{R}_{q,g,t} +(1−w) \bar{R}_{q,g,t−1}, \text{for yearly surveys}
```

Where *w* is the defined weight.

If the previous year link is missing, then the weight applied to target period should = 1, such that *L<sub>q,g,t</sub>* = $\bar{R}_{q,g,t}$.

If weighted imputation is being applied to construction imputation, then $\bar{R}$ is replaced with *CR* in section 7.3.2.

### 7.4 Imputed Value Calculations

A single value, $\bar{Y}_{q,g,t}$ is imputed for the non-responder, 
 *i*,  in the target period, *t*, given by:

Forwards Imputation:

$$ \large \bar{Y}_{i,q,g,t} = F\bar{R}_{q,g,t} * y_{i,q,g,t-1} $$

```asciimath
Forwards imputation: \bar{Y}_{i,q,g,t} = F\bar{R}_{q,g,t} * y_{i,q,g,t-1}
```

Where the predictive value, *y<sub>i,q,g,t-1</sub>*, is either a returned,
 imputed or constructed value held for the responder in the previous period.

Backwards imputation:

$$ \large \bar{Y}_{i,q,g,t} = B\bar{R}_{q,g,t} * y_{i,q,g,t+1} $$

```asciimath
Backwards imputation: \bar{Y}_{i,q,g,t} = B\bar{R}_{q,g,t} * y_{i,q,g,t+1}
```

Where the predictive value, *y<sub>i,q,g,t+1</sub>*, is a
 returned value held for the contributor in a consecutive period.

Construction imputation:

$$ \large \bar{Y}_{i,q,g,t} = CR_{q,g,t} * y_{i,q,g,t} $$

```asciimath
Construction imputation: \bar{Y}_{i,q,g,t} = CR_{q,g,t} * y_{i,q,g,t}
```

Where the predictive value, $y_{i,q,g,t}$, is a well correlated
 register based auxiliary variable held for the respondent in the target period.

## 8.0 Imputation Rules

Mean of Ratios imputation follows a set of rules to ensure that it
 is used correctly, these rules are in the same order as the flow chart below:

* If there is no response available and the contributor is
 being sampled for the first time, calculate a constructed link
 using the auxiliary data available for that contributor (C)

* If there is no response available for a second period,
 forwards impute based off the constructed value (FIC)

* If a clean response is available for the previous period but not the
 current period, then perform forwards imputation (FIR)

* If a clean response is available for the second period but not the
 third, fourth or current, then perform rolling forwards imputation
 from the second period for all missing periods (FIR)

* If a respondent does not respond for the first two periods it
 is sampled (see second bullet point) however does respond for the
 third period and the response is clean, then overwrite periods 1 and 2 with backwards
 imputation (BI)

* Forwards and backwards imputation can be carried out
 from the same clean respondent however,
 a forwards impute will always be preferred to a backwards impute

* If a respondent responds for all periods, then imputation is not needed

* If a contributor is rotated out of the sample and then rotated back
 into the sample, any values that were previously present should not
 be used to forwards impute

* If a contributor is rotated out of sample and then rotated
 back into the sample, the response from the current period
 should not be used to backwards impute

* If auxiliary data is missing and the contributor is a
 non-responder for all sampled periods, then an error will occur

* Mean or median imputation should be used if there is no
 auxiliary information available (no previous period auxiliary,
 no consecutive period auxiliary and no register-based auxiliary variable)

* Mean imputation: impute a mean value for non-responders
 based upon target period cleared respondents within the same imputation class

* Median imputation: impute a median value for non-responders
 based upon target period cleared respondents within the same imputation class

Please see the image below for further information.

![imputation_types1](https://user-images.githubusercontent.com/87982871/167370091-bd18e5bb-fef5-4d46-9b1e-a452040d9e16.png)
