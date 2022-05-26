Date Adjustment Specification
=============================

Overview
--------

======================= ==================================================================================================================================
Descriptive             Details
======================= ==================================================================================================================================
Support Area            *Editing and Imputation Expert Group*
Version                 *1.0*
Methods reference       **Filled in by DST**
*Description*           *Date Adjustment*
                       
                        *To generate adjusted return data.*
                       
                        *When a return is received the data (eg total turnover) may be for a responder’s specified period rather than the require period.*
                       
                        *The Date Adjustment function are methods to approximate the values of the data for the required period.*
                       
                        *It is achieved by weighting the data provided by the responder.*
*Method theme*          *Editing and Imputation*
*Method classification* *Editing*
Status                  *In development*
Inputs                  *Name and description of inputs*
Outputs                 *Name and description of outputs*
======================= ==================================================================================================================================

Method Specification
--------------------

Method Specification Amendments/Change Log
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

==================== ======================================== ============================= ============================ ========================================================================================================
**Document version** **Description**                          **Author(s)**                 **Date**                     **Comments\***
==================== ======================================== ============================= ============================ ========================================================================================================
*1.0*                *First draft or amendment (minor/major)* *Name and email*              *Publication/amendment date* *What changes have been made?*
*1.0*                Initial Specification                    *Phil Lewis*                  *April 2020*                
                                                                                                                        
                                                              philip.a.lewis@ons.gov.uk                                 
                                                                                                                        
                                                              *Secondary reviewer:*                                     
                                                                                                                        
                                                              *Claire Dobbins*                                          
                                                                                                                        
                                                              *claire.dobbins@ons.gov.uk*                               
*1.1.*               *Minor formatting*                       *Jonathan Digby-North*        *17.7.2020*                  *-*
*1.2*                *Minor update*                           *JDN on behalf of Phil Lewis* *28.7.2020*                  *Method Input table the “Standard Industrial Classification code” type of variable should say “String.”*
*1.3*                *Minor update*                           *JDN on behalf of Phil Lewis* *29.7.2020*                  *Updated so Domain should be used where the look-up is required*
*1.4*                *Typo*                                   *JDN on behalf of Phil Lewis* *06.08.2020*                 *Updated rule 19*
*1.5*                *Typo*                                   *JDN on behalf of Phil Lewis* *24.08.2020*                 *Update 10.c*
==================== ======================================== ============================= ============================ ========================================================================================================

Summary
~~~~~~~

To generate summary and headline statistics for a period, the ONS
requires all businesses responses to cover the same period. However,
sometimes it is not possible for a business to provide data for the
exact period of days required. The responder may specify a different
start and end dates for which the response totals cover. Therefore, Date
Adjustment methods are required to approximate the values of the data
for the required period that are based on the data received.

Date Adjustment methods essentially weighting the totals form the
response period to approximate the values for the desired period. To
weight the data to the correct period, each day must be given a weight.
Then,

the adjusted value = original value X (Sum of the weights over the
required period) / (Sum of the weights covered by the period specified
in the response)

There are two main methods for prescribing the weights:

1. Equal weighting counts. This can be achieved by setting all the
   weights to be 1.

   The values in the return are divided by the number of days the
   response represents. Then multiplied by the number of days in the
   period required.

2. Trading day weights.

When a business responds for a different period then there should be
some overlap with the required period. However:

-  The totals provided may include days not in the required period.

-  The totals provided may just cover part of the period required.

-  The totals provided may be based on a relatively short number of
   days. For example, the response may be based on a two-week period
   when the required period required totals covers four-weeks. In this
   situation, they also may include days not in the required period.

-  The totals provided may be based on a long number of days, and so
   will include days not in the required period.

   For example, the response may be based on a six-week period when the
   required period required totals covers four-weeks.

   In this situation, they also may not cover all the days in the
   required period.

Responses from Electronic Questionnaires (eQ) have bespoke restrictions
on date ranges that may be submitted to help maintain the data quality.

For example, for RSI eQ responses:

-  A response must cover a minimum 23 days.

-  The start date may not be more than 19 days prior to the survey
   period.

Consequently, by these restrictions an eQ RSI return predominantly in
the previous period to the expected period must then have at least 4
days in the expected period.

There are different methods to determine what standard period any
non-standard period response totals should be used to generate.
Especially where a response received has not had any restrictions
imposed on what may be submitted. Though even with the eQ restrictions
there may still be choice in some instances.

The three methods used by this function, to determine what standard
period a return is use for, are:

1. Adjust to the period that the form requested.

2. Adjust to the period that contains the mid-point of the return
   period.

