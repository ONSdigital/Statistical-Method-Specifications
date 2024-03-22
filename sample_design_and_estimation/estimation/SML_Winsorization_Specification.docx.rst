Winsorisation Specification
===========================

Overview
--------

======================= =============================================================
Descriptive             Details
======================= =============================================================
Support Area            *Sample design & estimation expert group*
Version                 *Latest version*
Methods reference       **Filled in by DST**
*Description*           *High level synopsis of the method*
*Method theme*          *Specify broad group of methods this method belongs to.*
*Method classification* *Specify type of method*
Status                  *Prototype, peer review, meets coding standards, unit tested*
Inputs                  *Name and description of inputs*
Outputs                 *Name and description of outputs*
======================= =============================================================

Method Specification
--------------------

Method Specification Amendments/Change Log
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

==================== ======================================== ============================= ============================ =======================================================================================================================================================================================================================================================================================================================================
**Document version** **Description**                          **Author(s)**                 **Date**                     **Comments\***
==================== ======================================== ============================= ============================ =======================================================================================================================================================================================================================================================================================================================================
*1.0*                *First draft or amendment (minor/major)* *Name and email*              *Publication/amendment date* *What changes have been made?*
*1.0*                *Final spec*                             *Laura Kirkham                *21.02.2020*                 *More detail – background and method.*
                                                              \ *\ laura.kirkham@ons.gov.uk                             
                                                                                                                         *Nuances around expansion / ratio estimation. Correct notation.*
                                                                                                                        
                                                                                                                         *Worked example.*
                                                                                                                        
                                                                                                                         *Limitations & dependencies.*
                                                                                                                        
                                                                                                                         *Input & output data.*
                                                                                                                        
                                                                                                                         *Alternative methods.*
                                                                                                                        
                                                                                                                         *Special case treatment.*
*2.0*                *Final spec*                             *Laura Kirkham*               *13.5.2020*                  *Detail surrounding calibration weights. Correction*
*2.1*                                                         *Laura Kirkham*               *9.6.2020*                   *Small typo in example. Notation clearer. Made consistent.*
*3.0*                *Final spec*                             *Laura Kirkham*               *2.7.2020*                   -  *Mathematical error in* :math:`{\widehat{\mu}}_{i}\ `\ *– this only impacts the combined ratio estimation calculation*
                                                                                                                        
                                                                                                                         -  *Amended definition of design-weight* :math:`a_{i}`\ *, to inform the user to use the birth-death-adjusted design-weight if that was the choice during Estimation.*
                                                                                                                        
                                                                                                                         -  *Amended notation to reflect changes.*
                                                                                                                        
                                                                                                                         -  *Removed* :math:`g_{h} = 1` *for expansion estimation – agreed with DST previously not to include dummies for variables which are not required*
*3.1*                *Minor changes*                          *Laura Kirkham*               *7.4.2021*                   *Extra information around data checks, clarity around descriptions. Process flow .png file added to Jira*
*4.0*                *Final spec*                             *Laura Kirkham*               *3.2.2022*                   *Changing way method deals with data inputs to prevent requirement for superfluous pre and post wrangling. Extra output column to indicate if wins has occurred. Makes method more robust. Removed extra output columns no longer being produced by the method (method outputs minimum requirement). Added some more scenarios to test*
*4.1*                *Minor changes*                          *Laura Kirkham*               *4.2.2022*                   *Changed notation to differentiate between stratum and calibration group in indexing. Removed differences between separate and ratio Winsorisation in equations – indicated difference in notation of j*
*4.2a*               *Minor changes*                          *Laura Kirkham*               *3.3.2022*                   *Adding information around the priority of flags*
*4.2b*               *Minor changes*                          *Laura Kirkham*               *12.4.2022*                  *Included l-value column in input dataset to reflect SML code*
*4.3*                *Minor changes*                          *Laura Kirkham*               *13.5.2022*                  *Consolidating 4.2a & 4.2b*
==================== ======================================== ============================= ============================ =======================================================================================================================================================================================================================================================================================================================================

**\*** *Comments can include whether two versions of the method are
being used.*

