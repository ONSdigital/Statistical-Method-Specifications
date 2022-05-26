M Probability Parameter
=======================

Overview
--------

======================= ==========================================================================
Support Area            *Data Linkage Expert Group, datalinkage@ons.gov.uk*
======================= ==========================================================================
Methods reference       **Filled in by DST**
*Description*           *A method of calculating M probabilities for use in probabilistic linkage*
*Method theme*          *Data Linkage*
*Method classification* *Probabilistic Data Linkage*
*Status*                *Specified, awaiting build*
======================= ==========================================================================

Method Specification
--------------------

Method Specification Amendments/Change Log
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

==================== =============== ========================== ================== ================================================
**Document version** **Description** **Author(s)**              **Date**           **Comments\***
==================== =============== ========================== ================== ================================================
*1.0*                *First draft*   *Josie Plachta*            *15\ th June 2021* *Changed from a template into a first draft*
                                                                                  
                                     *Josie.plachta@ons.gov.uk*                   
*1.1*                *Formatting*    *Jonathan Digby-North*     *17.06.21*         *Formatting, removing blank sections for upload*
\                                                                                 
==================== =============== ========================== ================== ================================================

**\*** *Comments can include whether two versions of the method are
being used.*

Summary
~~~~~~~

Brief statement of the methods aim:

A method of calculating M probabilities for use in probabilistic
linkage. The theory is covered in the Analysis Functions Data Linkage
training courses which is recommended for anyone undertaking linkage.

The M parameter manual calculation method uses a high-quality training
dataset of matched records to estimate M probabilities. These are one of
the parameters used in the popular Fellegi-Sunter probabilistic linkage
algorithm for pair-wise linkage. M represents the proportion of true
match pairs where a given variables agrees exactly and can be thought of
as a measure of the quality of the datasets being linked. They are used
in combination with U values to produce an agreement weight for each
matching variable between a pair of records. A total match score is then
calculated for each pair by summing the agreement weights of each
matching variable. Where pre-requisites are met, this is Methodology’s
recommended method for calculating M values.

This method should be used when an individual has access to a
high-quality set of true match pairs (known as a gold standard) that is
representative of all the true matches in the datasets to be linked,
i.e. not just a list of ‘easy matches’. This training data must be from
the datasets to be linked – proxy datasets or synthetic data are not
suitable.

Manual M calculations are highly useful when using scoring, as they are
informed by the data itself. They are also computationally very easy to
run and simple to understand.

However, there are limitations to the method. If an unsuitable training
dataset is used, any characteristics of that training dataset will
translate into the M parameter estimates. For example, if a training
dataset does not contain any links from a specific demographic group,
that bias will directly affect the probabilistic algorithm that utilizes
the M value, causing bias in the outputted linkage table. Equally, if a
training data doesn’t contain any of the ‘hardest’ matches (e.g. the
ones with lots of error that most automatic methods would struggle
with), the method itself will also struggle to link these pairs.

Where a high-quality training dataset is not available, the Expectation
Maximisation algorithm can be used.

Manual M parameter calculations can used as part of Active Learning
methods (Supervised Machine Learning Algorithms). An initial M estimate
is calculated and run, and then clerical review is used to confirm
decisions made, resolve the hard links that can’t be automatically
accepted, and break any incorrect decisions. These clerically matched
pairs can then form a new training dataset or add to the old one. For
the next iteration, the M value calculations are run on the new training
dataset, with the results being reviewed once again by clerical and
feeding back into the training dataset.

Requirements and Dependencies
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  This method is appropriate for pair-wise linkages only (I.e. a
   dataset being linked to itself or two datasets being linked together)

-  Users of this method are strongly advised to have undertaken The
   Analytical Functions Data Linkage Working and Practitioner level
   courses that are run by the ONS Linkage Expert Group (ONS Methodology
   Linkage and the ONS Linkage Hub).

