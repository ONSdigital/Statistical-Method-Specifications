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
* Imputation Class – The variable used to group the data for the calculation of
 imputation links.
* Predictive Variable – A value used as a predictor for a contributor's target variable.
* Predictive Record – The record containing a contributor's predictive value.
* Predictive Period – The period containing predictive records; defined
 relative to the target period.
* Auxiliary variable – The variable used a predictor for a contributor’s target
 variable, where the predictive value is not available (i.e., where the
 contributor was not sampled in the predictive period).
* Responder – A contributor who has responded to the survey within a given period.
* Link – A ratio which is calculated for each imputation class and applied
 to impute data values. Commonly known as the imputation link.
* Previous Year Link – This is the imputation link for the previous year for
 a given contributor. This should be input into the method if the user wishes
 to apply weighted imputation.
* Target Period Link Weight – If using weighted imputation, this value
 represents the weight of the target period imputation link as a proportion
 of 1. If = 1, then the method will take 100% of the target period link.
* Matched Pair – A contributor that has returned, cleaned, non-zero (unless chosen
 otherwise) values in both the target and predictive period and within the same
 imputation class.

## 3.0 Introduction

Mean of Ratios imputation is a standard imputation method for business
 surveys. It can be used to impute value for unit (complete) non-response
 or item (partial) non-response. There is an option to use trimming as the
 method can be influenced by extreme values. The method imputes a single
 numeric variable. It uses the relationship between the variable being imputed
 and an appropriate predictive or auxiliary variable. Typically, the predictive
 variable can be:

* a previous value from the variable of interest for the non-responder,
 used for forwards imputation
* a consecutive value from the variable of interest for the non-responder,
 used for backwards imputation

Typically, the auxiliary variable can be:

* a known register-based variable for the non-responder that is well correlated
 with the variable of interest, used for construction imputation

Previous or consecutive predictive variables either can be returned, imputed
 or constructed. Forwards and backwards imputed values can be based upon
 returned, imputed or constructed values from the predictive record.

As imputation can be carried out for multiple periods simultaneously, the
 method can apply forward, backward or construction imputation. The type of
 imputation used which will vary for each non-respondent in each period
 depending on whether data is available in the predictive period.

The generic formula for using Mean of Ratios imputation is the imputation link
 multiplied by the predictive variable of interest (auxiliary variable in the
 case of construction) for the non-respondent from either a previous, consecutive
 or current period for forwards, backwards or construction imputation respectively.

## 4.0 Assumptions

* The predictive variable is well correlated (a good predictor) with the
 target variable
* The imputation classes group similar contributors together whilst
 providing a sufficient number of contributors within each class enabling
 robust link calculation
* The auxiliary variable is well correlated (a good predictor) with the target variable
* The auxiliary variable is populated for every contributor when performing
 construction imputation
* Matched pairs must comprise of clean, respondent
 data for both the target and predictive period and within the same imputation class

## 5.0 Method Input and Output

### 5.1 Input Records

Input records must include the following fields of the correct types:

* Unique Identifier – Any e.g., Business Reporting Unit for a given contributor
* Period – String in "YYYYMM" format
* Imputation Class – Any
* Target Variable – Numeric – Nulls Allowed
* Auxiliary Variable – Numeric
* Forward Link – Numeric – Optional – Nulls Allowed
* Backward Link – Numeric – Optional – Nulls Allowed
* Construction Link – Numeric – Optional – Nulls Allowed
* Link Filter Columns – Optional – This can be any number of columns of any type
  that are to be used by the link filter (section 6.1.1) to prevent
  responders being used in the imputation link calculations

Unless otherwise noted, fields must not contain null values. All
 other fields shall be ignored.

Note that the predictive variable is indirectly defined as the
 target variable in the predictive period.

There are additional parameters, separate to the inputs, that the user must
 populate. These are:

* Periodicity to inform the method of the appropriate time lags for growth ratio
 calculations (section 7.1) and for weighted imputation (section 7.3.3)
* Whether zeros will be included when finding valid matched pairs for forward and
 backward growth ratio calculations (section 6)
* Whether trimming of growth ratios will take place and if so, what percent of
 the classes will be trimmed (section 6.1.3)
* The minimum number of matched pairs present in an imputation
 class to perform trimming, denoted $N+1$.
* Target period link weight must be specified to implement weighted imputation
 links (section 6.2.5)
