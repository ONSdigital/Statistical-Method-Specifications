﻿***********************************
Ratio of Means Specification
***********************************


Overview
--------

======================= ==============================================================================================================
Descriptive             Details
======================= ==============================================================================================================
Support Area            Methodology - Editing & imputation  `Editing.and.Imputation.expert.group@ons.gov.uk <editing.and.imputation.expert.group@ons.gov.uk>`_.
Version                 0.8
Methods reference       **Methodology**
Description             Ratio of means imputation is a standard imputation method for business surveys. The
                        standard method, as outlined in examples 1 to 3 below, does not use any form of trimming for outliers. The
                        method is fairly robust to outliers, unless they are exceptional and they dominate an imputation class. In addition,
                        the standard method does not incorporate any weights **except** when averaging imputation links from more than one period (see 'Exceptions' below)
Method theme            Imputation.
Method classification   Ratio imputation
Status                  Partially tested, draft (not published)
Inputs                  See 'Method input table'
Outputs                 See 'Method output table'
======================= ==============================================================================================================

Method Specification
--------------------

Method Specification Amendments/Change Log
__________________________________________


====================  ==========================================   =============================  ===============  =======================================================
**Document version**  **Description**                              **Author(s)**                  **Date**          **Comments\***
====================  ==========================================   =============================  ===============  =======================================================
0.1                   Original specification                       n/k                            n/k           
0.2                   Draft specification                          Siân Lloyd                     07.02.20          link notation corrected, wording amended
0.3                   Draft specification                          Siân Lloyd                     17.03.20          wording and layout amended for clarification
0.3                   Draft specification                          Claire Dobbins - reviewer      17.03.20          0.3 subject to amendments after further testing
0.4                   Final draft specification                    Siân Lloyd                     16.04.20          links added, final draft specification subject to review
0.5                   Formatting/checking                          Jonathan Digby-North           17.04.20          Updated target links to point to this spec and formatted
0.6                   Update Method output table                   Siân Lloyd                     28.09.20          outputColumn now produces a numeric value i.e double
0.7                   Incorporate *customise* parameter - draft    Siân Lloyd                     10.03.21          Major specification modification - draft
0.7                   Incorporate *customise* parameter - draft    Danni Lewis - reviewer         16.03.21          Major specification modification - draft
0.8                   Specification modification                   Alice Gundry                   27.05.21          Deletion of construction filter and addition of mean/
		                                                                                                    median imputation as a fallback method. 		      
0.9                   Specification modification                   Alice Gundry                   22.06.21          Imputation markers updated
0.10                  Specification modification                   Alice Gundry	                  05.10.21          Incorporate additional dataframe input for when
                                                                                                                    prior data is available. Reference, period and strata
                                                                                                                    columns added to list of outputs.  
0.11                  Specification modification                   Alice Gundry                   09.11.21          Comments added for clarification on variable inputs.
														    Changes to variable names in the method input table.
0.12                  Specification modification                   Peter Davies                   08.03.22          Change to input table as per requested in SPP-5894                                                                                                                     
0.13		      Removed references to old code based	   Jonathan Digby-North		  08.03.22	    No longer relevant, added new code
0.14		      Specification modification		   Peter Davies			  17.03.22          Changes to the specification with regards to SPP-5894 
								   Claire Dobbins - reviewer      17.03.22	    with comments explaining impact on imputation links
====================  ==========================================   =============================  ===============  =======================================================


**\*** *Comments can include whether two versions of the method are
being used.*


Summary
______________

Ratio of means is an imputation method for a single numeric variable.
It uses the relationship between the variable being imputed and an auxiliary variable.
Typically, the auxiliary variable can be:

  + a previous value, from the variable of interest for the non-responder, if it is available (see forward imputation - example 1 below)
  + a consecutive value, from the variable of interest for the non-responder, if it is available (see backward imputation - example 2 below)
  + a known register-based variable for the non-responder that is well correlated with the variable of interest (e.g. frozen turnover or
    frozen employment from the IDBR - see example 3 below)


As an example, for a unit :math:`i` at time :math:`t` which has a missing value for the variable of interest :math:`y` but an available value
for the auxiliary variable :math:`x`, the imputed value for unit :math:`i` is given by:

.. math::
    y_{i,t}^{*} = \frac{\sum_{j\in{impclass}}{y_{j,t}}}{\sum_{j\in{impclass}}{x_{j,t}}}x_{i,t}