3. Adjust to the period that contains the mid-point of the trimmed
   return period dates.

Method 3 is for use where weights may be zero. That is, where
non-trading days may exist. For example, sometimes Saturday, Sundays or
Bank Holidays may have a trading weight of zero. If the weights may not
be zero, then it is the same as Method 2.

Though when weights may be zero, then the returns dates are considered
to

-  Start from the first day with a non-zero trading weight on or after
   the start date specified return.

-  End on the last day with a non-zero trading weight on or before the
   end date specified return.

That is, the days at the start and end of the return period are
“trimmed”. Otherwise, a different mid-point could be generated for the
same trading period days, just because non-trading days appear at the
start and /or end.

.. note::

   Very Important Note: The method only requires that one day should be
   common to both the returned period and the period to be adjusted to.
   It cannot determine how appropriate the adjusted returned period is
   at representing or generating the output for the period required.

.. _section-1:

Requirements and Dependencies
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**DTrades Background.**

-  For Distributive Trades (DTrades) a working day is any day,
   irrespective of whether it has a trading-day weight of zero.

-  The data to be data adjusted are predominantly from eQ (Electronic
   Questionnaire) returns.

Each eQ is for a standard reporting period. The UK Retail Sales Index
historically is based on a 4-4-5-week reporting period. It will change
to a calendar month period once DTrades goes live. The 4-4-5 based
method is intended to inform testing of methods (that is, to be on a
comparable basis with existing systems).

It is intended that the calendar monthly date adjustment method that
will be implemented in live production.

A 4-4-5 reporting frequency does not feature trading day effects and
causes artificial moving holidays and phase shifts that affect
seasonality. The switch to a calendar month reporting will remove these
anomalies and introduce a trading day effect.

The json (JavaScript Object Notation) file generated from the eQ
contains the standard reporting period start and end-dates as variables.
These variables reflect the required standard reporting period, they are
not dates provided by the respondent.

A business may change the start and end-dates by completing
contributor’s returned period start and end-dates. They are currently
question 11 for the start-date and question 12 for the end-date. Note
that if q11 and q12 variables are not populated in the json file then
the contributor has returned data which matches the standard reporting
period.

**DTrades eQ validation.**

For DTrades either both the “Contributor’s returned period start-date”
(q11) and the “Contributor’s returned period end-date” (q12) are blank.
Or both the “Contributor’s returned period start-date” (q11) and the
“Contributor’s returned period end-date” are populated (q12). That is,
we will not get one blank and the other populated.

**RSI**

The eQ validation ensures there is overlap with

-  the “Contributor’s returned period start-date” to the “Contributor’s
   returned period end-date” (including the start and end days) range
   and

-  the period containing the period start-date and period end-date
   (including the start and end days) range.

From eQ validation, a minimum 23 days is required. Then the start date
may not be more than 19 days prior to the survey period. Hence, a return
predominantly in the previous period to the expected period must have at
least 4 days in the expected period. Similarly, the end date may not be
more than 20 days after the survey period. Hence, a return predominantly
in the next period after the expected period must have at least 3 days
in the expected period.

**MBS**

The eQ validation restrictions does not ensure there is some overlap.
Only a minimum 10 days is required. Then the start date may not be more
than 19 days prior to the survey period. For example, if expecting a
return for April, then the contributor return dates of 22nd March to
31st March would pass eQ validation for April. However, the date range
does not overlap with April.

For further conditions on contributor’s returned period start and
end-dates from the eQ see:

https://share.sp.ons.statistics.gov.uk/sites/MTH/BusStat/Mon/Distributive_Trade/PE_and_I/DTrades-%20The%20Electronic%20questionnaire.docx

**Pre-Processing requirements for DTrades**

Before calling Date Adjustment for DTrades ensure that the following
composite SIC codes are created:

-  SIC 99901 is made up from SIC 47210, SIC 47220, SIC 47230, SIC 47240,
   SIC 47290.

-  SIC 99902 is made up from SIC 47250, SIC 47260.

-  SIC 99903 is made up from SIC 47410, SIC 47420.

-  SIC 99904 is made up from SIC 47610, SIC 47620.

-  SIC 99905 is made up from SIC 47640, SIC 47650.

.. note::

   The function should be able to distinguish between Null /Missing
   values and Empty values. Empty values are where data should not exist
   and hence should not generate an error. (See Note on “National
   Accounts Terminology: Missing, Empty, Null.”)

.. note::

   **Note on National Accounts Terminology: Missing, Empty, Null.**

1. **Missing**: refers to data points in input series where there is not
   a value expressed for a certain time period, it is not zero, but we
   do not know what it is