.. _section-1:

Summary
~~~~~~~

Winsorisation (sometimes known as Winsorization) can be one-sided or
two-sided. Usually one-sided Winsorisation, where large extreme values
are outliered, is used for surveys in the ONS. This specification
explains one-sided Winsorisation. Two sided Winsorisation is not
recommended because it is not optimal in any way; the choice of L-value
is subjective, thus two-sided Winsorisation equates to trimming.

The method uses a pre-calculated parameter, ‘L-value’ (supplied by
Methodology), to calculate a threshold for each return using one of two
methods; the decision is founded on whether expansion estimation or
(combined) ratio estimation is being used for the variable.

If a return is deemed influential by the method (greater or lower than
the calculated threshold), a new outlier weight, ‘o-weight’, is
calculated which will reduce the value of the return, to the most
extreme retained value, when subsequently grossed.

An observation is considered influential if its value is correct, but
its weighted contribution has an excessive effect on the estimated total
or period-to-period change. Although influential values occur
infrequently in economic surveys, if one appears and is not “treated,”
it may introduce substantial over- or under-estimation of survey totals
or period-to-period change. If the sample contains no influential
values, the procedure is anti-conservative in that it adjusts values not
considered influential to minimize the MSE (by reducing the variance).
In contrast, the procedure can become very conservative depending on the
degree of difference of the weighted influential value from the others
in the sample. When the sample contains two or more influential values,
Winsorisation detects and adjusts only the influential values and does
not trim any values that are not influential.  [1]_

**Strengths:**

-  Statistically & algebraically rigorous optimised method

-  No underlying assumptions (there are some assumptions for the
   calculation of the L-value parameter; these are not necessary for
   this specification and are not tested in practice)

-  Reduces variance in a way which is optimal to reduce MSE (when the
   L-value parameter is optimised)

**Limitations:**

-  Introduces bias – the level of bias is controlled by the L-value

-  L-value calculation can be problematic (many ways to calculate,
   subjectivity required)

-  Masking can occur – when a large value masks the identification of
   other extreme values which subsequently may not be treated by
   Winsorisation

**Alternative methods:**

-  `Trimming <https://collaborate2.ons.gov.uk/confluence/display/SML/Trimming>`__

-  Manual/surprise outliering (also sometimes incorrectly referred to as
   post-stratification)

-  Two-sided Winsorisation

No guidance for recommended size of outlier-weight; the size of the
o-weight is dependent on how large the influencing outlier is.

.. _section-2:

Requirements and Dependencies 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  Requires design-weights, :math:`a_{i}` for Winsorisation using
   Expansion Estimation.

-  Requires design-weights, :math:`a_{i}`, and calibration-weights,
   :math:`g_{i}` for Winsorisation using Ratio Estimation.

-  Assume data has been cleaned and processed in editing & imputation
   method prior to the running of this method.

-  Requires a pre-calculated parameter, ‘L-value’, for each variable
   (and potentially different time periods), supplied by Methodology.
   Any changes to this parameter should be subject to Methodology’s
   advice.

-  Grossing depends on having already run Winsorisation, as the o-weight
   calculated in Winsorisation is used in Grossing.

.. _section-3:

Assumptions and Validity
~~~~~~~~~~~~~~~~~~~~~~~~

-  All values being used from previous datasets are valid observations
   from the target population.

-  All auxiliary data being used is correct.

-  No operational assumptions.

.. _section-4:

.. _section-5:

Method Input
~~~~~~~~~~~~~

.. tabularcolumns:: |p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.30\linewidth-2\tabcolsep}|

.. rst-class:: longtable



Method expects a complete input dataset – ie. no missing values.