* If using weighted imputation, time lag, *k*, informs which previous period’s
 imputation links should be used in addition to the target period links

### 5.2 Output Records

Output records shall always contain the following fields with the following types:

* Unique Identifier – Any e.g., Business Reporting Unit for a given contributor
* Period – String in "YYYYMM" format
* Imputation Class – Any
* Forward Growth Ratio – Numeric
* Backward Growth Ratio – Numeric
* Forward Link – Optionally weighted – Numeric
* Backward Link – Optionally weighted – Numeric
* Construction Link – Optionally weighted – Numeric
* Weighted Forward Link – Numeric – Optional
* Weighted Backward Link – Numeric – Optional
* Weighted Construction Link – Numeric – Optional
* Final Target Variable – Numeric
* Imputation Marker – String
* Forward Link Observation Count – Numeric
* Backward Link Observation Count – Numeric
* Construction Link Observation Count – Numeric
* Forward Link Default Marker – Boolean
* Backward Link Default Marker – Boolean
* Construction Link Default Marker – Boolean

If Trimming is performed, then output records should also contain:

* Trim Inclusion Forward Growth Ratio Marker
 – Boolean – Optional – Nulls Allowed
* Trim Inclusion Backward Growth Ratio Marker
 – Boolean – Optional – Nulls Allowed

If Responder Filtering is performed, then output records should also contain:

* Post Filter Inclusion Marker Previous – Boolean – Nulls Allowed
* Post Filter Inclusion Marker Target – Boolean – Nulls Allowed
* Post Filter Inclusion Marker Next – Boolean – Nulls Allowed

If Weighted Imputation is performed, then output record should also contain:

* Unweighted Forward Link – Numeric – Optional – Nulls Allowed
* Unweighted Backward Link – Numeric – Optional – Nulls Allowed
* Unweighted Construction Link – Numeric – Optional – Nulls Allowed

Fields of type 'Any' shall be of the same type as the corresponding
 input fields as the values shall be the same in both input and output records.

Output records labelled ‘Optional’ shall only be populated in the output
 dataset if the corresponding functionality was used e.g., trimming.

The Imputation Marker must be one of the following:

* FIR = Forwards imputation from response
* FIC = Forwards imputation from construction
* BI = Backwards imputation
* C = Construction imputation from auxiliary variable
* R = Response. This value is cleared of errors or warnings

### 5.3 Back Data

In order to correctly handle the first period of data, the method
 must accept a dataset containing back data. This dataset must
 contain the period directly preceding the first period in the
 main dataset. This data shall be the result of a prior imputation
 run and must not appear in the output.

If weighted imputation is being implemented, then the back data should
 include the necessary periods for the specified time lag
 (e.g., the previous year) to allow this.

Back data records shall always contain the following fields:

* Unique Identifier – Any e.g., Business Reporting Unit
* Period – String in "YYYYMM" format
* Imputation Class
* Final Target Variable – Numeric
* Imputation Marker – String

These fields must have the same types as their counterparts
 in the Input and Output records.
If Weighted Imputation is performed, then the back data should also contain:

* Unweighted Forward Link – Numeric
* Unweighted Backward Link – Numeric
* Unweighted Construction Link – Numeric

These fields must have the same types as their weighted counterparts in the
 Output records.

## 6.0 Overall Method

The method uses an imputation link in combination with a predictive
 or auxiliary value to calculate an imputed value for a target record.
 This predictive value can either be the target variable value from
 the contributor's predictive record or an auxiliary variable
 (e.g., a register-based variable).

Initially, a growth ratio is calculated for each matched pair for
 a given target variable; where a growth ratio is defined as the
 ratio of the value held for the target period by the value held
 for the predictive period. Within each imputation class, the set
 of growth ratios for a given question may undergo trimming to
 remove extreme, influential ratios. The mean of these ratios that
 remain after trimming then becomes the imputation link for each
 target variable in a given imputation class.

Matched pairs must be found using cleaned responses and non-zero (unless
 necessary) data for both the target and predictive periods and
 within the same imputation class.
 It is best practice to exclude zeros when finding matched pairs however,
 if the input data set has a high prevalence of zeros in the target
 variable, then the user can choose to include zeros when finding matched
 pairs for forwards and backwards imputation. This includes zeros in
 either the target variable or predictive variable, or both.