2. **Null:** refers to the result of a calculation that results in a
   missing value, e.g. dividing by zero or the passing through of a
   missing value

3. **Empty:** refers to data points that do not exist. So, for a time
   series that runs from 2000 to 2010, 2011 is empty but not missing.

.. note::

**Note on Emerging Platforms version of the function.**

   For DTrades: The Trading day weights inputs are to 3 decimal places.
   However, when read into pyspark the number representation includes a
   very small decimal. That is, something such as a string of zeroes
   from the 4th decimal place followed by a one. To overcome this the
   Trading day weights in the Emerging Platforms function version
   required to have rounding to 3 decimals applied.

Date adjustment must be performed before Selective Editing is called.

Assumptions and Validity
~~~~~~~~~~~~~~~~~~~~~~~~

The method only requires that one day should be common to both the
returned period and the period to be adjusted to. It cannot determine
how appropriate the adjusted returned period is at representing the
period required.

.. _section-2:

Method Input
~~~~~~~~~~~~~

.. tabularcolumns:: |p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.30\linewidth-2\tabcolsep}|

.. rst-class:: longtable



=========================================================== ================================= =========================================== ================================== =========================================================================================================== ================================== ================================= =================================================================================================================================================
Variable definition                                         Type of variable                  Format of specific variable (if applicable) Expected range of the values       Meaning of the values                                                                                       Expected level of aggregation      Frequency                         Comments
=========================================================== ================================= =========================================== ================================== =========================================================================================================== ================================== ================================= =================================================================================================================================================
*e.g. 10-digit enterprise reference number*                 *e.g. character; integer; double* *e.g. date*                                 *e.g.*                             *e.g.*                                                                                                      *e.g.*                             *e.g. quarterly, monthly, annual*
                                                                                                                                                                                                                                                                                                                                                             
                                                                                              *YYYY-MM-DD*                                *weights should be greater than 0* *Stagger = 0 indicates that the reporting period is a month*                                                *RU level;*                                                         
                                                                                                                                                                                                                                                                                                                                                             
                                                                                                                                                                                                                                                                                         *VAT unit level; Enterprise level*                                  
Expected period start-date.                                 Date                              YYYY-MM-DD                                                                     The start date of the period for which the questionnaire was issued /record intended.                       Reporting unit level                                                 For RSI, this is a variable generated by the eQ.
Expected period end-date.                                   Date                              YYYY-MM-DD                                                                     The end date of the period for which the questionnaire was issued /record intended.                         Reporting unit level                                                 For RSI, this is a variable generated by the eQ.
Date Mapping (containing the Trading Weights when required) Look-up table.                                                                                                   A mapping is required when required if                                                                      Reporting unit level                                                 If “Use calendar days” = “N” then the mapping should show what days fall in which period.
                                                                                                                                                                                                                                                                                                                                                             
                                                                                                                                                                             “Use calendar days” = “N”                                                                                                                                                        For each record, want to determine what is the earliest and what is the latest date for the period for which the data of the record should cover.
                                                                                                                                                                                                                                                                                                                                                             
                                                                                                                                                                             Or if                                                                                                                                                                            If “equal weighted” = “N” then this file should also contain the Trading Day Weights for Domain Grouping.
                                                                                                                                                                                                                                                                                                                                                             
                                                                                                                                                                             “Set-to equal weighted” = “N”.                                                                                                                                                   Conversely: If “equal weighted” = “Y” then all weights are 1.
                                                                                                                                                                                                                                                                                                                                                             
                                                                                                                                                                                                                                                                                                                                                              That is, the Trading Day Weights do not have to be present / would not be used.
                                                                                                                                                                                                                                                                                                                                                             
                                                                                                                                                                                                                                                                                                                                                              Note, when required, there is only one set of trading weights that are used for every variable to be date adjusted.
Variables to be date adjusted.                              Numeric                                                                                                                                                                                                                      Reporting unit level                                                 For example, Total Turnover.
Contributor’s returned period start-date.                   Date                                                                                                             If the responder specifies or record has a different start date than expected.                              Reporting unit level                                                 Optional.
Contributor’s returned period end-date.                     Date                                                                                                             If the responder specifies or record has a different end date than expected.                                Reporting unit level                                                 Optional.
Domain                                                      String                                                                                                           Domain group must be present in look-up table                                                               Reporting unit level                                                 The domain group is required to identify the correct Date Mapping containing the Trading day weights should to be used.
Set-to mid-point                                            Function call parameter.          Values: “YT”                                                                   “YT” = Yes and apply trimming.                                                                                                                                                  
                                                                                                                                                                                                                                                                                                                                                             
                                                                                              “Y”                                                                            “Y” = Yes and do not apply trimming.                                                                                                                                            
                                                                                                                                                                                                                                                                                                                                                             
                                                                                              “N”                                                                            “N” = use Expected period.                                                                                                                                                      