=========================================== ================================= =========================================== ================================== ============================================================ ======================================================================================== ================================= ========================================================================================================================================================
Variable definition                         Type of variable                  Format of specific variable (if applicable) Expected range of the values       Meaning of the values                                        Expected level of aggregation                                                            Frequency                         Comments
=========================================== ================================= =========================================== ================================== ============================================================ ======================================================================================== ================================= ========================================================================================================================================================
*e.g. 10-digit enterprise reference number* *e.g. character; integer; double* *e.g. date*                                 *e.g.*                             *e.g.*                                                       *e.g.*                                                                                   *e.g. quarterly, monthly, annual*
                                                                                                                                                                                                                                                                                                                                                    
                                                                              *YYYY-MM-DD*                                *weights should be greater than 0* *Stagger = 0 indicates that the reporting period is a month* *RU (reporting unit) level;*                                                                                              
                                                                                                                                                                                                                                                                                                                                                    
                                                                                                                                                                                                                          *VAT unit level; Enterprise level*                                                                                        
*Reference number*                          *11-digit integer*                                                            *N/A*                                                                                           *RU level*                                                                                                                
*Cell number (cell_no / cell)*              *4-digit integer*                                                             *N/A*                              *Each cell number represents a stratum.*                     *RU level*                                                                                                                 *Based on SIC, sizeband etc.*
*period*                                    *6-digit or 4-digit integer*      *YYYYMM / YYYYQ/ YYYY*                      *MM ~ [01,12]*                                                                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                                                    
                                                                                                                          *Q ~ [1,4]*                                                                                                                                                                                                               
*Return variable to Winsorise*                                                .. math:: y_{i}                                                                                                                             *RU level*                                                                                                                
*Design weight (a-weight)*                  *Numeric*                         .. math:: a_{i}                             *[1,*\ :math:`\infty`\ *)*                                                                      *RU level*                                                                                                                
*Calibration factor (g-weight)*             *Numeric*                         .. math:: g_{i}                             *[0.3,3]*                                                                                       *RU level*                                                                                                                 *Not required for Winsorisation using Expansion Estimation. Required for Winsorisation using Ratio Estimation.*
                                                                                                                                                                                                                                                                                                                                                    
                                                                                                                                                                                                                                                                                                                                                     *Anything outside of [0.3,3] should be treated as suspicious.*
                                                                                                                                                                                                                                                                                                                                                    
                                                                                                                                                                                                                                                                                                                                                     *May be larger than 3 when cut-off sampling is used and methodology has assessed the quality balance & has deemed these weights acceptable for purpose.*
*Auxiliary variable*                        *Numeric*                         .. math:: x_{i}                                                                                                                             *RU level*                                                                                                                 *Additional data used, where Methodology deem (combined) ratio estimation appropriate. Not necessary for expansion estimation.
                                                                                                                                                                                                                                                                                                                                                     E.g. selection turnover / selection employment*
*L-value*                                   *Numeric*                         .. math:: L                                 *>0*                                                                                            *Usually top level for a given variable. Can be at other levels – lowest stratum level.*                                   *a numeric parameter supplied by methodology. There is a unique L-value for each return variable to Winsorise.*
=========================================== ================================= =========================================== ================================== ============================================================ ======================================================================================== ================================= ========================================================================================================================================================

.. _section-6:

Method Output
~~~~~~~~~~~~~

.. tabularcolumns:: |p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.30\linewidth-2\tabcolsep}|

.. rst-class:: longtable



=========================================== ==================================== =========================================== ================================== ============================================================ ================================== ================================= ==========================================================================================================================================================
Variable definition                         Type of variable                     Format of specific variable (if applicable) Expected range of the values       Meaning of the values                                        Expected level of aggregation      Frequency                         Comments
=========================================== ==================================== =========================================== ================================== ============================================================ ================================== ================================= ==========================================================================================================================================================
*e.g. 10-digit enterprise reference number* *e.g. character; integer; double*    *e.g. date*                                 *e.g.*                             *e.g.*                                                       *e.g.*                             *e.g. quarterly, monthly, annual*
                                                                                                                                                                                                                                                                                                 
                                                                                 *YYYY-MM-DD*                                *weights should be greater than 0* *Stagger = 0 indicates that the reporting period is a month* *RU level;*                                                         
                                                                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                             *VAT unit level; Enterprise level*                                  
*Reference number*                          *11-digit integer*                                                               *N/A*                                                                                           *RU level*                                                          
*period*                                    *6-digit or 4-digit integer*         *YYYYMM / YYYYQ / YYYY*                     *MM ~ [01,12]*                                                                                                                                                      
                                                                                                                                                                                                                                                                                                 
                                                                                                                             *Q ~ [1,4]*                                                                                                                                                         
