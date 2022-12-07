# Date Adjustment specification

## 1.0 Meta

* Support area – Methodology – Editing and Imputation
* Method Theme – Editing
* Status – Fully tested

## 2.0 Terminology

* Expected period start date – The expected start date of the period set
by the user.
* Expected period end date – The expected end date of the period set by
the user.
* Variable(s) to be date adjusted – The user can select one or more variables
to be date adjusted.
* Contributor’s returned start date – The start date of the period returned
by a respondent/observed in the data.
* Contributor’s returned end date – The end date of the period returned by
a respondent/observed in the data.
* Domain – Classification group.
* Set to mid-point – Allows the user to apply the mid-point method
described below.
* Set to equal weighted – Allows the user to apply the equal weighted
method described below.
* Use calendar days – Allows the user to apply the calendar days method
described below.
* Average weekly – Allows the user to apply the average weekly method
described below.
* Short period parameter – This is used to raise a flag to alert the
user that the contributor’s returned period is short.
* Long period parameter – This is used to raise a flag to alert the
user that the contributor’s returned period is long.
* Date mapping – This is a file which contains dates and relevant trading
day weights per domain.
* Trading day weights: These are weights associated with each day to
allow the user to give a higher value to certain days relative to others
in a given period. For example setting weights of 0.2 for weekdays and 0
for weekends when considering turnover data values would imply that
turnover is not generated on the weekend and is stable throughout
weekdays.
* Sum of trading day weights over contributor's period: The trading day
weights will be summed over the dates returned; all dates are inclusive.
* Number of days in contributor's returned period: The number of days
returned by the contributor; all dates are inclusive.
* Actual period start date: Will appear on the output dataset and is
the start date that the output is based on.
* Actual period end date: Will appear on the output dataset and is the
end date that the output is based on.
* Number of days in actual returned period: The number of days the user
set.
* Sum of trading day weights over actual period: The sum of the trading
weights over the days set by the user.
* Date adjusted variable: The adjusted data value based on the sum of
the trading days weights ratio.

## 3.0 Summary

To generate summary and headline statistics for a reporting period is
important to ensure that data reported are on a consistent basis
referencing the same period. However, sometimes it is not possible for
data to be reported for the exact period of time required, e.g. monthly,
quarterly, yearly. The responder may specify different start and end
dates for which the observed (referenced as response throughout this
specification) data cover. The Date Adjustment method weights the
data values from the response period to approximate the values for the
desired (expected) period, either by giving varying weights to trading
days or giving each date the same weight.

## 4.0 Assumptions

* All data inputs required by the method are available.
* Each respondent is clearly classified into one mutually exclusive domain
group.
* All trading day weights are equal or greater than 0.
* Trading day weights are available for all periods and domains, or the
equal weighted option is set to yes.

## 5.0 Method input and output

All field names in this document are not definitive; the actual field names
must be configurable, and the method used to configure these names is an
implementation detail and thus out of scope of this document.

### 5.1 Input records

Input records must include the following fields of the correct type and be
certain values listed below:

* Unique identifier- Any
* Contributor’s returned start date – YYYYMMDD
* Contributor’s returned end date – YYYMMDD
* Expected period start date – YYYYMMDD
* Expected period end date – YYYYMMDD
* Domain – string
* Variable(s) to be adjusted – List of variables to be adjusted
where the values are floats.
* Set to mid-point – Y or YT or N
* Set to equal weight – Y or N
* Use Calendar days – Y or N
* Average weekly – A or N
* Short period parameter – integer
* Long period parameter - integer
* Unless otherwise noted, fields must not contain Null values. All other
fields shall be ignored.

### 5.2 Output records

Output records shall always contain the above variables in addition to the
below variables with the following types:

* Actual period start date – YYYYMMDD
* Actual period end date - YYYYMMDD
* Sum of trading day weights over contributors’ period - Float
* Number of days in contributors returned period - Float
* Number of days in actual returned period -Float
* Sum of trading day weights over actual period - Float
* Adjusted variable(s) – Float
* Error flag – String

## 6.0 Method

### 6.1 Date adjustment

A contributor will give a response for a period, this does not have to match
the expected period, and will be fed into the method. Data which have the
same returned and expected start and end dates may have this method applied
but no adjustments will be made as the input data is fit
for purpose. Where this is not the case trading day weights are
assigned to each day covered by the contributor’s returned period and the
expected period. These are then summed to provide a sum for the total trading
day weights for the returned and expected periods respectively. The expected
period total trading day weights are divided by the contributor’s returned
period total trading day weights. This ratio is then applied to the
contributor’s response so that it is representative of the expected period.

#### 6.1.1 Incomplete or erroneous data provided

Contributors may not always provide full or correct data and the Date
Adjustment method will handle it as detailed below:

* If data is not returned then an E01 flag will be raised in the output.
* If a contributor’s returned start date is empty then it will default
to the expected period start date.
* If a contributor’s returned end date is empty then it will default to
the expected period end date.
* If the contributor’s returned start and end date are empty then it
will be assumed that the response is already in the correct period.
* If the contributor’s returned end date is before the contributor’s
returned start date, then the output will be Null for the variables to
be date adjusted and place an E02 in the date adjustment error flag.
* If any required dates are missing from the trading day weights, then
return an E03 marker in the date adjustment error flag.
* If any required trading day weights are missing between the
contributor’s returned start date and end date or the expected start and
end date then an E04 marker is placed in the date adjustment error flag.
* If any required trading day weights are negative between the contributor’s
returned start date and end date, then an E05 marker is place in the date
adjustment error flag.

### 6.2 Set to Mid-point – set as Y or YT or N

A mid-point method can be used in Date Adjustment to check whether a
contributor’s returned dates are within the expected period. If the
mid-point of the contributor’s returned start and end dates are outside
the expected period, then a “C” flag is raised in the error flag column
inform the user that the contributor’s response data aligns with a different
reporting period (i.e. not the expected period). If the respondent’s
mid-point does lie outside of the period the user is
interested in then date adjustment will not occur.

There are two ways to use the mid-point method: set the mid-point to “Y”
and set the mid-point to “YT”.

Setting the mid-point input parameter to “Y” will simply calculate the
mid-point of the days returned. If the number of days in the contributor’s
returned period are even then divide the count by 2 and add that to the
contributor’s returned start date to find the mid-point. If the number of
days in the contributor’s returned period are odd then add 1 to the count
and divide by 2. This is then added onto the contributor’s returned start
date. If the user sets the mid-point input parameter to “YT”, then the method
will trim any weighted days at the start or end of the contributor’s returned
dates if they have trading day weights set to 0 and do the same calculation
as above with regards to the number of days in the period. Therefore, each
method could provide a slightly different answer.

If the user does not want the mid-point method to occur, then please set
this to “N”.

### 6.3 Use calendar days – set as Y or N

If the mid-point lies outside of the expected period start and end dates, a
“C” flag is raised in the Date change column. Calendar days function will only
work when there is a “C” flag present. The calendar days function, when set to
“Y”, allows the user to automatically set the expected start and end period
dates to the first day and last day of the month where the mid-point lies.
When the calendar days function is set to “N” it allows start and end period
dates to be set to the start and end dates that “C” flag lies in.

### 6.4 Set to equal weighted – set to Y or N

The equal weighted method is where all the trading day weights are set to 1
instead of having a unique trading day weight associated with each day. This
can be a useful feature when each day is of equal importance with respect to
the data collected. To use this feature, the equal-weighted column should
be marked as “Y”.

If the user does not want the trading day weights to be equal-weighted, then
please set this to “N”.

### 6.5 Average weekly – set to A or N

The average weekly function allows the responses to be given on a weekly value
rather than the period specified by the user. This can be a useful tool when
the user’s period isn’t always equal, to allow the user to compare similar
values. To use this feature, the average weekly value will need to be set
to “Y”.

If the user does not want to have average weekly values in the output, then
please set this to “N”.

## 7.0 Date Adjustment example

This is a simple example of date adjustment, please see the Date Adjustment
user notes for more detailed scenarios. We are going to use the equal weighted
function described above to help detail how the sum of the trading day weights
are calculated.

| Reference | Contributor period start date | Contributor period end date | Variable to be date adjusted | Expected period start date | Expected period end date | Domain | Set to mid-point | Set to equal weighted | Use calendar days | Short period parameter | Long period parameter | Average Weekly |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 20220601 | 20220628 | 5000 | 20220601 | 20220630 | 1 | N | Y | N | 20 | 45 | N |
| 2 | 20220601 | 20220630 | 4800 | 20220601 | 20220630 | 1 | N | Y | N | 20 | 45 | N |
| 3 | 20220601 | 20220624 | 5000 | 20220601 | 20220630 | 1 | N | Y | N | 20 | 45 | N |

The above table a potential input into the date adjustment method and the
table below is an example output.

| Reference | Contributor period start date | Contributor period end date | Variable to be date adjusted | Expected period start date | Expected period end date | Domain | Set to mid-point | Set to equal weighted | Use calendar days | Short period parameter | Long period parameter | Average Weekly | Sum of contributors trading day weights | Sum of expected trading day weights | Date adjusted variables |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 20220601 | 20220628 | 5000 | 20220601 | 20220630 | 1 | N | Y | N | 20 | 45 | N | 28 | 30 | 5357.14 |
| 2 | 20220601 | 20220630 | 4800 | 20220601 | 20220630 | 1 | N | Y | N | 20 | 45 | N | 30 | 30 | 4800 |
| 3 | 20220601 | 20220704 | 6000 | 20220601 | 20220630 | 1 | N | Y | N | 20 | 45 | N | 34 | 30 | 5294.12 |

* Reference 1 has returned a shorter period then the expected period, returned
28 days and we are expecting 30, therefore we can see that the variable has been
date adjusted and has increased proportionately in relation to the weights.
* Reference 2 has returned the same period we want for our expected period;
therefore, the sum of the weights will be equal and therefore a change has not
occurred.
* Reference 3 has returned a longer period than the expected period, returned
34 days and we are expecting 30, therefore we can see that the variable has been
date adjusted and has decreased proportionately in relation to the weights.