Set-to equal weighted                                       Function call parameter           Values:                                                                        “Y” = Yes.                                                                                                                                                                       *If “Y” then Trading weights are not required in the Date Mapping.*
                                                                                                                                                                                                                                                                                                                                                             
                                                                                              “Y”                                                                            “N” = No.                                                                                                                                                                        *All weights are set to be 1.*
                                                                                                                                                                                                                                                                                                                                                             
                                                                                              “N”                                                                                                                                                                                                                                                            
Use calendar days                                           Function call parameter           Values:                                                                        “Y” = Yes.                                                                                                                                                                       *If “Y” then the period that a date belongs to is the normal calendar month.*
                                                                                                                                                                                                                                                                                                                                                             
                                                                                              “Y”                                                                            “N” = No.                                                                                                                                                                        *That is, the Date Mapping does not need to show which period each day belongs to.*
                                                                                                                                                                                                                                                                                                                                                             
                                                                                              “N”                                                                                                                                                                                                                                                             *If “N” then the period that a date belongs to is the normal calendar month should be obtained by the Date Mapping look-up.*
Short-period parameter.                                     Function call parameter           Numeric.                                                                       This is used to flag a record for which from a short time period under the Date adjustment length flag.                                                                          Set to 27 for DTrades
                                                                                                                                                                                                                                                                                                                                                             
                                                                                                                                                                             The Number of Day in the Contributor’s returned period is less than or equal to the Short-period parameter.                                                                     
Long-period parameter.                                      Function call parameter           Numeric.                                                                       This is used to flag a record for which from a long time period under the Date adjustment length flag.                                                                           Set to 35.for DTrades.
                                                                                                                                                                                                                                                                                                                                                             
                                                                                                                                                                             The Number of Day in the Contributor’s returned period is greater than to the Long-period parameter.                                                                            
Average Weekly                                              parameter list                    Values                                                                         Output the average weekly value of Date Adjusted variables.                                                                                                                     
                                                                                                                                                                                                                                                                                                                                                             
                                                                                              “A”                                                                            “A” for **all** Date Adjusted Variables.                                                                                                                                        
                                                                                                                                                                                                                                                                                                                                                             
                                                                                              “N”                                                                            “N” for **none,** no average weekly values to be generated.                                                                                                                     
                                                                                                                                                                                                                                                                                                                                                             
                                                                                              List of variables                                                              List of variables = list of variables to output the average weekly value of Date Adjusted.                                                                                      
=========================================================== ================================= =========================================== ================================== =========================================================================================================== ================================== ================================= =================================================================================================================================================

Method Output
~~~~~~~~~~~~~