outlier weight (o-weight)                   *Numeric*                            .. math:: o_{i}                                                                                                                             RU level                                                            
Winsorisation_flag                          System dependent. suggested: Integer                                                                                W = Winsorised                                               RU level                                                             *To indicate if the method has Winsorised the unit or not. If not, why. NW_AG indicates problem in sample design. NW_AG only used for ratio Winsorisation*
                                                                                                                                                                                                                                                                                                 
                                                                                                                                                                NW_FE = Not Winsorised Fully Enumerated (a=1)                                                                                    
                                                                                                                                                                                                                                                                                                 
                                                                                                                                                                NW_AG = Not Winsorised ag <1                                                                                                     
=========================================== ==================================== =========================================== ================================== ============================================================ ================================== ================================= ==========================================================================================================================================================

Statistical Process Flow/Formal Definition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Notation**

In the following equations:

-  :math:`i` is an index denoting an single observation / business / row

-  :math:`h` is an index denoting a stratum

-  :math:`j` is an index denoting a calibration group (this can be a
   collection of strata, or a single stratum, depending on use of
   combined or separate ratio estimation respectively)

-  :math:`y_{i}` is an observed value for a given variable for a given
   business

-  :math:`n_{h}` or :math:`n_{j}` is the sample size (or the number of
   businesses with a value, i.e. returned, imputed, constructed etc. -
   choice appropriate to survey) for stratum :math:`h` or calibration
   group :math:`j`

-  :math:`N_{h}` or :math:`N_{j}` is the population size for stratum
   :math:`h` or calibration group :math:`j`

-  :math:`w_{i}\ `\ is the weight which takes a value of either:

   -  :math:`a_{i}` for business :math:`i` in expansion estimation

   -  :math:`a_{i}g_{i}` for business :math:`i` in Ratio Estimation

-  :math:`L` is the Winsorisation parameter to be supplied – this is
   calculated from historic data using a bespoke method meeting survey
   requirements.

-  :math:`a_{i}` is the design-weight,
   :math:`a_{i} = \ a_{h} = \ \frac{N_{h}}{n_{h}}\ `, or the
   birth-death-adjusted design-weight,
   :math:`a_{i} = a_{h} = \ \frac{N_{h}}{n_{h}}\left( 1 + H_{h}\left( \frac{d_{h}}{n_{h} - d_{h}} \right) \right)`,
   for business :math:`i \in` stratum :math:`h`, as chosen during
   estimation. Out of scope adjustments must be used if applicable.

-  :math:`{\overline{y}}_{h} = \frac{\sum_{i = 1}^{n_{h}}y_{i}}{n_{h}}`
   is the sample mean for stratum :math:`h` for period to be Winsorised

-  :math:`g_{i} = g_{j} = \ \frac{\sum_{i = 1}^{N_{j}}x_{i}}{\sum_{i = 1}^{n_{j}}{a_{i}x_{i}}}`
   is the calibration-weight for business :math:`i \in` calibration
   group :math:`j`

where :math:`a_{i} = \ \frac{N_{h}}{n_{h}}` (regardless of whether the
birth-death-adjusted design-weight has been chosen during estimation)

-  :math:`x_{i}` is the auxiliary variable for a given sampled variable

-  :math:`{\widehat{\mu}}_{i} = x_{i}\frac{\sum_{i = 1}^{n_{j}}{a_{i}y_{i}}}{\sum_{i = 1}^{n_{j}}{a_{i}x_{i}}}`
   where :math:`{\widehat{\mu}}_{i}` is calculated using the data to be
   Winsorised for each observation and
   :math:`\frac{\sum_{i = 1}^{n_{j}}{a_{i}y_{i}}}{\sum_{i = 1}^{n_{j}}{a_{i}x}_{i}}`
   is calculated for each calibration group :math:`j`. Note:
   :math:`a_{i}` is as chosen during Estimation. businesses within the
   same stratum will have the same design-weight, but strata within a
   calibration group will have differing design-weights.