Where :math:`impclass` is the set of units in a pre-defined imputation class with responses for both
:math:`y_{j,t}` and :math:`x_{j,t}`.
The method is called ratio of means but it is more commonly expressed as a ratio of sums as there is the same number of values in both the
numerator and denominator of the fraction.
NOTE: this is because the ratio uses matched pairs of respondents i.e. they must be respondents in both time periods when using non-register
based data to calculate the ratio or the imputation link shown below.

The fraction :math:`\frac{\sum_{j\in{impclass}}{y_{j,t}}}{\sum_{j\in{impclass}}{x_{j,t}}}` is called the **imputation link**.

While the imputation link calculation is based on matched pairs of respondents, the auxiliary value that the link is then multiplied by to
create the imputed value, can be *any* status available (i.e. responded, imputed, constructed).

It is possible to remove respondents from the imputation link calculation by using the inclusion_marker column. The inclusion_marker column 
is an optional input column where a user can identify respondents that need to be excluded from the imputation link calculation. If the
inclusion_marker column is used then the values will need to have the Boolean type (True/False). The respondents that are included in the
imputation link calculations will need to be marked as True and respondents that are excluded from the imputation link calulations will 
need to be marked as False. If a given respodent is marked as False under the inclusion marker, this means they will be removed form all 
possible imputation links (i.e. forwards imputation, backwards imputation, construction imputation). By definition this marker is applied
to all respondents and therefore are not imputed for; this is purely to contorl for their contribution towards imputation link calculations.

**\*** *Please note alternative notation of these formulae e.g. de Waal et al (2011) pp244.*



Assumptions
_________________

1. The auxiliary variable should be a good predictor of the variable of interest.
2. The auxiliary variable must be available for the non-respondent.

Standard method examples
_______________________________

All the examples below relate to an imputation method that applies Ratio of Means imputation to a dataframe to predict missing values.

:Terminology: the below terms can be used interchangeably
	
     		+ **link** = a ratio.
     		+ **variable of interest** = target variable		

Example 1 - Forward Imputation
________________________________


The most common scenario is where the current period's (*t*) missing value is imputed using a previous period's (*t-1*) value as the auxiliary.
Only records that:
  
  + are actual returns free of error, i.e. have been cleaned and those that have not been imputed by either the forward, backward or constructed computations in these examples.
  + have responded in both periods
  + marked as True if the inclusion_marker column is an input

are used to calculate the link below.

Forwards Imputation Link formula
################################
.. math::
    \text{Forward link} = \frac{\text{sum(variable of interest within strata and current period)}}{\text{sum(variable of interest within strata and previous period)}}

Forwards Imputation formula
###########################

.. math::
    \text{forward link} * \text{variable of interest that relates to the non-respondent from a previous period}

**\*** *the variable of interest that relates to the non-respondent from the relative period can be any status (i.e. response, imputed, constructed).*

Example 2 - Backwards Imputation
_________________________________

In this example the method imputes a value backwards using a value from a consecutive period (*t+1*) to the period of interest (*t*).
Only records that:

  + are actual returns free of error, i.e. have been cleaned and those that have not been imputed by either the forward, backward or constructed computations in these examples.
  + have responded in both periods
  + marked as True if the inclusion_marker column is an input

are used to calculate the link below.
In addition, backward imputation is only applied to records that have been imputed with a constructed value or that were forward imputed from a constructed value.

Backwards Imputation Link formula
#################################

.. math::
    \text{Backward link} = \frac{\text{sum(variable of interest within strata and period of interest)}}{\text{sum(variable of interest within strata and consecutive period)}}

Backwards Imputation formula
############################

.. math::
    \text{backward link} * \text{variable of interest that relates to the non-respondent from a consecutive period}

**\*** *the variable of interest that relates to the non-respondent from the relative period can be any status (i.e. response, imputed, constructed).*

Example 3 - Construction Imputation
_____________________________________

In this example, the method constructs the value to be imputed using an auxiliary variable for a non-responding business where no previous period auxiliary is available;
for example, where a business never responds, or is brought into the sample for the first time, or brought back in after a break. Typically, in this case, the auxiliary variable is a known register-based
variable that is well correlated with the variable of interest, such as frozen turnover or employment from the business register (IDBR). If the inclusion_marker column is an input then
only respondents marked as True will be included in the link calculations.

Construction Link formula
##########################

.. math::
    \text{Construction link} = \frac{\text{sum(variable of interest within strata and current period)}}{\text{sum(aux variable within strata and current period)}}

Construction Imputation formula
################################

.. math::
    \text{Construction link} * \text{aux variable value for non-respondent from current period}.



Exceptions
^^^^^^^^^^

.. note:: Please note the following exceptions to the method's standard behaviour.