========================================================================== ================================= =========================================== ================================== ========================================================================================================================================= ================================== ================================= ===============================================================================================================================================================================================================================
Variable definition                                                        Type of variable                  Format of specific variable (if applicable) Expected range of the values       Meaning of the values                                                                                                                     Expected level of aggregation      Frequency                         Comments
========================================================================== ================================= =========================================== ================================== ========================================================================================================================================= ================================== ================================= ===============================================================================================================================================================================================================================
*e.g. 10-digit enterprise reference number*                                *e.g. character; integer; double* *e.g. date*                                 *e.g.*                             *e.g.*                                                                                                                                    *e.g.*                             *e.g. quarterly, monthly, annual*
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                             *YYYY-MM-DD*                                *weights should be greater than 0* *Stagger = 0 indicates that the reporting period is a month*                                                                              *RU level;*                                                         
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                                                                                                                                                                                                      *VAT unit level; Enterprise level*                                  
Actual period start-date.                                                  Date                              YYYY-MM-DD                                                                     Actual period start-date variable(s) adjusted to.                                                                                         Reporting unit level                                                 Most instances will be the same as the “Expected period start-date”.
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                                                                                                                                                                                                                                                                           This will be different when determined that data is for a different period other than the Expected period.
Actual period end-date.                                                    Date                              YYYY-MM-DD                                                                     Actual period end-date variable(s) adjusted to.                                                                                           Reporting unit level                                                 Most instances will be the same as the “Expected period end-date”.
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                                                                                                                                                                                                                                                                           This will be different when determined that data is for a different period other than the Expected period.
Date adjusted Variable(s).                                                 Numeric                                                                                                          Values of the variable(s) after date adjustment.                                                                                          Reporting unit level                                                 *If date adjustment was not required, that is the return was already for the required /expected period then the original input values will be outputted.*
The Number of Days in the Actual period.                                   Numeric                                                                                                          Counting from and including the Actual period start-date. Up to and including the Actual period end-date.                                 Reporting unit level                                                 **Note:** If the mid-point method with trimming is selected then the count will start from the first day in the Actual period with a non-zero weight. Similarly, the count will end with the day with the last non-zero weight.
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                                                            See note in last column.                                                                                                                                                                                      
The sum of the trading-day weights over the Actual period.                 Numeric                                                                                                          Counting from and including the Contributor’s returned period start-date. Up to and including the Contributor’s returned period end-date. Reporting unit level                                                
The Number of Day in the Contributor’s returned period                     Numeric                                                                                                          Counting from and including the Contributor’s returned period start-date. Up to and including the Contributor’s returned period end-date  Reporting unit level                                                 If the input Contributor’s returned period dates are not populated, then this will be same as the Actual Period. Here the Actual Period = Expected Period.
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                                                            See note in last column.                                                                                                                                                                                       **Note:** If the mid-point method with trimming is selected then the count will start from the first day in the period with a non-zero weight. Similarly, the count will end with the day with the last non-zero weight.
The sum of the trading-day weights over the Contributor’s returned period. Numeric                                                                                                          Sum from and including the Contributor’s returned period start-date. Up to and including the Contributor’s returned period end-date.      Reporting unit level                                                 If date adjustment is not required, then an output is still required. The sum of the trading-day weights over the Actual period. This is where the Actual period is the Expected period.
Date adjustment error flag                                                 Character                                                                     “E00”                              “E00” = Average Weekly parameter is invalid                                                                                               Reporting unit level                                                 If a row of data is not able to be processed, an error flag will be raised to indicate the issue with the data
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E01”                              “E01” = The value to be date adjusted is missing from one of the target columns                                                                                                                               
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E02”                              “E02” = The contributor returned end date is earlier than the contributor returned start date                                                                                                                 
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E03”                              “E03” = A required record for calculating weight m is missing from the trading weights table                                                                                                                  
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E04”                              “E04” = A required trading weight for calculating weight m is null or blank                                                                                                                                   
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E05”                              “E05” = A required trading weight for calculating weight m has a negative value                                                                                                                               
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E06”                              “E06” = A required record for calculating weight n is missing from the trading weights table                                                                                                                  
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E07”                              “E07” = A required trading weight for calculating weight n is null or blank                                                                                                                                   
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E08”                              “E08” = A required trading weight for calculating weight n has a negative value                                                                                                                               
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E09”                              “E09” = Contributors return does not cover any of expected period                                                                                                                                             
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E10”                              “E10” = The sum of trading day weights over contributors returned period is zero                                                                                                                              
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “E11”                              “E11” = The sum of trading day weights over actual period is zero                                                                                                                                             
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         Empty                                                                                                                                                                                                                                            
Date change in return period flag.                                         Character                                                                     “C”                                “C” = Change of return period.                                                                                                            Reporting unit level                                                 The Actual return period is different to the Expected return period.
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         Empty                                                                                                                                                                                                                                             This may not necessarily be an error, as for some collections:
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                                                                                                                                                                                                                                                                           a valid return for one period may submitted on a form meant for a different period.
Date adjustment length flag.                                               Character                                                                     “S”                                “S” = The contributors returned period is less than the threshold supplied in the short period parameter                                  Reporting unit level                                                 If a row of data is able to be processed, but requires the user to be notified of a potential issue,
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “L”                                “L” = The contributors returned period is greater than the threshold supplied in the long period parameter                                                                                                     a warning flag is raised for that row
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         “SL”                               “SL” = The supplied short period parameter is greater than or equal to the supplied long period parameter                                                                                                     
                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                                                                                                         Empty                                                                                                                                                                                                                                            
Average weekly Date adjusted Variable(s).                                  Numeric                                                                                                          Average weekly values of the Date adjusted of the variable(s) required.                                                                   Reporting unit level                                                 *If date adjustment was not required, that is the return was already for the required /expected period then the original input values will be used to calculate the average weekly value.*
========================================================================== ================================= =========================================== ================================== ========================================================================================================================================= ================================== ================================= ===============================================================================================================================================================================================================================

**.. note::**

   **In most cases the actual period dates should be the same as the
   expected period dates. Though they may differ, for example, if the
   mid-point method for determining the periodicity of the return is
   selected.**

.. _section-3:

Statistical Process Flow/Formal Definition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**A) Dealing with data that does not need to be Date-adjusted.**