**Winsorisation**

**Before Winsorisation, the method should check the following things:**

-  **If** :math:`a_{i} = 1`, the unit should not be Winsorised but given
   an outlier weight = 1 and a Winsorisation flag NW_FE. This should
   occur regardless of whether other units in the same stratum have
   :math:`a_{i} \neq 1` (and regardless of any internal assignment of
   value to the :math:`g_{i}` or :math:`x_{i}` in the expansion method)

-  If using ratio Winsorisation for the target variable, if
   :math:`a_{i}g_{i} \leq 1`, the unit should not be Winsorised but
   given an outlier weight = 1 and a Winsorisation flag NW_AG

-  If :math:`a_{i} = 1` AND :math:`g_{i} \neq 1` AND
   :math:`a_{i}g_{i} < 1` then NW_FE should take priority for expansion
   Winsorisation, but NE_AG takes priority for ratio Winsorisation
   (though this should never happen in practice because if
   :math:`a_{i} = 1`, :math:`g_{i} = 1` by sample design theory)

-  If NW_FE or NW_AG flags are applied to a given unit, the unit should
   be removed from Winsorisation calculations

-  If these checks are not true, the unit should be Winsorised, its
   outlier weight calculated by the method, and a Winsorisation flag W

-  The input dataset has no missing values in the input columns

The outlier weight of a unit due to Winsorisation is

:math:`o_{i} = \left\{ \begin{matrix}
1 & \text{if }y_{i} \text{is not an outlier} \\
\frac{y_{i}^{*}}{y_{i}} = \frac{\text{modified y}}{\text{original y}} & \text{if }y_{i} \text{is an outlier} \\
\end{matrix} \right.\ `

Where

:math:`{y_{i}}^{*} = \left\{ \begin{matrix}
\text{y}_{\text{i }}\text{if }y_{i} \text{is not an outlier,}   i.e.  y_{i} \leq k_{i}\text{} \\
\frac{1}{w_{i}}y_{i} + \left( 1 - \frac{1}{w_{i}} \right)k_{i}\text{}\text{if }y_{i} \text{is an outlier,}   i.e. y_{i} > k_{i}  \\
\  \\
\end{matrix} \right.\ `

and where :math:`k_{i}` is defined in the following sections.

**Expansion Estimation**

The threshold for a unit, :math:`i`, in a given stratum, :math:`h`, is:

.. math:: k_{i} = k_{h} = \overline{y_{h}} + \frac{L}{a_{i} - 1}

Apart from parameter :math:`L`, which is calculated with a bespoke
method using historic data, this is calculated using the data from the
period to be Winsorised. Because :math:`a_{i} = \ a_{h}`, all businesses
in a given stratum will have the same threshold :math:`k_{h}`

**Ratio Estimation**

The unique threshold for a unit, :math:`i`, is:

.. math:: k_{i} = \ {\widehat{\mu}}_{i} + \frac{L}{a_{i}g_{i} - 1}

Apart from parameter :math:`L`, which is calculated with a bespoke
method using historic data, this is calculated using the data from the
period to be Winsorised.

|image0|

.. image:: Wins_process_flow.png