The method ends only when either there are no more missing values
 within the target variable or no more values can be imputed. The
 latter case constitutes an error condition and will be handled
 according to the error handling behaviour defined in section 6.4.

### 6.1 Growth Ratios

#### 6.1.1 Respondent Filtering and Matching

When calculating growth ratios, the method considers all cleaned,
 non-zero (unless necessary) respondent data for both the
 target and predictive period.

Only respondents present in both the target and predictive period
 and the same imputation class can form a matched pair.

By default, the method will consider all responders when finding matched
 pairs for growth ratio calculations. However, the method must also accept
 an optional expression for filtering responders as there are specific
 requirements for the data that should be used when calculating imputation
 links. If provided, link calculations will only consider responders matching
 this filter (in addition to the conditions described above). This filter
 will only apply to link calculations.

Note, filtering applies to specific contributors in
 specific reference periods. If a contributors' record is excluded
 for the target period, then by design it will also be excluded
 for the predictive period when using forwards or backwards imputation
 as a matched pair will not be found. The method will output filtering
 inclusion markers to show this.

Trimming (section 6.1.3) should remove any influential growth ratios.
 However, if some remain after trimming, then filtering could be used
 to remove these known influential growth ratios. This would require the
 method to run twice. First to identify the influential growth ratios and
 second, to run the method again with the filter applied.

If forward, backward and construction links are all provided in the input
 dataset, then neither filtering nor trimming will be performed. This forces
 the method to use the exact links provided.

#### 6.1.2 Growth Ratio Calculations

Growth ratios must be calculated for each matched pair for every
 target variable by imputation class. The growth ratio will depend
 on the type of imputation taking place:

* A forwards growth ratio for a matched pair is the value held
 for the target variable for the target period divided by the value
 held for the previous period
* A backwards growth ratio for a matched pair is the value held
 for the target variable for the target period divided by the value
 held for the consecutive period
* Construction imputation does not require growth ratios to be calculated

If the user has chosen to include zeros when finding matched pairs
 (section 6.1.1), then the growth ratio for the affected respondents
 for both forwards and backwards imputation should default to equal 1.
 Note that this does not mean the overall imputation links should be
 defaulted to 1; this only affects matched pairs with zero data values.

A set of growth ratios will exist for each type of imputation
 and each target variable in a given imputation class. These sets
 can be ordered for trimming, if required by the user.

#### 6.1.3 Trimming

Trimming is an optional functionality of the Mean of Ratios
 imputation method which can be used to remove influential growth ratios.

Growth ratios for a given imputation class should be arranged in
 descending order (largest growth ratio to smallest). A contributor’s
 ratio should be trimmed if the row number is either less than the
 upper cut off value or greater than the lower cut off value. See
 section 7.2 for upper and lower cut off value calculations. Contributors
 that remain after trimming will be used to calculate imputation links.

For a given target variable, if the number of matched pairs in
 a given imputation class is less than or equal to a predefined threshold,
 then trimming should not take place in the affected imputation class.
 See section 6.2.1. If filtering is applied and the resulting number of matched
 pairs falls under or equal to the predefined threshold, then trimming should
 still be applied. The method will automatically adjust the predefined threshold
 to allow this.

It is advised to keep a record of contributors that have
 been trimmed and the statistics of the imputation class prior to trimming.

Note that trimming is not applied when using construction
 imputation as growth ratios are not calculated for each contributor.

### 6.2 Imputation

Imputation uses a predictive value for a contributor and
 multiplies that by the appropriate link. Both the link and
 predictive value used depend on the type of imputation being
 performed. Imputation can only take place on records with no
 value for the target variable and where the appropriate
 predictive value and link are present or can be calculated.
 In particular, imputation of multiple contiguous periods must
 be performed in the correct order since imputations for a given
 contributor chain together. In all cases, the predictive period
 for a type of imputation is the same as that of the link being used.

#### 6.2.1 Imputation Links

When applying forwards and backwards imputation for a given target
 variable, an imputation link is calculated for each imputation class
 by summing the growth ratios of the (trimmed) data set, then dividing
 these by the number of ratios in the set (i.e., calculate the average
 growth ratio after trimming). Note, the number of ratios in the
 imputation class will only be equal to the total number of
 contributors in the imputation class if every contributor has formed
 a matched pair I.e., there are no missing values in the
 imputation class.