1. If a null (or missing value) is present in the variable subject to
date adjustment, then output null for the variable(s) to be date
adjusted and place an “E01” in the Date adjustment error flag.

.. note::

   The function should be able to distinguish between Null /Missing
   values and Empty values. Empty values are where data should not exist
   and hence should not generate an error. (See Note on “National
   Accounts Terminology: Missing, Empty, Null.”)

2. If both the “Contributor’s returned period start-date” and the
“Contributor’s returned period end-date” are empty, then output the
(non-null) variable(s) to be date adjusted. That is, the reported
value(s) is(are) already for the correct time period and no adjustment
is required.

.. note::

Even if no date adjustment is required, still should output:

-  The sum of the trading-day weights over the Actual (expected) period.

-  The Number of Days in the Actual (expected) period

-  The sum of the trading-day weights over the Contributor’s returned
   period which is the sum of the trading-day weights over the Actual
   (expected) period.

-  The Number of Days Contributor’s returned period which is the same as
   the number of Days in the Actual (expected) period

**B) Preliminary stages, such as to calculate values needed to perform
Date-adjustment.**

3a.) If the “Contributor’s returned period start-date” is empty, then

set the “Contributor’s returned period start-date” = “Expected period
start-date”.

3b.) If the “Contributor’s returned period end-date” is empty, then

set the “Contributor’s returned period end-date” = “Expected period
end-date”.

4.) If the “Contributor’s returned period end-date” is before
“Contributor’s returned period start-date”

then output null for the variable(s) to be date adjusted and place an
“E02” in the Date adjustment error flag.

This is a critical stop so no further outputs such as any sums or counts
may be generated.

.. note::

For DTrades, the Electronic Questionnaire (eQ) checks should prevent
this error from occurring.

5.) If equal weight = “Y”, then populate all the trading weights to be
1.

6) By the previous (steps 3 and 4) both the “Contributor’s returned
period start-date” and “Contributor’s returned period end-date” should
be populated, as well as feasible.

Next, Trading day weights should exist for all time Contributor’s
returned period in range. That is,

-  from, and including, the “Contributor’s returned period start-date”

-  to, and including, the Contributor’s returned period end-date”.

If any required weights are missing between the “Contributor’s returned
period start-date” and the “Contributor’s returned period end-date” for
the given domain then,

set weights_m to be 0 and place an “E04” marker in the Date adjustment
error flag.

If any weights are negative between the “Contributor’s returned period
start-date” and the “Contributor’s returned period end-date” for the
given domain then,

set weights_m = 0 and place an “E05” in the Date adjustment error flag.

If any records containing weights between the “Contributor’s returned
period start-date” and the “Contributor’s returned period end-date” are
missing for the given domain then,

set weights_m = 0 and place an “E03” in the Date adjustment error flag.

Otherwise,

set weights_m to be the sum of the trading weights between the
“Contributor’s returned period start-date” and “Contributor’s returned
period end-date”, including the start and end date weights.

**If the mid-point method has been selected. That is, mid-point = “Y” or
“YT”. Then apply steps 7 – 10.**

**Otherwise if mid-point = “N” then go to step 11.**

7a) If equal weight = “Y” and mid-point = “YT” then set mid-point = “Y”.

That is, if all the weights are equal then they are 1 and so not zero.

Therefore, no trimming needs to be applied and steps 7b.) and 7c.) would
not be necessary.

7b.) If mid-point = “YT”, that is apply trimming has been selected.

Then change the “Contributor’s returned period start-date” = earliest
period on or after the input “Contributor’s returned period start-date”
with a non – zero weight. If no date with a non-zero weight is in range,
then this is a critical stop then

-  place an “E” marker in the Date adjustment error flag.

-  set weights_m to be 0.

-  Output Number of Days in the Contributor period as 0.

-  Output the sum of the trading-day weights over the Contributor period
   as 0.

-  “Contributor’s returned period start-date” = null.

-  “Contributor’s returned period end-date” = null.

-  Skip to step 11

7c.) If mid-point = “YT”, that is apply trimming has been selected. Then
change the

“Contributor’s returned period end-date” = latest period on or before
“Contributor’s returned period end-date” with a non – zero weight.

8.) Let the mid-point be the middle point date between “Contributor’s
returned period start-date” and the “Contributor’s returned period
end-date”.

That is, count the number of days starting with the “Contributor’s
returned period start-date” then up to and including “Contributor’s
returned period end-date”.

If the count is an even number,

   then the mid-point is count/2 day (with the “Contributor’s returned
   period start-date” as 1).

If the count is an odd number,

   then the mid-point is (count+1)/2 day (with the “Contributor’s
   returned period start-date” as 1).

