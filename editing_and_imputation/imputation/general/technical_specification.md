# General Imputation Method Specification

## 1.0 Terminology

* Contributor - A member of the sample identified by an identifier.
* Record - A set of values for each contributor and period. At most one
  record for a contributor can be present for a given period.
* Target Variable - The variable (A.K.A. field) in the record to be imputed.
* Response - A value in a target variable which exists prior to any
  imputation for a given period.
* Responder - A contributor whose target variable contains a response for a
  given period.
* Non-responder - a contributor who is not a Responder.
* Predictive Value - A value used as a predictor for a contributor's target
  variable.
* Predictive Record - The record containing a contributor's predictive
  value.
* Target Period - The period currently undergoing imputation.
* Predictive Period - The period containing predictive records.
* Target Record - A contributor's record in the target period.
* Grouping or group - How the data has been broken into subsets also known as
  imputation class.
* Link - A ratio used as part of the imputation process.

## 2.0 Description

This document specifies the generic method used by all ratio imputation
types. The method imputes for a single numeric target variable within the
dataset and outputs a separate dataset containing the imputed variable and
other information necessary to use the imputed variable. Note that while the
input data structure only allows for a single target variable column, a user
can perform multivariable imputation by having all variables in the one target
column and adding to the grouping a column to distinguish between variables.

## 3.0 Data Records

All field names in this document are not definitive; the actual field names
must be configurable and the method used to configure these names is an
implementation detail and thus out of scope of this document. In addition,
different types of ratio imputation may require additional fields as per the
type specific specifications.

### 3.1 Input Records

Input records must include the following fields of the correct types:

* Identifier - Any
* Period - String in "YYYYMM" format
* Group - Any
* Target Variable - Numeric - Nulls Allowed
* Auxiliary Variable - Numeric

Optionally the following fields can also be passed:

* Forward Link - Numeric
* Backward Link - Numeric
* Construction Link - Numeric

Forward and backward Links must be passed in together.
Construction Links can be passed in separately.

Other fields should only be passed if required by Responder Filtering (4.1).
Unless otherwise noted, fields must not contain null values.

### 3.2 Output Records

Output records shall always contain the following fields with the following
types:

* Identifier - Any
* Period - String in "YYYYMM" format
* Target/Imputed Variable - Numeric
* Imputation Marker - String
* Forward Link - Numeric
* Backward Link - Numeric
* Construction Link - Numeric
* Forward Link Observation Count - Numeric
* Backward Link Observation Count - Numeric
* Construction Link Observation Count - Numeric
* Forward Link Default Value Marker - Boolean
* Backward Link Default Value Marker - Boolean
* Construction Link Default Value Marker - Boolean

If Responder Filtering is performed, output records shall also contain:

* Post Filter Inclusion Previous - Boolean - Nulls Allowed
* Post Filter Inclusion Current - Boolean - Nulls Allowed
* Post Filter Inclusion Next - Boolean - Nulls Allowed

If Link Weighting is being performed then the output records shall also contain:

* Unweighted Forward Link - Numeric
* Unweighted Backward Link - Numeric
* Unweighted Construction Link - Numeric

Fields of type "Any" shall be of the same type as the corresponding input
fields as the values shall be the same in both input and output records.

### 3.3 Back Data

In order to correctly handle the first period of data, the method must
accept a dataset containing back data. This dataset is used when data prior
to the first period of the input is required i.e. for the first period of
imputation and for link weighting where the calculations reference links
outside of the time period covered by the input dataset. Data provided as
part of this dataset will be combined with the results of calculations
performed by the method when required. These result sets shall be treated
equivalently. Only back data periods prior to those in the input dataset
shall be used and periods only present in the back data shall not appear in
the method output.

Back data records shall always contain the following fields:

* Identifier
* Period
* Group
* Imputed Variable
* Imputation Marker
* Auxiliary Variable

These fields must have the same types as their counterparts in the Input and
Output records.

If Link Weighting is being performed then the back data must contain the
following fields with the same types as the corresponding weighted link
equivalents in the output:

* Unweighted Forward Link
* Unweighted Backward Link
* Unweighted Construction Link

## 4.0 Method

The complete imputation method consists of a number of imputation
processes and ends only when either the target variable is fully populated
or no more values can be imputed. If the latter condition occurs without
satisfying the former then this constitutes an error condition.

In the case of an error condition, either as specified in this document or
due to any other cause, the method shall not result in any output
records. Instead a suitable error shall be emitted.

Imputation must only take place on records with no value for the target
variable and where the appropriate predictive value and link are present or
can be calculated. Given these conditions, the general imputation formula is:

```asciimath
v_(impute)((p_(target), c)) =
    v_(predictive)((p_(predictive), c)) * l((p_(target), g((c))))
```

Where:

* `v_(impute)` is the imputed value
* `v_(predictive)` is the predictive value for the given imputation process
* `p_(target)` is the target period
* `p_(predictive` is the predictive period for the given imputation process
* `c` is the contributor
* `l` is a link calculation function
* `g` is a function mapping a contributor to a grouping in the dataset

Both the link and predictive value used depend on the imputation process. In
addition, for forward and backward imputation, multiple periods must be
imputed in the correct order for the imputation process being performed
since imputes for a given contributor chain together i.e. imputes are used
as predictive values when the contributor fails to respond for a contiguous
sequence of periods such that:

`v_(predictive)((p, c)) = v_(impute)((p_(predictive), c))`

if and only if the contributor was sampled for period `p_(predictive)` and
`v_(predictive)` does not already exist.

In all cases, the predictive period for an imputation process is the same as
that of the link being used. In addition, the predictive period must be
calculated assuming a contiguous sequence of periods covering the full
inclusive range of periods in the dataset rather than based on the periods
actually present for a given contributor or in the dataset as a whole.

This document defines target and predictive periods as terms in the above
sequence and assumes that the sequence is in ascending order regardless of
the required order of processing. Also, the details of how to calculate the
sequence of periods are not covered as the method is agnostic with
regards to the periodicity of the dataset.

The following imputation processes comprise the complete method in
precedence order:

1. Forward imputation from response
2. Backward imputation
3. Imputation based on construction

Since Construction Imputation must take place before Forward Imputation from
Construction, they are collectively referred to as Construction based
Imputation in the above list. Providing the order of precedence is
preserved, the steps above may be performed in any order when constructing
the output dataset.

### 4.1 Responder Filtering

By default the method will consider all responders when calculating links.
However the method must also accept an optional expression for filtering
responders. If provided, link calculation methods will only consider responders
matching this filter. This filter will only apply to link calculations.

For each record, the respective post filtering inclusion marker fields will
be set to reflect whether the values in the previous, current and next
periods match the filter expression.

If forward, backward and construction links are all provided in the input
dataset this process will not be performed.

### 4.2 Links

Links provided as part of the input dataset will be used rather than those
calculated or weighted in accordance with the rest of this section. Providing
only one of forward or backward links is not permitted to avoid the method
assuming relationships between these links.

#### 4.2.1 Link Calculation

In link calculations, only responders present in the same group in both
target and predictive periods shall be used to calculate the ratios. These
pairs of responders are referred to as matched pairs.

The link calculation function is out of scope of this document as it
is specific to the type of Ratio Imputation being performed. However, as in
the general imputation formula above, forward, backward and construction
links must be calculated per period and grouping within the dataset, with
forward and backward links based on matched pairs. Observation counts for
the calculated links must also be provided.

#### 4.2.2 Link Defaulting

When a link is not able to be calculated, the field shall be defaulted to
`1` and the corresponding default value marker field shall be set to
`true`.

#### 4.2.3 Link Weighting

The method shall support weighting the links in the current period against
those in a prior weighting period using the following formula:

```asciimath
l_(weighted)((g, c)) =
    ((l((p_(target), g((c)))) * w))+((l((p_(target) - r, g((c)))) * ((1-w))))
```

where:

* `w` is the provided weight
* `r` is the amount of periods prior to the target period from which to
  obtain the weighting link

The above applies if and only if `l((p_(target) - r, g((c))))` exists.
Otherwise the unweighted link shall be used.

### 4.3 Forward Imputation

In this method there are two forward imputation processes. In all cases the
forward link is used, the predictive period for period `p` is `p - 1` and
the predictive value is the value of the target variable in the predictive
record. Thus this process must process periods in ascending order.

#### 4.3.1 Forward Imputation From Response

In this imputation process, only predictive records which are either
responses or forward imputes from responses can be used. Records imputed
using this process will be marked `FIR`.

#### 4.3.2 Forward Imputation From Construction

In this imputation process, only predictive records which are either
construction imputes or forward imputes from construction can be used.
Records imputed using this process will be marked `FIC`.

### 4.4 Backward Imputation

In this imputation process, the backward link is used, the predictive period
for period `p` is `p + 1` and the predictive value is the value for the
target variable for the predictive record. Thus this process must
process periods in descending order.

Only predictive records which are either backward imputes or responses can
be used. Backward imputation from construction must not occur.

Records imputed using this process will be marked
`BI`.

### 4.5 Construction Imputation

In this imputation process the construction link is used, the predictive
period for period `p` is `p` and the predictive value is the auxiliary
variable from the target record. Records imputed using this process will be
marked `C`.

As per the precedence order, Construction imputes must only be output in
cases where no other imputation process can be performed; specifically, in
the case of a contiguous sequence of periods of non-response for a
contributor, Construction imputes must only be output for the earliest
period in the sequence. However, since this rule only applies to the method
output and both the predictive and target periods are the same for this
process, the order in which periods are processed is unimportant.

For Copyright information, please see LICENCE.