Construction imputation links are calculated using a consistent
 approach to constructed data as applied in the Ratio of Means
 imputation method. For a given imputation class and target variable,
 the construction imputation link uses the sum of the clean responses
 in the target period divided by the sum of the responders' auxiliary
 values for the target period.

There is an option to use a weighted imputation link to account
 for growth from the same point in time in the previous year,
 where necessary. For a given target variable, the weighted
 imputation link is given by a weighted sum of average ratios
 from the same imputation class in the current period and the
 previous year same period.

When implementing best practice, if there are less than or equal
 to 10 matched pairs then trimming should not be applied. The value
 of 10 can be less, however links generated in this case may be less
 robust and should be used with caution. To prevent this, imputation
 classes should be of appropriate size, where possible.

#### 6.2.2 Forwards Imputation

Forward imputation imputes data for non-responders in the
 target period by multiplying a link to the predictive period
 data, where the predictive period is the period that immediately
 precedes the target period. It should be noted that the same link
 is used for forwards imputation from a response and from a construction.

##### Forwards Imputation from Response

For forwards imputation from response, the predictive value
 must either be a responded value or forward imputed values
 from a response. Records imputed using this imputation will
 be marked FIR.

##### Forwards Imputation from Construction

For forwards imputation from construction, the predictive value
 must only come from imputed values from construction imputation
 or forward imputed values from a construction. Records imputed
 using this imputation will be marked FIC.

#### 6.2.3 Backwards Imputation

Backwards imputation imputes data for non-responders in the
 target period by multiplying a link to the predictive period
 data, where the predictive period is the period that immediately
 follows the target period (i.e., consecutive period). Backwards
 imputation from construction must not occur. Records imputed using
 this imputation will be marked BI.

#### 6.2.4 Construction Imputation

Construction imputation imputes data for non-responders in
 the target period where no data is available in the predictive
 period and therefore, an auxiliary variable is used from the
 target period and is then multiplied by a link to create a
 constructed value. Records imputed using this imputation will
 be marked C.

Note that trimming cannot be applied when using construction imputation.

#### 6.2.5 Weighted Imputation

If required by the user, weighted imputation can be applied
 to account for growth or loss observed in a particular time
 period e.g., the growth in the previous year. This is done by
 summing a proportion of the imputation link in the current period
 with a proportion of the imputation link in the chosen time period.

The proportions must sum to 1, where 1 represents 100% i.e.,
 target period link weight = 0.8 means the overall imputation
 links should be calculated using 80% of the current year link
 and 20% of the previous link.  The type of imputation link
 must also be consistent with both periods e.g., the target period
 and chosen time period links must both be forwards imputation links.

If the previous link is not available, then 100% of the target
 period link should be used.

Note, if the forward, backward or construction links are provided in
 the input dataset, then weighting is not applied. This forces the
 method to use the exact links provided.

### 6.3 Best Practice Guidance

To summarise the best practice information mentioned within this
 specification:

* Zeros should be excluded when calculating growth ratios except
 when the data has a high prevalence of zeros
* Only data that is clean (free of errors or warnings) should be used
 when finding matched pairs. Data that is suspicious or illogical should
 not be used to inform imputation links
* Trimming should not be applied if the number of matched pairs
 (growth ratios) is less than or equal to 10
* Respondent filtering should only be used to remove extreme or
 influential growth ratios that remain after trimming has been
 implemented. Respondent filtering should be seen as a last resort.

### 6.4 Error Handling

In the case of errors occurring the method shall not result
 in any output records. Instead, a suitable error shall be emitted.

## 7.0 Calculations

### 7.1 Growth Ratio Calculations

Let $x_{q,i,t}$  be the target variable and its predictive
 variable be $x_{q,i,t−1}$ or $x_{q,i,t+1}$ so that
 matched pairs for the target and predictive period, where the target
 variable in the target period, $x_{q,i,t}$ and the predictive
 value are both non-zero (unless necessary), responded values
 and the predictive value has been cleaned of errors or warnings.

Note, growth ratios are not calculated for construction imputation.
 See section 7.3.2 for further details on how construction imputation links are calculated.

#### 7.1.1 Forwards Imputation

For a given contributor:

$$\large Fr_{q, i, t} = \frac{x_{q, i, t}}{x_{q, i, t-1}}$$

```asciimath
Fr_{q, i, t} = \frac{x_{q, i, t}}{x_{q, i, t-1}}
```