9.) if the mid-point does lie within, or on either, the “Expected period
start-date” and “Expected period end-date” then the return is for the
Expected period.

Then

“Actual period start-date” = “Expected period start-date”.

“Actual period end-date” = “Expected period end-date”.

Otherwise

10a.) if the mid-point does not lie within, or on either, the “Expected
period start-date” and “Expected period end-date” then the return is not
for the Expected period.

Place a “C” in the Date change in return period flag.

That is, the Actual return period is different to the Expected return
period.

10b.) if “Use calendar days” = “Y” then

“Actual period start-date” = 1st day with month and year of the
mid-point.

“Actual period end-date” = last day of the month containing the
mid-point, with month and year of the mid-point.

.. note::

Be mindful of leap-years here.

10c.) Else if “Use calendar days” = “N” then use the Date Mapping:

“Actual period start-date” = Earliest date containing the mid-point
value.

“Actual period end-date” = Latest date containing the mid-point value.

11.) If mid-point = “N” then

“Actual period start-date” = “Expected period start-date”.

“Actual period end-date” = “Expected period end-date”.

12a) If mid-point not equal “YT” then

Let N be the number days between “Actual period start-date” and the
“Actual period end-date”.

This is, including the “Actual period start-date” and the “Actual period
end-date”.

Output this as the Number of Days in the Actual period.

12b) If mid-point = “YT” then

Let N be the number days counting from the first day on or after the
“Actual period start-date” with a non-zero trading day weight

up-to the last day on or before the “Actual period end-date” with a
non-zero-trading weight.

Output this as the Number of Days in the Actual period.

| 13.) If any weights are missing between the “Actual period start-date”
  and the “Actual period end-date”
| (including the “Actual period start-date” and the “Actual period
  end-date”) for the given domain then,

set weights_n = 0 and place an “E07” marker in the Date adjustment error
flag.

If any weights are negative between the “Actual period start-date” and
the “Actual period end-date” for the given domain then,

set weights_n = 0 and place an “E08” in the Date adjustment error flag.

If any records containing weights between the “Actual period start-date”
and the “Actual period end-date” are missing for the given domain then,

set weights_n = 0 and place an “E06” in the Date adjustment error flag.

Otherwise,

set weights_n be the sum of the trading weights between the “Actual
period start-date” and the “Actual period end-date” (including the
“Actual period start-date” and the “Actual period end-date”).

.. note::

If the equal weighted option has been selected then there should not be
any missing weights.

14.) Output weights_n as the sum of the trading-day weights over as the
sum of the trading-day weights over the Actual period.

**C) All other cases not covered by 1. – 2. are passed to date
Adjustment Calculation:**

15.) If the “Contributor’s returned period start-date” and
“Contributor’s returned period end-date” are null then the critical stop
in step 7b) has occurred, weights_m are 0. Then go to step 16.).

Otherwise,

Trading day weights should exist for all time Contributor’s returned
period in range. That is,

-  from, and including, the “Contributor’s returned period start-date”

-  to, and including, the “Contributor’s returned period end-date”.

If any required weights are missing then,

set weights_m to be 0 and place an “E04” marker in the Date adjustment
error flag column.

Otherwise,

weights_m be the sum of the trading weights between the “Contributor’s
returned period start-date” and the “Contributor’s returned period
end-date”, including the start and end date weights.

16.) Output weights_m as the sum of the trading-day weights over the
Contributor period.

.. note::

   If both

   “Contributor’s returned period start-date” = “Actual period
   start-date”

   and

   “Contributor’s returned period end-date” = “Actual period end-date”

   Then

   weights_m = weights_n.

That is, the sum of the trading-day weights over the Contributor period
= the sum of the trading-day weights over the Actual period.

In particular, (see step 2) where both the input “Contributor’s returned
period start-date” and input “Contributor’s returned period end-date”
were not populated.

17.) Output the “Actual period start-date” and “Actual period end-date”.
There should be some overlap between the period containing the
“Contributor’s returned period start-date” to the “Contributor’s
returned period end-date” (including the start and end days)

with

the period containing the “Actual period start-date” and “Actual period
end-date” (including the start and end days).

That is, at least one day should be common to both.

So, at least one day that appears in

-  the period from the “Contributor’s returned period start-date” to the
   “Contributor’s returned period end-date” (including the start and end
   days)

should also appear be in the

-  the period from “Actual period start-date” and “Actual period
   end-date” (including the start and end days).

If there is no overlap then the return is out of range, then

output the variable(s) to be date adjusted as null and place an “E09” in
the Date adjustment error flag column.