1. In some cases it may be appropriate to use an imputation link which is an average of imputation links for more than one.

In the simplest case this could be the average of two links.
Two further parameters would need to be specified: the lag (:math:`k`) and the weight (:math:`w`) given to each period.
In this case the imputation link is calculated as:

.. math::
    \text{imputation link} = w * \frac{\sum_{j\in{impclass}}{y_{j,t}}}{\sum_{j\in{impclass}}{x_{j,t}}} + (1 - w)*\frac{\sum_{j\in{impclass}}{y_{j,t-k}}}{\sum_{j\in{impclass}}{x_{j,t-k}}}

This could be generalised further to allow more than two links to be included in the average.

2. In some instances, a user may want to:


+ specify that the imputed value for the given target variable is constructed using links that have been calculated for another variable named by the user within a corresponding imputation class
+ specify that a variable is backwards or forwards imputed using links that have been calculated for another variable named by the user within a corresponding imputation class or use a link of 1.



Imputation rules
^^^^^^^^^^^^^^^^

* Forward impute if no response available for current period but is available from a previous period
* Rolling Forward impute if no response available for few rolling periods but is available from an earlier period
* Forwards imputation based on a previous constructed value if business never responds but auxiliary data is available for that business
* If a business is rotated out of the sample and then rotated back into the sample, values that were previously imputed (see examples 1 to 3 above) should not be used
* Use a returned survey response where available
* Mean or median imputation should be used if there is no auxiliary information available (no previous period auxiliary, no consecutive period auxiliary and no register-based auxiliary variable)
* Backwards imputation only applies to records that are constructed or are based on a constructed value; records imputed using mean imputation or based on a mean imputed value or records imputed using median imputation or based on a median imputed value 

.. note:: 

   Mean imputation: impute a mean value for non-responders based upon current period respondents within the same imputation class
   
   Median imputation: impute a median value for non-responders based upon current period respondents within the same imputation class

Example
_______

.. figure:: ./images/imputation_types.png
		:target: "../../../build/html/specifications/imputation/ratio_of_means/ratio_of_means.html"


Treatment of special cases
__________________________

If a value of :math:`y_{i,t}` is missing then it is imputed in the following way:

#. If a unit never responds, then their first value will be constructed and subsequent values will be forward imputed.
#. When a non-responder responds for the first time, at time :math:`t = T > 1` then the values for :math:`t = 1, …, T-1` are backwards imputed.
   If it is a non-responder in future periods, then these values will be forward imputed from a returned value or a forward imputed value if more than one consecutive period is missing.
#. A unit responds :math:`t = 1` but does not respond at a time :math:`t = T > 1`.
   This value is forward imputed either from a returned value or a forward imputed value if more than one consecutive period is missing.

Example Data
____________

The example data link can be found in the `Statistical Methods Library repostitory <http://np2rvlapxx507/DAP-S/Statistical_Methods_Library/tree/feature/SML_94/resources>`_.



Method input table (required to generate output)
________________________________________________

=========================================================== ================================= =========================================== ================================== =========================================================================================================== ================================== ================================= ============================================================================================================================================================================
Variable definition                                         Type of variable                  Format of specific variable (if applicable) Expected range of the values       Meaning of the values                                                                                       Expected level of aggregation      Frequency                         Comments
=========================================================== ================================= =========================================== ================================== =========================================================================================================== ================================== ================================= ============================================================================================================================================================================                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
Unique identifier                                           Any                                                                                                                                                                                                                          Reporting unit level                                                                                                                                        
Imputation class                                            Any                                                                                                                                                                                                                          Reporting unit level                                                 Used to partition the data into imputation classes. Multiple columns can be used to define the imputation class.                                                        
Period                                                      Date                                                                                                                                                                                                                         Reporting unit level
Target variable                                             Numeric                                                                                                                                                                                                                      Reporting unit level                                                 The method will assume present values are returned values and blank cells are to be imputed. 
Auxiliary variable                                          Numeric                                                                                                                                                                                                                      Reporting unit level 
Forwards imputation link*                                   Numeric                                                                                                                                                                                                                      Reporting unit level                                                 Optional variable. Should be provided if the user would like to forwards impute values using an alternative forwards imputation link. Can contain nulls.                                                                                                                                                                                                                       
Backwards imputation link*                                  Numeric                                                                                                                                                                                                                      Reporting unit level                                                 Optional variable. Should be provided if the user would like to backwards impute values using an alternative backwards imputation link. Can contain nulls. 
Construction imputation link*                               Numeric                                                                                                                                                                                                                      Reporting unit level                                                 Optional variable. Should be provided if the user would like to construct values using an alternative construction imputation link. Can contain nulls.  
Periodicity (function call parameter, not a variable)       String                                                                        "A", "M", "Q", "Annually",         "A"/"Annually" = data provided annually   
                                                                  
                                                                                                                                          "Monthly", "Quarterly"             "M"/"Monthly" = data provided on a monthly basis

                                                                                                                                                                             "Q"/"Quarterly" = data provided on a quarterly basis   