-  To use this method, users must have a high-quality training dataset
   or a gold standard. This training dataset will need to include true
   match pairs from the two datasets to be linked, complete with all the
   matching variables that will be used. Methodology stress that the
   training data must be representative of all the true matches, e.g.
   cannot be biased towards easy links or specific demographic groups.

Assumptions and Validity
~~~~~~~~~~~~~~~~~~~~~~~~

-  The method assumes that the training data is suitable, as mentioned
   above.

-  If the training data is not representative, either the Expectation
   Maximisation approach or an Active Learning approach could be used.

.. _section-1:

Method Input
~~~~~~~~~~~~~

.. tabularcolumns:: |p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.30\linewidth-2\tabcolsep}|

.. rst-class:: longtable



To calculate M values manually, the user must submit training data to
the method. The training data should contain data from the two datasets
to be linked, cleaned, standardised and formatted to the same
specification. The variable definitions are given in table 1.1 below. An
example dataset is given in table 1.2, which could be used to calculate
M values for Forename, Surname, Date of Birth, and Postcode. Note how
the dataset contains both easy matches (with high levels of agreement)
and hard matches which would have been made by clerical staff.

The user will also need to specify which variables are the matching
variables. E.g. (format pending)

Matching_vars = [‘forename’, ‘surname’, ‘DoB’, ‘postcode’]

Table 1.1.
^^^^^^^^^^

================================== =================== ============================================================================================================================================================================================================================================================================================================ ========================== ========
Variable definition                Type of variable    Expected range of the values                                                                                                                                                                                                                                                                                 Meaning of the values      Comments
================================== =================== ============================================================================================================================================================================================================================================================================================================ ========================== ========
*Dataframe 1 record ID*            *String or integer*                                                                                                                                                                                                                                                                                                              *Unique ID to that record*
*Dataframe 2 record ID*            *String or integer*                                                                                                                                                                                                                                                                                                              *Unique ID to that record*
*Dataframe 1 Matching variable #1* *Any*               *Matching variables are personal identifiable information such as Forename, Business name, Address, Date of Birth. They will depend on what you are linking and what is available in your dataset. See The Analytical functions linkage training courses for theory on what makes a good matching variable.*                           
                                                                                                                                                                                                                                                                                                                                                                                              
                                                       *You must have two columns for each matching variable, one from dataframe 1, one from dataframe 2.*                                                                                                                                                                                                                                    
                                                                                                                                                                                                                                                                                                                                                                                              
                                                       *Variables must be standardized to the same specification in both dataframe 1 and dataframe 2.*                                                                                                                                                                                                                                        
*Dataframe 2 Matching variable #1* *Any*                                                                                                                                                                                                                                                                                                                                                      
*Dataframe 1 Matching variable #2* *Any*                                                                                                                                                                                                                                                                                                                                                      
*……*                               *……*                                                                                                                                                                                                                                                                                                                                                       
================================== =================== ============================================================================================================================================================================================================================================================================================================ ========================== ========

Table 1.2
^^^^^^^^^

======= ======== =================================== =================================== ================================== ================================== ======================================== ======================================== =================================== ===================================
DF 1 ID DF 2 ID  DF 1 Matching variable #1: Forename DF 1 Matching variable #1: Forename DF 1 Matching variable #2: Surname DF 2 Matching variable #2: Surname DF 1 Matching variable #3: Date of birth DF 2 Matching variable #3: Date of Birth DF 1 Matching variable #4: Postcode DF 2 Matching variable #4: Postcode
======= ======== =================================== =================================== ================================== ================================== ======================================== ======================================== =================================== ===================================
61758   A9019063 Harry                               Harry                               Potter                             Potter                             31/07/1980                               31/07/1980                               SU4 6TH                             SU4 6TH
90267   A71751   Ronald                              Ron                                 Weasley                            Weasley                            01/03/1980                               01/03/1980                               OS5 2ED                             OS5 2ED
37732   A48420   Hermione                            Hermione                            Granger                            Granger                            19/09/1979                               19/09/1979                               N2 6gh                              N2 6gh
68887   A44119   Lord                                Thomas                              Voldemort                          Riddle                             31/12/1926                               31/12/1926                               ZZ99 3WS                            HG5 3RT
======= ======== =================================== =================================== ================================== ================================== ======================================== ======================================== =================================== ===================================