.. note::

   If the “Contributor’s returned period start-date” and “Contributor’s
   returned period end-date” are null then the critical stop in step 7b)
   has occurred output the variable(s) to be date adjusted as null and
   place an “ER” in the Date adjustment error flag.

.. note::

   For

-  RSI – the eQ validation ensures there is some overlap.

-  MBS - the eQ validation does not ensure there is some overlap.

..

   (See further details in “DTrades eQ validation.”.)

18.) Let M be the number of days between the contributor’s returned
period start-date and the contributor’s returned period end-date,
including the start and end dates itself.

Output this as the Number of Days in the Contributor period.

.. note::

If the “Contributor’s returned period start-date” and “Contributor’s
returned period end-date” are null then the critical stop in step 7b)
then M =0.

.. note::

If both

“Contributor’s returned period start-date” = “Actual period start-date”

and

“Contributor’s returned period end-date” = “Actual period end-date”

Then

M = N.

That is, the Number of Days in the Contributor period = the Number of
Days in the Actual period.

In particular, (see step 2) where both the input “Contributor’s returned
period start-date” and input “Contributor’s returned period end-date”
were not populated.

.. note::

   If equal weight = “Y”, that is all the weights are one then M should
   be the same as weights_m, the sum of the trading-day weights over the
   Contributor period

19.) If M is less than or equal to the Short-period parameter (set to 27
for DTrades),

   then output the ‘S’ in the Date adjustment length flag.

If M > Long-period parameter (set to 35 for DTrades),

   then output the ‘L’ in the Date adjustment length flag.

If the short-period parameter is greater than the long-period parameter

then output ‘SL’ in the Date adjustment length flag

20.) Null values in the variable(s) to be date adjusted should be dealt
by step 1.

For non-null values in the variable(s) to be date adjusted:

If weights_n = weights_m, then (similar to step 2)

-  Output as the date adjusted variable(s) to be the same value as the
   input variable(s).

Otherwise, for each variable to be date adjusted:

-  If weights_m is not 0, then for each variable to be date-adjusted:

   output date-adjusted variable= original variable x (weights_n /
   weights_m).

..

   else if weights_m = 0 then

   output null for the variable(s) to be date adjusted,

   place an “E10” marker in the Date adjustment error flag.

-  If weights_n = 0 then place an “E11” marker in the Date adjustment
   error flag.

**D Final Steps**

21.) If Average Weekly = “A” (for all) or a list of variables.

Then for each Date-Adjusted variable specified:

Average weekly Date adjusted Variable = 7 X Date adjusted Variable(s) /
N

Where N is the Number of Days in the Actual period.

.. note::

   | If any a Date adjusted Variable value has a Null value, then the
     Average weekly Date adjusted Variable value will also be null.
   | An Error Flag will have been issued when the Date adjusted Variable
     was set to null.

22.) If in the processing the output dataset record order is not the
same as the input row order. Then ideally, the output should be sorted
to match the input row order. This is especially important to help the
comparison of inputs and outputs.

**
**

**
**

.. _section-4:

Worked Example
~~~~~~~~~~~~~~

**Test plan- see date adjustment tab:**

https://share.sp.ons.statistics.gov.uk/sites/MSDRS/Documentation_/Access%20to%20Documents/Methods%20Testing/Pipeline%20method%20testing%20plan%20-%20acceptance%20criteria.xlsx

**Unit Test data:**

https://share.sp.ons.statistics.gov.uk/sites/MSDRS/Documentation_/Access%20to%20Documents/Methods%20Testing/Test%20data%20-%20date%20adjustment

.. _section-5:

Treatment of Special Cases
~~~~~~~~~~~~~~~~~~~~~~~~~~

The function should be able to distinguish between Null /Missing values
and Empty values.

Empty values are where data should not exist and hence should not
generate an error.

(See Note on “National Accounts Terminology: Missing, Empty, Null.”)

.. _section-6:

References
~~~~~~~~~~

The method is based on DTrades requirements and what is known about the
Common Software Process.

The aim is to reproduce the results that are generated in Common
Software.

However, it appears they are specified to work within the Common
Software System and may not be appropriate for more powerful, modern
software methods.

The documents that exist do not provide all the details requires and it
is believed that documents have unfortunately been deleted.

DTrades Date Adjust Calculating the mid-point:

https://share.sp.ons.statistics.gov.uk/sites/MTH/BusStat/Mon/Distributive_Trade/PE_and_I/DTrades%20Date%20Adjust%20Calculating%20the%20mid-point.docx

.. _section-7:

-  the “\ *Background*\ ” section.

-  The variable description tables discussed in the sections “\ *Input
   Data*\ ” and “\ *Output Data*\ ”