Where $Fr_{q,i,t}$ is the growth ratio of the clean returned
 target variable in the target period and corresponding clean returned
 predictive variable in the previous predictive period for question *q*,
 contributor *i* and at time *t*. This is the growth ratio of
 $x_{q,i,t}$.

If the user has chosen to include zeros when finding matched pairs,
 then $Fr_{q,i,t} = 1$ when at least one of $x_{q,i,t} = 0$ or $x_{q,i,t-1} = 0$.

#### 7.1.2 Backwards Imputation

For a given contributor:

$$\large Br_{q,i,t} = \frac{x_{q,i,t}}{x_{q,i,t+1}}$$

```asciimath
Br_{q,i,t} = \frac{x_{q,i,t}}{x_{q,i,t+1}}
```

Where $Br_{q,i,t}$ is the growth ratio of the clean returned
 target variable in the target period and corresponding clean returned
 predictive variable in the consecutive predictive period for question *q*,
 contributor *i* and at time *t*. This is the growth ratio of
 $x_{q,i,t}$.

If the user has chosen to include zeros when finding matched pairs,
 then $Br_{q,i,t} = 1$ when at least one of $x_{q,i,t} = 0$ or $x_{q,i,t+1} = 0$.

### 7.2 Trimming Calculations

For a given variable, once growth ratios for each imputation class
 are ordered as described in section 6.1.3, define lower and upper
 trim as $M_{lower}$ and $M_{upper}$  respectively.
 These are the top and bottom percent of the data to be removed before
 imputation links are calculated. From these, calculate the upper and
 lower cut-offs, $U_{q,g,t}$  and $W_{q,g,t}$  such that:

$$ \text{Upper cut off} = U_{q,g,t} = n_{q,g,t}*\Bigl(\frac{M_{upper}}{100}\Bigl)$$

$$ \text{Lower cut off} = W_{q,g,t} = n_{q,g,t}*\Bigl(\frac{1−M_{lower}}
 {100}\Bigl) + 1 $$

```asciimath
Upper cut off = U_{q,g,t} = n_{q,g,t}*(\frac{M_{upper}}{100})

Lower cut off = W_{q,g,t} = n_{q,g,t}*(\frac{1−M_{lower}}{100}) + 1
```

Where $n_{q,g,t}$ is the number of valid matched pairs in the dataset for
 question *q*, imputation class *g* and time *t*. A record should be trimmed
 from the data set if one of the following conditions are met:

$\text{Row number} < U_{q,g,t} , \text{or}$

$\text{Row number} > W_{q,g,t}$

```asciimath
Row number < U_{q,g,t} , or

Row number > W_{q,g,t}
```

For example, if $U_{q,g,t}$  is between 4 and 5, then rows
 4 and lower will be removed. If $W_{q,g,t}$  is between 56
 and 57, then rows 57 and above will be removed. If $U_{q,g,t}$
 is exactly 4, then rows 3 and below will be removed. If $W_{q,g,t}$
 is exactly 56, then rows 57 and above will be removed.

### 7.3 Imputation Link Calculations

#### 7.3.1 Forwards and Backward Imputation Links

For simplicity, let the growth ratios $[F, B]r_{q,i,t} = r_{q,i,t}$
 depending on whether the type of imputation is either forwards
 or backwards. For each imputation class, the imputation links are
 calculated by the mean growth ratio of the (trimmed) dataset:

$$ \large \bar{R}_{q,g,t} = \Sigma_{all \ i \in g \ \text{in dataset after trim}}
\Bigl(\frac{r_{q,i,t}}{n_{trimmed,q,g,t}}\Bigl) $$

```asciimath
\bar{R}_{q,g,t} = \Sigma_{all \ i \in g \ \text{in dataset after trim}} (\frac{r_{q,i,t}}{N_{trimmed,q,g,t}})
```

Where $\bar{R}$ is the mean of ratios. $n_{trimmed, q,g,t}$
 is the lowered number of items in the dataset post trim.
 This is equal to $n_{q,g,t}$  when $n_{q,g,t} ≤ N$ or
 if trimming was not required. $N+1$ is the minimum number of matched
 pairs in a given imputation class required to apply trimming.

Recall that all unmatched pairs and filtered out observations were removed before
the trimming process, so the average ratio over the imputation group excludes these.

#### 7.3.2 Construction Imputation Links