Method Output
~~~~~~~~~~~~~

.. tabularcolumns:: |p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.30\linewidth-2\tabcolsep}|

.. rst-class:: longtable



The output of this method is a single M value parameter for each
variable inputted as a matching variable. Table 2.1 defines the
variables, and Table 2.2 shows an example output table.

*Table 2.1*

=================== ================ ============================ ============================================================================================= ========
Variable definition Type of variable Expected range of the values Meaning of the values                                                                         Comments
=================== ================ ============================ ============================================================================================= ========
Variable #1 M value Integer          0 - 1                        The proportion of true matches where this variable agrees between Dataframe 1 and Dataframe 2
Variable #2 M value Integer          0 - 1                        The proportion of true matches where this variable agrees between Dataframe 1 and Dataframe 2
….                                                                                                                                                             
=================== ================ ============================ ============================================================================================= ========

**For example, when specifying the following matching variables, we
would get an output of:**

*Table 2.2*

Matching_vars = [‘forename’, ‘surname’, ‘DoB’, ‘postcode’]

======== ====================
Variable Proportion (M value)
======== ====================
Forename 0.405405
Surname  0.675676
DoB      0.648649
Postcode 0.675676
======== ====================

Statistical Process Flow/Formal Definition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. *Import training data containing true match pairs and their matching
   variables*

2. *Define matching variables*

3. *For each matching variable, calculate proportion of match pairs in
   the training data where there is exact agreement between the
   dataframe 1 and 2 record*

4. *Export the proportions as M values ready for use in probabilistic
   scoring*

.. _section-2:

Worked Example
~~~~~~~~~~~~~~

This example contains a training dataset of match pairs for the matching
variables Forename, Surname, Date of Birth, and Postcode. The sections
follow the numbering of the stages in the Statistical Process/Flow
section of this specification. The very last section gives an example of
a Fuzzy agreement M value, as mentioned in the special cases section.

1. *Import training data containing true match pairs and their matching
   variables*

A representative sample of matches between dataframe 1 and dataframe 2:

============== ============== ==================== ==================== =================== =================== ========================= =========================== ==================== ====================
dataframe_1_ID dataframe_2_ID dataframe_1_forename dataframe_2_forename dataframe_1_surname dataframe_2_surname dataframe_1_Date_of_birth dataframe_2\_ Date_of_birth dataframe_1_Postcode dataframe_2_Postcode
============== ============== ==================== ==================== =================== =================== ========================= =========================== ==================== ====================
61758          A9019063       Harry                Harry                Potter              Potter              31/07/1980                31/07/1980                  SU4 6TH              SU4 6TH
90267          A71751         Ronald               Ron                  Weasley             Weasley             01/03/1980                01/03/1980                  OS5 2ED              OS5 2ED
37732          A48420         Hermione             Hermione             Granger             Granger             19/09/1979                19/09/1979                  N2 6gh               N2 6gh
68887          A44119         Lord                 Thomas               Voldemort           Riddle              31/12/1926                31/12/1926                  ZZ99 3WS             HG5 3RT
49559          A16467         Severus              Steven               Snape               Smith               09/01/1960                09/01/1960                  HG5 3RT              HG5 3RT
55629          A37927         Albus                Professor            Dumbledore          Dumbledore          01/01/1881                01/01/1881                  HG5 3RT              HG5 3RT
30499          A84774         Reuben               Rubeus               Hagrid              Hagrid              06/12/1928                06/12/1920                                       HG5 3RT
22847          A36313         Susan                Susie                Bones               Bones               01/09/1980                09/01/1960                  NO8 10OP             NO8 10OP
19536          A95075         Amelia               Amelia               Bones               Bones               12/08/1976                12/08/1976                  NO8 2ET              NO8 2ET
24603          A17203         Professor Gilderoy   I can't remember     Lockheart           Don't know          26/01/1964                01/01/1960                  HA5 4EW              ZZ99 3WS
98120          A17407         Firenze              Firenze                                                                                                                                
70165          A79723         Dedalus              Dedalus              Diggle              Diggle              01/01/1964                23/09/1964                  K34 7YH              K34 7YH
62114          A38417         Arabella             Annabella            Figg                Figg                03/04/1945                03/04/1945                  SU4 6TH              SU4 6TH
70110          A92675         Miranda              Miranda              Goshawk                                 01/01/1921                07/06/1921                                      
60168          A97267         Draco                Lucius Draco         Malfoy              Malfoy              05/06/1980                05/06/1980                  WT4 5CV              WT4 5CV
31954          A92970         Malfoy               Lucius               Lucius              Malfoy                                        03/06/1954                  WT4 5CV              WT4 5CV
48007          A72749         Narssica             Jessica              Black               Malfoy              02/02/1955                02/02/1955                  C2 6TY               WT4 5CV
15256          A91109         Sirius               Sirius               Black               White                                         03/11/1959                  ZZ99 3Vz             C2 6TY
77125          A98275         Ms                   Bellatrix            LeStrange           LeStrange           01/01/1951                                            ZZ99 3Vz            
52925          A63685         Minerva              Minerva              McGonnagal          Urquart             04/10/1935                04/10/1907                  HG5 3RT              HG5 3RT
19812          A20470         Filius               Phillip              Flitwick            Flitwick            17/10/1958                17/10/1958                  HG5 3RT              HG5 3RT
99727          A10552         Pomona               Fomona               Sprout              Sprout              15/04/1900                15/04/1900                  HG5 3RT              HG5 3RT
65747          A70296         Argus                Argus                Filch               Filch               01/01/1950                01/01/1900                  HG5 3RT              HG5 3RT
78884          A92266         Sir Nicholas         Nearly               de Mimsy-Porpington Nick                31/10/1492                31/10/1492                  HG5 3RT              HG5 3RT
83700          A62573         Sybill               Sybill               Trelawny            Trelawny            09/03/1960                09/03/1960                  HG5 3RT              HG5 3RT
63974          A95533         Cuthbert             Cuthbert             Binns               Bones                                                                     HG5 3RT              HG5 3RT
97740          A74502         Horace               Horace               Slughorn            Ziugho              28/04/1901                28/04/1901                  HG5 3RT              HG5 3RT
38679          A25344         Rolanda              Madam Rolanda        Hooch               Hooch               01/01/1918                01/01/1918                  HG5 3RT              HG5 3RT
98639          A19310         Aberforth            A                    Dumbledore          Dumbledore          04/07/1881                04/07/1881                  HG1 5FG              HG1 5FG
11611          A37768         Madam                                     Puddifoot           Puddifoot                                     HG1 5XQ                     HG1 5XQ             
27670          A26780         Nymphadora           Tonks                Tonks               Tonks               02/04/1973                02/04/1973                  MN2 8YU              WT4 RFG
34652          A21378         Remus                Remus                Lupin               Lupin               10/03/1960                10/03/1960                  HG5 3RT              WT4 RFG
50610          A87124         Molly                Molly                Weasley             Weasley             30/10/1940                30/10/1940                  OS5 2ED              OS5 2ED
34093          A50560         Ginny                Ginevra              Weasley             Weasley             11/06/1981                11/06/1981                  OS5 2ED              OS5 2ED
57862          A60459         Fred                 Gred                 Weasley             Weasley             01/04/1978                01/04/1978                  DA2 7YH              OS5 2ED
25317          A74043         George               Freoge               Weasley             Weasley             01/04/1978                01/04/1978                  DA2 7YH              OS5 2ED
25916          A24048         Arthur               Arthur               Weasley             Weasley             06/02/1950                06/02/1950                  OS5 2ED              OS5 2ED
============== ============== ==================== ==================== =================== =================== ========================= =========================== ==================== ====================