Please download the :download:`Process flow < Wins_process_flow.png>\`
for more details.

Worked Example
~~~~~~~~~~~~~~

**Input**

============ ==================== ==== ====== ====== ======== ========
RU reference SIC / classification Cell Period Return a-weight g-weight
============ ==================== ==== ====== ====== ======== ========
12345678901  47900                1001 201601 50     100      1
23456789012  47900                1001 201601 60     100      1
34567890123  47900                1001 201601 55     100      1
45678901234  47900                1001 201601 40     100      1
56789012345  47900                1001 201601 45     100      1
67890123456  47900                1002 201601 120    50       1
78901234567  47900                1002 201601 110    50       1
89012345678  47900                1002 201601 115    50       1
90123456789  47900                1002 201601 125    50       1
01234567890  47900                1002 201601 700    50       1
============ ==================== ==== ====== ====== ======== ========

L value = 3000

**Process**

| For cell 1001:
  Mean = 50
| Threshold:
  :math:`k = 50 + \frac{3000}{50 - 1} = 111.22\ (2\ dp - rounded\ for\ example\ purposes\ only)`
| No returns for cell 1001 > 111.22
| :math:`{y_{i}}^{*} = y_{i}` for all RU
| :math:`o_{i} = \frac{{y_{i}}^{*}}{y_{i}} = 1` for all RU

| For cell 1002:
| Mean = 234
| Threshold:
  :math:`k = 234 + \frac{3000}{100 - 1} = 264.30\ (2\ dp\  - rounded\ for\ example\ purposes\ only)`
| One return with RU ref 01234567890 has return > 264.30
| :math:`{y_{i}}^{*} = y_{i}` where return :math:`\leq` 264.30
| :math:`o_{i} = \frac{{y_{i}}^{*}}{y_{i}} = 1` where return
  :math:`\leq` 264.30
| For RU ref 01234567890,
  :math:`{y_{i}}^{*} = \frac{1}{100 \times 1} \times 700 + \left( 1 - \frac{1}{100 \times 1} \right) \times 264.30 = 268.657\ (2dp - rounded\ for\ example\ purposes\ only)`
| For RU ref 01234567890,
  :math:`o_{i} = \frac{268.657}{700} = 0.384\ (3dp - rounded\ for\ example\ purposes\ only)`
| **Output**

============ ==================== ==== ====== ====== ======== ======== ======== ====
RU reference SIC / classification Cell Period Return a-weight g-weight o-weight Flag
============ ==================== ==== ====== ====== ======== ======== ======== ====
12345678901  47900                1001 201601 50     100      1        1        0
23456789012  47900                1001 201601 60     100      1        1        0
34567890123  47900                1001 201601 55     100      1        1        0
45678901234  47900                1001 201601 40     100      1        1        0
56789012345  47900                1001 201601 45     100      1        1        0
67890123456  47900                1002 201601 120    50       1        1        0
78901234567  47900                1002 201601 110    50       1        1        0
89012345678  47900                1002 201601 115    50       1        1        0
90123456789  47900                1002 201601 125    50       1        1        0
01234567890  47900                1002 201601 700    50       1        0.384    1
============ ==================== ==== ====== ====== ======== ======== ======== ====

.. _section-7:

.. _section-8:

Scenarios to be Tested
~~~~~~~~~~~~~~~~~~~~~~

Expansion estimation Winsorisation

Ratio estimation Winsorisation

Outlier weight already present

Missing data in input data – method should fail

Not specifying all parameters – method should fail

Valid data inputs

Negative L-value – method should fail

Different L-values within a stratum for a given variable – method should
fail

Cells with design weight = 1 not Winsorised; should be given outlier
weight = 1 and flag NW_FE

Cells with design weight x calibration factor :math:`\leq` 1 should not
be Winsorised; should be given outlier weight = 1 and flag NW_AG

.. _section-9:

Treatment of Special Cases
~~~~~~~~~~~~~~~~~~~~~~~~~~

Negative values – some or all
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Winsorisation should only be used on positive values. In the event of
negative values, the business area should contact Methodology for
further, bespoke advice.

Derived variables – should Winsorise components rather than derived
variable, business area to contact Methodology if this does not seem
appropriate - `Useful
paper <https://www.researchgate.net/publication/307632859_Winsorization_for_Identifying_and_Treating_Outliers_in_Business_Surveys>`__

.. _section-10:

References
~~~~~~~~~~

https://www.researchgate.net/profile/Ray-Chambers/publication/307632859_Winsorization_for_Identifying_and_Treating_Outliers_in_Business_Surveys/links/5a100fc2458515cc5aa6afba/Winsorization-for-Identifying-and-Treating-Outliers-in-Business-Surveys.pdf
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. [1]
   https://pdfs.semanticscholar.org/9c07/1353fecb6efd1e04b8a2e2d7d0f354c8d8a0.pdf

.. |image0| image:: SML_Winsorization_Specification_2605.docx_images/media/image1.png
   :width: 9.49375in
   :height: 3.81806in