Construction imputation links are calculated using the Ratio of Means
 method and does not use trimming. However, exclusion markers can be
 applied to remove influential contributors.

For each imputation class, the imputation links are calculated by the sum
 of the cleaned responders in the target period, $x_{q, i, t}$,
 divided by the sum of their corresponding auxiliary variables,
 $y_{q, i, t}$, also held for the target period i.e.,
 the predictive period is also the target period.

If the denominator is 0 then the link shall default to 1.

$$\large CR_{q,g,t} = \frac{\large\Sigma x_{q,i,t}}{\large\Sigma y_{q,i,t}} $$

```asciimath
CR_{q,g,t} = \frac{\Sigma x_{q,i,t}}{\Sigma y_{q,i,t}}
```

Where $CR_{q, g, t}$ is the construction imputation link for
 a given variable, *q*, in imputation class, *g*, and in the target period, *t*.

#### 7.3.3 Weighted Imputation Links

Let $L_{q,g,t}$ be the weighted imputation link given by:

$$\large L_{q,g,t} = w\bar{R}\_{q,g,t} +(1−w) \bar{R}\_{q,g,t−k} $$

```asciimath
L_{q,g,t} = w\bar{R}_{q,g,t} +(1−w) \bar{R}_{q,g,t-k}
```

Where $t-k$ relates to the imputation link of time lag *k* and *w* is the defined
 target period link weight. These two parameters would need to be specified if
 using weighted imputation.

If the previous year link is missing, then the target period link weight applied
 to target period should = 1, such that $L_{q,g,t} = \bar{R}_{q,g,t}$.

If weighted imputation is being applied to construction imputation,
 then $\bar{R}$ is replaced with *CR* in section 7.3.2.

### 7.4 Imputed Value Calculations

Let $\bar{Y}_{q,i,t}$ denote a single variable, *q*, which is imputed for the
non-responder, *i*,  in the target period, *t*, given by:

$$ \large \bar{Y}_{q,i,t} = \text{Link * (Predictive or Auxiliary Value)} $$

```asciimath
\bar{Y}_{q,i,t} = \text{Link * (Predictive or Auxiliary Value)}
```

For forwards imputation, the link as calculated in 7.3.1 is used with the predictive
 value, $y_{q,i,t-1}$, which is either a returned,
 imputed or constructed value held for the responder in the previous period.

For backwards imputation, the link as calculated in 7.3.1 is used with the predictive
 value, $y_{q,i,t+1}$, which is a
 returned value held for the contributor in a consecutive period.

For construction imputation, the link as calculated in 7.3.2 is used with the auxiliary
 value, $y_{q,i,t}$, which is a well correlated
 register based auxiliary variable held for the respondent in the target period.

## 8.0 Imputation Rules

Mean of Ratios imputation follows a set of rules to ensure that it
 is used correctly, these rules are in the same order as the flow chart below:

* If there is no clean response available and the contributor is
 being sampled for the first time, calculate a constructed link
 using the auxiliary data available for that contributor (C)

* If there is no clean response available for a second period,
 forwards impute based off the constructed value (FIC)

* If a clean response is available for the previous period but not the
 current period, then perform forwards imputation (FIR)

* If a clean response is available for the second period but not the
 third, fourth or current, then perform rolling forwards imputation
 from the second period for all missing periods (FIR)

* If a contributor does not respond for the first two periods it
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

* If a contributor’s imputation class in the predictive period is
 different from its imputation class in the target period (i.e., the
 imputation class has changed), then perform construction imputation (C)

* If a contributor’s imputation class in the predictive period is
 different from its imputation class in the target period (i.e., the
 imputation class has changed), then a growth ratio should not be
 calculated for this contributor

* If auxiliary data is missing and the contributor is a
 non-responder for all sampled periods, then an error will occur

* Mean or median imputation should be used if there is no
 auxiliary information available (no previous period auxiliary,
 no consecutive period auxiliary and no register-based auxiliary variable).
 Mean and median imputation are not available in this method.

  * Mean imputation: impute a mean value for non-responders
 based upon target period cleared respondents within the same imputation class

  * Median imputation: impute a median value for non-responders
 based upon target period cleared respondents within the same imputation class

Please see the image below for further information.

![imputation_types1](https://user-images.githubusercontent.com/87982871/167370091-bd18e5bb-fef5-4d46-9b1e-a452040d9e16.png)
