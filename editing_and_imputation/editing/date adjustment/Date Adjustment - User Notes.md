# Date Adjustment User Notes
Once the date adjustment method is available on your computer you will be able
to call the method and perform date adjustment on a dataset. The basic date
adjustment method is:

Adjusted response=Original response * Sum of the weights of the desired period
/Sum of the weights of the response

Below is a snapshot of an example dataset and how the input data should look
like:
 
| Reference | Contributors start date | Contributors end date | Q20 | Expected start date | Expected end date | Domain | Mid-point | Equal-weighted | Calendar days | Average Weekly | Short period | Long period | Error Column |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 20220601 | 20220630 | 1184 | 20220601 | 20220630 | A | N | N | N | N | 24 | 42 | |
| 2 | 20220604 | 20220630 | 2045 | 20220601 | 20220630 | A | N | N | N | N | 24 | 42 | |
| 3 | 20220601 | 20220624 | 2013 | 20220601 | 20220630 | A | N | N | N | N | 24 | 42 | |
| 4 | 20220601 | 20220704 | 1992 | 20220601 | 20220630 | A | N | N | N | N | 24 | 42 | |
| 5 | 20220530 | 20220628 | 1027 | 20220601 | 20220630 | A | N | N | N | N | 24 | 42 | |

Below is a snapshot of an example data and how the trading day data should
look like:

| date | domain | weight | period | period_start | period_end |
| --- | --- | --- | --- | --- | --- |
| 20220528 | A | 0 | 202205 | 20220501 | 20220531 |
| 20220529 | A | 0 | 202205 | 20220501 | 20220531 |
| 20220530 | A | 0.2 | 202205 | 20220501 | 20220531 |
| 20220531 | A | 0.2 | 202205 | 20220501 | 20220531 |
| 20220601 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220602 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220603 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220604 | A | 0 | 202206 | 20220601 | 20220630 |
| 20220605 | A | 0 | 202206 | 20220601 | 20220630 |
| 20220606 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220607 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220608 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220609 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220610 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220611 | A | 0 | 202206 | 20220601 | 20220630 |
| 20220612 | A | 0 | 202206 | 20220601 | 20220630 |
| 20220613 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220614 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220615 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220616 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220617 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220618 | A | 0 | 202206 | 20220601 | 20220630 |
| 20220619 | A | 0 | 202206 | 20220601 | 20220630 |
| 20220620 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220621 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220622 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220623 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220624 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220625 | A | 0 | 202206 | 20220601 | 20220630 |
| 20220626 | A | 0 | 202206 | 20220601 | 20220630 |
| 20220627 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220628 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220629 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220630 | A | 0.2 | 202206 | 20220601 | 20220630 |
| 20220701 | A | 0.2 | 202207 | 20220701 | 20220731 |
| 20220702 | A | 0 | 202207 | 20220701 | 20220731 |
| 20220703 | A | 0 | 202207 | 20220701 | 20220731 |
| 20220704 | A | 0.2 | 202207 | 20220701 | 20220731 |


Here we have 5 respondents with all the columns needed to perform the date
adjustment method:
* Reference: Unique to each respondent
* Contributors start date: Start date returned by the contributor
* Contributors end date: End date returned by the contributor
* Q20: Variable response that needs to be date adjusted
* Expected start date: Start date the user is expecting from the
contributor
* Expected end date: End date the user is expecting from the contributor
* Domain: Domain classification
* Mid-point: Indicator as to whether the mid-point method needs to be used.
* Equal-weighted: Indicator as to whether the equal-weighted method needs
to be used.
* Calendar days: Indicator as to whether the calendar days method needs to
be used.
* Average weekly: Indicator as to whether the average weekly method needs
to be used
* Short period: A value that shows the user whether a response is of a short
time frame
* Long period: A value that shows the user whether a response is of a
long-time frame
* Error column: A column that will be populated if any errors occur

We also have trading day weights for all dates present and for the domain we
are interested in:
* Date: Date we are interested in
* Domain: Domain classification
* Weight: Weight for the specific trading day
* Period: An indicator of the period.
* Period_start: Start date of the period
* Period_end: End date of the period

We can then run the method as shown below ensuring that we are calling the
right columns from the dataframe:
 
![image](https://user-images.githubusercontent.com/87982871/205026679-9fb0c6af-fa95-41c2-a5dc-42a0adf2d532.png)

The output gets exported as a csv file and will give you the adjusted responses
along with the sum of the weights. 
 
New columns are produced from running the method and are described below:
* Sum of trading day weights over contributors period: The trading day weights
will be summed over the dates returned, all dates are inclusive.
* Number of days in contributors returned period: The number of days returned
by the contributor, all dates are inclusive.
* Actual period start date: The start date the user is looking for (usually
similar to expected start date).
* Actual period end date: The end date the user is looking for (usually similar
to expected end date).
* Number of days in actual returned period: The number of days the user set 
* Sum of trading day weights over actual period: The sum of the trading weights
over the days set by the user.
* Date adjusted Q20: The adjusted question value based on the sum of the trading
days weights ratio.