Back data                                                   DataFrame                                                                                                                                                                                                                    Reporting unit level                                                 Optional parameter. Single period of data to be provided when prior data is avilable for imputation. This period must be the previous period with 
                                                                                                                                                                                                                                                                                                                                                              regards to the periodicity of the dataset being imputed and must be fully imputed e.g. if you are looking to impute for February, March, April then 
                                                                                                                                                                                                                                                                                                                                                              this column refers to January (the period prior to the period range where imputation is required) and should include all data types (e.g. response, imputed and constructed 
                                                                                                                                                                                                                                                                                                                                                              values). If not provided, the method will perform standard imputation. 
                                                                                                                                                                                                                                                                                                                                                              The dataset must contain the unqiue identifier, imputation class, period, output variable, imputation marker, forwards imputation link, backwards imputation link and 
                                                                                                                                                                                                                                                                                                                                                              construction link columns.
inclusion_marker                                            Boolean                                                                                                                                                                                                                      Reporting unit level                                                 This is an optional input that indicates if a contributor should be excluded from the imputation link calculations. Values accepted are True or False, if blank then en error
																																			 								      will be produced.
                                                                                                                                                                                                                                                                                                                                                               
                                                                                                                                                                                                                                                                                                                                                                
                                                                                                                                                                                                                                                                                                                                                              
                                                                                                                                                                                                                                                                                                                                                               
                                                                                                                                                                                                                                                                                                                                                                
                                                                                                                                                                                                                                                                                                                                                              
                                                                                                                                                                                                                                                                                                                                                                               
                                                                                                                                                                                                                                                                                                                                                              
=========================================================== ================================= =========================================== ================================== =========================================================================================================== ================================== ================================= ============================================================================================================================================================================

* If the forward imputation link, backward imputation link and construction imputation link columns are provided then the links should be supplied for all periods and observations. Any blanks in these columns will be defaulted to 1. 

Method output table
___________________

=========================================================== ================================= =========================================== ================================== =========================================================================================================== ================================== ================================= =================================================================================================================================================
Variable definition                                         Type of variable                  Format of specific variable (if applicable) Expected range of the values       Meaning of the values                                                                                       Expected level of aggregation      Frequency                         Comments
=========================================================== ================================= =========================================== ================================== =========================================================================================================== ================================== ================================= =================================================================================================================================================                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  
Unique identifier                                           Any            
Imputation class                                            Any
Period                                                      Date 
Output variable                                             Numeric                                                                                                                                                                                                                      Reporting unit level                                                 Contains returned and imputed values                                                                                                                                                                                                                           
Imputation marker                                           Character                                                                     "FIR", "FIC", "FIMN", "FIMD", "BI" "FIR" = forwards imputed value from a returned value                                                        Reporting unit level 
                                                                                                                                          , "C", "R", "MNI", "MDI"  
                                                                                                                                                                             "FIC" = forwards imputed value from a constructed value

                                                                                                                                                                             "FIMN" = forwards imputed value from a mean imputed value 
                                                                                                                                                                             
                                                                                                                                                                             "FIMD" = forwards imputed value from a median imputed value 
                               
                                                                                                                                                                             "BI" = backwards imputed value

                                                                                                                                                                             "C" = constructed value
 
                                                                                                                                                                             "R" = returned value 
                                                                                                                                                                              
                                                                                                                                                                             "MNI" = mean imputed value 

                                                                                                                                                                             "MDI" = median imputed value 
  
Forwards imputation link                                    Numeric                                                                                                                                                                                                                      Reporting unit level                                                 
Backwards imputation link                                   Numeric                                                                                                                                                                                                                      Reporting unit level
Construction imputation link                                Numeric                                                                                                                                                                                                                      Reporting unit level
=========================================================== ================================= =========================================== ================================== =========================================================================================================== ================================== ================================= =================================================================================================================================================

Code
_____

Python
^^^^^^

.. currentmodule:: statistical_methods_library.imputation

.. autofunction:: impute



References
----------
**De Waal, T., Pannekoek, J. and Scholtus, S.** (2011) Handbook of Data Editing and Imputation. New York: Wiley and Sons.