..

   *2. Define matching variables*

Forename, Surname, Date of Birth, Postcode

   *3. For each matching variable, calculate proportion of match pairs
   in the training data where there is exact agreement between the
   dataframe 1 and 2 record*

Number of agreeing matching pairs (:math:`P_{i}`) – For each variable,
count the number of match pairs where the variable agrees exactly.

Number of disagreeing matching pairs (:math:`P_{j}`)– For each variable,
count the number of match pairs where the variable does not agree
exactly.

Proportion (M) = :math:`\frac{P_{i}}{(P_{i + \ P_{j})}}` = The
proportion of the total match pairs where the variable agrees exactly.

============= ================================================= ================================================= ==============
Variable      Number of agreeing matching pairs (:math:`P_{i}`) Number of disagreeing match pairs (:math:`P_{j}`) Proportion (M)
============= ================================================= ================================================= ==============
Forename      15                                                22                                                0.405405
Surname       25                                                12                                                0.675676
Date of birth 24                                                13                                                0.648649
Postcode      25                                                12                                                0.675676
============= ================================================= ================================================= ==============

..

   *4. Export the proportions as M values ready for use in probabilistic
   scoring*

======== ========
Variable M
======== ========
Forename 0.405405
Surname  0.675676
DoB      0.648649
Postcode 0.675676
======== ========

*Fuzzy agreement case:*

============== ================================================= ================================================= ==============
Variable       Number of agreeing matching pairs (:math:`P_{i}`) Number of disagreeing match pairs (:math:`P_{j}`) Proportion (M)
============== ================================================= ================================================= ==============
Date of birth  24                                                13                                                0.648649
Day of birth   27                                                10                                                0.72973
Month of birth 28                                                9                                                 0.756757
Year of birth  26                                                11                                                0.702703
============== ================================================= ================================================= ==============

.. _section-3:

Scenarios to be Tested
~~~~~~~~~~~~~~~~~~~~~~

Standard method testing.

.. _section-4:

Treatment of Special Cases
~~~~~~~~~~~~~~~~~~~~~~~~~~

There are two special cases to consider, the first, missing values, is
generally present in all datasets, the second, fuzziness, is up to user
choice.

Treatment of missing values:

Missing values are present in nearly all datasets. In our calculations,
missingness does not count towards agreement. Thus, if we have a match
pair that both have a missing bate of birth, this is not recorded as
agreement and so does not contribute towards the count of agreeing pairs
in our M value calculation.

Fuzzy agreement refers to cases where a match pair does not agree
exactly, however there is some similarity between the values. Examples
could be a match pair where postcode area matches but the full postcode
does not, or a shortened version of a name to a fullname such as Anna
and Annabell. If users wish, they can calculate M values (and U values)
of these derived variables so that fuzzy agreement does not cause a
record pair to get a disagreement score but also not get the full
agreement score either (e.g. a score in between disagreement/agreement
is computed).

.. _section-5:

Example (Synthetic) Data 
~~~~~~~~~~~~~~~~~~~~~~~~~

