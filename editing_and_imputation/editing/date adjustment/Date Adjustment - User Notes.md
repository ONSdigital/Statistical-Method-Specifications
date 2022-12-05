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
* Contributors start date: Start date returned by the contributor.
* Contributors end date: End date returned by the contributor.
* Q20: Variable response that needs to be date adjusted.
* Expected start date: Start date the user is expecting from the
contributor.
* Expected end date: End date the user is expecting from the contributor
* Domain: Domain classification.
* Mid-point: Indicator as to whether the mid-point method needs to be used.
* Equal-weighted: Indicator as to whether the equal-weighted method needs
to be used.
* Calendar days: Indicator as to whether the calendar days method needs to
be used.
* Average weekly: Indicator as to whether the average weekly method needs
to be used.
* Short period: A value that shows the user whether a response is of a short
time frame.
* Long period: A value that shows the user whether a response is of a
long-time frame.
* Error column: A column that will be populated if any errors occur.

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

```
import pandas as pd
import date_adjustment as date_adjust

# Import datafile containing the respondent's data
datafile = "DA_user_notes_test_data.csv"

df = pd.read_csv(datafile)

# Import trading day weights file
trading_day_datafile = "DA_user_notes_trading_day_weights.csv"

trading_df = pd.read_csv(trading_day_datafile)

# Match up column names with with variables below
output = date_adjust.date_adjustment(input_dataframe = df, # Respondent's input dataframe
                    trading_weights = trading_df, # trading day weights dataframe
                    target_columns = ['Q20'], # The variable that needs to be date adjsuted
                    contributor_returned_start_date_col = "Contributors start date", # Contributor returned start date
                    contributor_returned_end_date_col = "Contributors end date", # Contributor returned end date
                    expected_start_date_col = "Expected start date", # Expected start date
                    expected_end_date_col = "Expected end date", # Expected end date
                    domain_col = "Domain", # Domain classification
                    short_period_parameter_col = "Short period", # Short period
                    long_period_parameter_col = "Long period", # Long period
                    equal_weighted_col = "Equal-weighted", # Equal-weighted column
                    set_to_mid_point_col = "Mid-point", # Mid-point indicator column
                    use_calendar_days_col = "Calendar days", # Calendar days column
                    average_weekly_col = "Average Weekly", # Average weekly indicator
                    da_error_flag_col = "Error column", # Column for errors to be populated
                    trading_date_col = "date", # Date in the trading day dataframe
                    trading_period_start_col = "period_start", # Start date column in trading day dataframe
                    trading_period_end_col = "period_end", # End date column in trading day dataframe
                    trading_weights_col = "weight", # Weight values for each date
                    trading_domain_col = "domain" # Domain for each date and weight
                    )
                    
# Exporting the output file to csv
output.to_csv("user_notes_test_data_output2.csv")
```

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