============== ============== ==================== ==================== =================== =================== ========================= =========================== ==================== ====================
dataframe_1_ID dataframe_2_ID dataframe_1_forename dataframe_2_forename dataframe_1_surname dataframe_2_surname dataframe_1_Date_of_birth dataframe_2\_ Date_of_birth dataframe_1_Postcode dataframe_2_Postcode
============== ============== ==================== ==================== =================== =================== ========================= =========================== ==================== ====================
61758          A9019063       Harry                Harry                Potter              Potter              31/07/1980                31/07/1980                  SU4 6TH              SU4 6TH
90267          A71751         Ronald               Ron                  Weasley             Weasley             01/03/1980                01/03/1980                  OS5 2ED              OS5 2ED
37732          A48420         Hermione             Hermione             Granger             Granger             19/09/1979                19/09/1979                  N2 6gh               N2 6gh
68887          A44119         Lord                 Thomas               Voldemort           Riddle              31/12/1926                31/12/1926                  ZZ99 3WS             HG5 3RT
49559          A16467         Severus              Steven               Snape               Smith               09/01/1960                09/01/1960                  HG5 3RT              HG5 3RT
55629          A37927         Albus                Professor            Dumbledore          Dumbledore          01/01/1881                01/01/1881                  HG5 3RT              HG5 3RT
30499          A84774         Reuben               Rubeus               Hagrid              Hagrid              06/12/1928                06/12/1920                                       HG5 3RT
22847          A36313         Susan                Susie                Bones               Bones               01/09/1980                09/01/1960                  NO8 10OP             NO8 10OP
19536          A95075         Amelia               Amelia               Bones               Bones               12/08/1976                12/08/1976                  NO8 2ET              NO8 2ET
24603          A17203         Professor Gilderoy   I can't remember     Lockheart           Don't know          26/01/1964                01/01/1960                  HA5 4EW              ZZ99 3WS
98120          A17407         Firenze              Firenze                                                                                                                                
70165          A79723         Dedalus              Dedalus              Diggle              Diggle              01/01/1964                23/09/1964                  K34 7YH              K34 7YH
62114          A38417         Arabella             Annabella            Figg                Figg                03/04/1945                03/04/1945                  SU4 6TH              SU4 6TH
70110          A92675         Miranda              Miranda              Goshawk                                 01/01/1921                07/06/1921                                      
60168          A97267         Draco                Lucius               Malfoy              Malfoy              05/06/1980                05/06/1980                  WT4 5CV              WT4 5CV
31954          A92970         Malfoy               Lucius               Lucius              Malfoy                                        03/06/1954                  WT4 5CV              WT4 5CV
48007          A72749         Narssica             Jessica              Black               Malfoy              02/02/1955                02/02/1955                  C2 6TY               WT4 5CV
15256          A91109         Sirius               Sirius               Black               White                                         03/11/1959                  ZZ99 3Vz             C2 6TY
77125          A98275         Ms                   Bellatrix            LeStrange           LeStrange           01/01/1951                                            ZZ99 3Vz            
52925          A63685         Minerva              Minerva              McGonnagal          Urquart             04/10/1935                04/10/1907                  HG5 3RT              HG5 3RT
19812          A20470         Filius               Phillip              Flitwick            Flitwick            17/10/1958                17/10/1958                  HG5 3RT              HG5 3RT
99727          A10552         Pomona               Fomona               Sprout              Sprout              15/04/1900                15/04/1900                  HG5 3RT              HG5 3RT
65747          A70296         Argus                Argus                Filch               Filch               01/01/1950                01/01/1900                  HG5 3RT              HG5 3RT
78884          A92266         Sir Nicholas         Nearly               de Mimsy-Porpington Nick                31/10/1492                31/10/1492                  HG5 3RT              HG5 3RT
83700          A62573         Sybill               Sybill               Trelawny            Trelawny            09/03/1960                09/03/1960                  HG5 3RT              HG5 3RT
63974          A95533         Cuthbert             Cuthbert             Binns               Bones                                                                     HG5 3RT              HG5 3RT
97740          A74502         Horace               Horace               Slughorn            Ziugho              28/04/1901                28/04/1901                  HG5 3RT              HG5 3RT
38679          A25344         Rolanda              Madam Rolanda        Hooch               Hooch               01/01/1918                01/01/1918                  HG5 3RT              HG5 3RT
98639          A19310         Aberforth            A                    Dumbledore          Dumbledore          04/07/1881                04/07/1881                  HG1 5FG              HG1 5FG
11611          A37768         Madam                                     Puddifoot           Puddifoot                                     HG1 5XQ                     HG1 5XQ             
27670          A26780         Nymphadora           Tonks                Tonks               Tonks               02/04/1973                02/04/1973                  MN2 8YU              WT4 RFG
34652          A21378         Remus                Remus                Lupin               Lupin               10/03/1960                10/03/1960                  HG5 3RT              WT4 RFG
50610          A87124         Molly                Molly                Weasley             Weasley             30/10/1940                30/10/1940                  OS5 2ED              OS5 2ED
34093          A50560         Ginny                Ginevra              Weasley             Weasley             11/06/1981                11/06/1981                  OS5 2ED              OS5 2ED
57862          A60459         Fred                 Gred                 Weasley             Weasley             01/04/1978                01/04/1978                  DA2 7YH              OS5 2ED
25317          A74043         George               Freoge               Weasley             Weasley             01/04/1978                01/04/1978                  DA2 7YH              OS5 2ED
25916          A24048         Arthur               Arthur               Weasley             Weasley             06/02/1950                06/02/1950                  OS5 2ED              OS5 2ED
============== ============== ==================== ==================== =================== =================== ========================= =========================== ==================== ====================

.. _section-6:

Code
~~~~

.. code-block:: python

   # Pyspark/Python pseudo code script for calculating M values from training data that contains the true_match pairs between dataframe 1 and dataframe 2

   # Author: datalinkage@ons.gov.uk

   # v: 1.0

   # --- Set Up --- #

   # This stage will import packages, start spark, import data and input your desired matching variables

   import pandas as pd

   from pyspark.sql import SparkSession

   # Create a sparksession to read in parquet or other large files

   spark = SparkSession.builder.appName("default-session").getOrCreate()

   # Import your training data as a spark dataframe

   training_data = spark.read.parquet("folder/file_location_for_training_data")

   # View columns to ensure variables are named appropriately

   training_data.columns

   """

   ["dataframe_1_record_ID",

   "dataframe_2_record_ID",

   "dataframe_1_forename",

   "dataframe_2_forename",

   "dataframe_1_surname",

   "dataframe_2_surname",

   "dataframe_1_sex",

   "dataframe_2_sex",

   "dataframe_1_date_of_birth",

   "dataframe_2_date_of_birth",

   "dataframe_1_postcode",

   "dataframe_2_postcode"]

   """

   # Visually check the dataset to ensure it is as expected.

   # Ensure that missing values are not coded in a fashion that will lead to agreement where both records have a missing value.

   training_data.show()

   # define matching variables without dataframe preffixes

   matching_vars =
   ["forename","surname","sex","date_of_birth","postcode"]

   # --- M calculation look --- #

   # This stage will iterate through the list of matching variables and spit out an M value for each one

   # M value calculation will be: number of pairs where matching variables agreed exactly / total number of pairs

   # Define the total number of pairs

   total_pairs = training_data.count()

   # create empty list to append M values to

   m_list = []

   for variable in matching_vars:

	   matching_pairs = training_data.filter(col("dataframe_1_" + variable)
	   == col("dataframe_2_" + variable)).count()

   m_value = matching_pairs / total_pairs

   m_list.append(m_value)

   # Transform list of M_values into a dataframe using pandas

   M_values = pd.DataFrame()

   M_values["matching_variable"] = matching_vars

   M_values["M"] = m_list

   training_data.write.csv("folder/file_location_for_training_data",header=True)

.. _section-7:

 

References
~~~~~~~~~~

`Link to data linkage training
course <https://learninghub.ons.gov.uk/enrol/index.php?id=1301>`__

`Link to Fellegi-Sunter Paper on probabilistic
linkage <https://www.tandfonline.com/doi/abs/10.1080/01621459.1969.10501049>`__

`Link to discussion on probabilistic linkage by Doidge and
Harron <https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6281162/>`__

.. _section-8:


