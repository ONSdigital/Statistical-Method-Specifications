U parameter for probabilistic data linkage
==========================================

Overview
--------

======================= ==========================================================================
Support Area            *Data Linkage Expert Group, datalinkage@ons.gov.uk*
======================= ==========================================================================
Version                 *Latest version*
Methods reference       **Filled in by DST**
*Description*           *A method of calculating M probabilities for use in probabilistic linkage*
*Method theme*          *Data Linkage*
*Method classification* *Probabilistic Data Linkage*
Status                  *Specified, awaiting build*
======================= ==========================================================================

Method Specification
--------------------

Method Specification Amendments/Change Log
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

==================== =============== ========================== ================== ============================================
**Document version** **Description** **Author(s)**              **Date**           **Comments\***
==================== =============== ========================== ================== ============================================
*1.0*                *First draft*   *Josie Plachta*            *15\ th June 2021* *Changed from a template into a first draft*
                                                                                  
                                     *Josie.plachta@ons.gov.uk*                   
*1.1*                *Formatting*    *Jonathan Digby-North*     *17\ th June 2021* *Formatting for upload.*
\                                                                                 
==================== =============== ========================== ================== ============================================

**\*** *Comments can include whether two versions of the method are
being used.*

Summary
~~~~~~~

Brief statement of the methods aim:

A method of calculating U probabilities for use in probabilistic
linkage. The theory is covered in the Analysis Functions Data Linkage
training courses which is recommended for anyone undertaking linkage.

The U parameter manual calculation method uses a training dataset built
be randomly pairing together the records from the datasets the user
wishes to link. These are one of the parameters used in the popular
Fellegi-Sunter probabilistic linkage algorithm for pair-wise linkage. M
represents the proportion of random non-match pairs where a given
variables agrees exactly and can be thought of as a measure of the
distinguishability of the variables being used for linkage. They are
used in combination with M values to produce an agreement weight for
each matching variable between a pair of records. A total match score is
then calculated for each pair by summing the agreement weights of each
matching variable. This is Methodology’s recommended method for
calculating U values.

U calculations are highly useful when using scoring, as they are
informed by the data itself. They are also computationally very easy to
run and simple to understand.

Requirements and Dependencies
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  None

Assumptions and Validity
~~~~~~~~~~~~~~~~~~~~~~~~

-  The user must use the two datasets they plan to link to construct the
   training data used for U value calculations.

-  U values cannot be transferred between linkages, and should be
   recalculated whenever significant change occurs to one or both of the
   datasets to be linked.

.. _section-1:

Method Input
~~~~~~~~~~~~~

.. tabularcolumns:: |p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.30\linewidth-2\tabcolsep}|

.. rst-class:: longtable



To calculate U values manually, the user must submit their two data
frames they plan to link to the method. Both datasets should be cleaned,
standardised and formatted to the same specification. It should be
ensured that missing values are not treated as agreement, either by
dealing with nulls specifically or by inserting different null flags
into the datasets, e.g. missingness in DF1 coded as ‘null’ and in DF2 as
‘na’. The variable definitions are given in table 1.1 a and b below. Two
example datasets are given in table 1.2 a and b, whose records could be
randomly paired together and used to calculate U values for Forename,
Surname, Date of Birth, and Postcode. Note how missing values are coded
in each dataframe.

The user will also need to specify which variables are the matching
variables. E.g. (format pending):

Matching_vars = [‘forename’, ‘surname’, ‘DoB’, ‘postcode’]

Table 1.1a: Dataframe 1 specification
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================================== =================== ============================================================================================================================================================================================================================================================================================================ ========================== ========
Variable definition                Type of variable    Expected range of the values                                                                                                                                                                                                                                                                                 Meaning of the values      Comments
================================== =================== ============================================================================================================================================================================================================================================================================================================ ========================== ========
*Dataframe 1 record ID*            *String or integer*                                                                                                                                                                                                                                                                                                              *Unique ID to that record*
*Dataframe 1 Matching variable #1* *Any*               *Matching variables are personal identifiable information such as Forename, Business name, Address, Date of Birth. They will depend on what you are linking and what is available in your dataset. See The Analytical functions linkage training courses for theory on what makes a good matching variable.*                           
                                                                                                                                                                                                                                                                                                                                                                                              
                                                       *Variables must be standardized to the same specification in both dataframe 1 and dataframe 2.*                                                                                                                                                                                                                                        
*Dataframe 1 Matching variable #2* *Any*                                                                                                                                                                                                                                                                                                                                                      
*Dataframe 1 Matching variable #3* *Any*                                                                                                                                                                                                                                                                                                                                                      
*……*                               *……*                                                                                                                                                                                                                                                                                                                                                       
================================== =================== ============================================================================================================================================================================================================================================================================================================ ========================== ========

Table 1.1 b: Dataframe 2 specification
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

================================== =================== ============================================================================================================================================================================================================================================================================================================ ========================== ========
Variable definition                Type of variable    Expected range of the values                                                                                                                                                                                                                                                                                 Meaning of the values      Comments
================================== =================== ============================================================================================================================================================================================================================================================================================================ ========================== ========
*Dataframe 2 record ID*            *String or integer*                                                                                                                                                                                                                                                                                                              *Unique ID to that record*
*Dataframe 2 Matching variable #1* *Any*               *Matching variables are personal identifiable information such as Forename, Business name, Address, Date of Birth. They will depend on what you are linking and what is available in your dataset. See The Analytical functions linkage training courses for theory on what makes a good matching variable.*                           
                                                                                                                                                                                                                                                                                                                                                                                              
                                                       *Variables must be standardized to the same specification in both dataframe 1 and dataframe 2.*                                                                                                                                                                                                                                        
*Dataframe 2 Matching variable #2* *Any*                                                                                                                                                                                                                                                                                                                                                      
*Dataframe 2 Matching variable #3* *Any*                                                                                                                                                                                                                                                                                                                                                      
*……*                               *……*                                                                                                                                                                                                                                                                                                                                                       
================================== =================== ============================================================================================================================================================================================================================================================================================================ ========================== ========

.. _section-2:

Table 1.2a: Dataframe 1 example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

============== ==================== =================== ========================= ====================
Dataframe_1_ID Dataframe_1_Forename Dataframe_1_Surname Dataframe_1_Date_of_birth Dataframe_1_Postcode
============== ==================== =================== ========================= ====================
61758          Harry                Potter              31/07/1980                SU4 6TH
90267          Ronald               Weasley             01/03/1980                OS5 2ED
37732          Hermione             Granger             19/09/1979                N2 6gh
68887          Lord                 Voldemort           31/12/1926                ZZ99 3WS
49559          Severus              Snape               09/01/1960                HG5 3RT
============== ==================== =================== ========================= ====================

Table 1.2 b: Dataframe 2 example

============== ==================== =================== ========================= ====================
Dataframe_2_ID Dataframe_2_Forename Dataframe_2_Surname Dataframe_2_Date of birth Dataframe_2_Postcode
============== ==================== =================== ========================= ====================
A87124         Molly                Weasley             30/10/1940                OS5 2ED
A9019063       Harry                Potter              31/07/1980                SU4 6TH
A91109         Sirius               White               03/11/1959                C2 6TY
A92266         Nearly               Nick                31/10/1492                HG5 3RT
A92341         Dung                 Fletcher            na                        na
A92362         Alistaire            Moody               na                        HG5 3RT
============== ==================== =================== ========================= ====================

Method Output
~~~~~~~~~~~~~

.. tabularcolumns:: |p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.10\linewidth-2\tabcolsep}|p{\dimexpr 0.30\linewidth-2\tabcolsep}|

.. rst-class:: longtable



The output of this method is a single U value parameter for each
variable inputted as a matching variable. Table 2.1 defines the
variables, and Table 2.2 shows an example output table.

Table 2.1

=================== ================ ============================ =================================================================================================== ========
Variable definition Type of variable Expected range of the values Meaning of the values                                                                               Comments
=================== ================ ============================ =================================================================================================== ========
Variable #1 U value Integer          0 - 1                        The proportion of random non matches where this variable agrees between Dataframe 1 and Dataframe 2
Variable #2 U value Integer          0 - 1                        The proportion of random non matches where this variable agrees between Dataframe 1 and Dataframe 2
….                                                                                                                                                                   
=================== ================ ============================ =================================================================================================== ========

**For example, when specifying the following matching variables, we
would get an output of:**

*Table 2.2*

Matching_vars = [‘forename’, ‘surname’, ‘DoB’, ‘postcode’]

======== ====================
Variable Proportion (U value)
======== ====================
Forename 0.00000001
Surname  0.00000001
DoB      0.00000024
Postcode 0.00000046
======== ====================

Statistical Process Flow/Formal Definition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Import the two datasets to be linked including their matching
   variables

2. Randomly pair the two datasets together to create a list of random
   non-match record pairs

3. Define matching variables

4. For each matching variable, calculate proportion of random non match
   pairs in the training data where there is exact agreement between the
   dataframe 1 and 2 record

5. Note: Where a U value is estimated as 0 it should be swapped with
   0.0000000000000001 or lower otherwise the fellegi-sunter equation
   will fail due to division by 0

6. Export the proportions as U values ready for use in probabilistic
   scoring

.. _section-3:

Worked Example
~~~~~~~~~~~~~~

Step 1: Import the two dataframes to be linked, including their matching
variables:

Dataframe 1:

============== ==================== =================== ======================== ====================
Dataframe_1_ID Dataframe_1_Forename Dataframe_1_Surname Dataframe_1_Dateof birth Dataframe_1_Postcode
============== ==================== =================== ======================== ====================
61758          Harry                Potter              31/07/1980               SU4 6TH
90267          Ronald               Weasley             01/03/1980               OS5 2ED
37732          Hermione             Granger             19/09/1979               N2 6gh
68887          Lord                 Voldemort           31/12/1926               ZZ99 3WS
49559          Severus              Snape               09/01/1960               HG5 3RT
55629          Albus                Dumbledore          01/01/1881               HG5 3RT
30499          Reuben               Hagrid              06/12/1928              
22847          Susan                Bones               01/09/1980               NO8 10OP
19536          Amelia               Bones               12/08/1976               NO8 2ET
24603          Lockheart            26/01/1964          HA5 4EW                 
98120          Firenze                                                          
70165          Dedalus              Diggle              01/01/1964               K34 7YH
62114          Arabella             Figg                03/04/1945               SU4 6TH
70110          Miranda              Goshawk             01/01/1921              
60168          Draco                Malfoy              05/06/1980               WT4 5CV
31954          Malfoy               Lucius                                       WT4 5CV
48007          Narssica             Black               02/02/1955               C2 6TY
15256          Sirius               Black                                        ZZ99 3Vz
77125          Ms                   LeStrange           01/01/1951              
52925          Minerva              McGonnagal          04/10/1935               HG5 3RT
19812          Filius               Flitwick            17/10/1958               HG5 3RT
99727          Pomona               Sprout              15/04/1900               HG5 3RT
65747          Argus                Filch               01/01/1950               HG5 3RT
78884                               de Mimsy-Porpington 31/10/1492               HG5 3RT
83700          Sybill               Trelawny            09/03/1960               HG5 3RT
63974          Cuthbert             Binns                                        HG5 3RT
97740          Horace               Slughorn            28/04/1901               HG5 3RT
38679          Rolanda              Hooch               01/01/1918               HG5 3RT
98639          Dumbledore           04/07/1881          HG1 5FG                 
11611          Madam                Puddifoot                                    HG1 5XQ
27670          Tonks                02/04/1973          MN2 8YU                 
34652          Remus                Lupin               10/03/1960               HG5 3RT
50610          Molly                Weasley             30/10/1940               OS5 2ED
34093          Ginny                Weasley             11/06/1981               OS5 2ED
57862          Fred                 Weasley             01/04/1978               DA2 7YH
25317          George               Weasley             01/04/1978               DA2 7YH
25916          Arthur               Weasley             06/02/1950               OS5 2ED
56723          Neville              Longbottom          30/07/1980               L3 5TH
55678          Dean                 Thomas              05/10/1979              
93768          Seamus               Fineegan                                     D19 4RT
50789          Lee                  Thomas                                      
423756         Chang                Cho                                          G34 7EY
============== ==================== =================== ======================== ====================

Dataframe 2:

============== ==================== =================== ========================= ====================
Dataframe_2_ID Dataframe_2_Forename Dataframe_2_Surname Dataframe_2_Date of Birth Dataframe_2_Postcode
============== ==================== =================== ========================= ====================
A10552         Fomona               Sprout              15/04/1900                HG5 3RT
A16467         Steven               Smith               09/01/1960                HG5 3RT
A17203         I can't remember     Don't know          01/01/1960                ZZ99 3WS
A17407         Firenze              na                  na                        na
A19273         Stanley              Shunpike            13/09/1975                HA3 1WE
A19310         A                    Dumbledore          04/07/1881                HG1 5FG
A20470         Phillip              Flitwick            17/10/1958                HG5 3RT
A21378         Remus                Lupin               10/03/1960                WT4 RFG
A24048         Arthur               Weasley             06/02/1950                OS5 2ED
A25344         Madam Rolanda        Hooch               01/01/1918                HG5 3RT
A26780         Tonks                Tonks               02/04/1973                WT4 RFG
A34782         Fleur                Weasley             01/01/1921                PB4 2ER
A36313         Susie                Bones               09/01/1960                NO8 10OP
A37768         na                   Puddifoot           na                        HG1 5XQ
A37927         Professor            Dumbledore          01/01/1881                HG5 3RT
A38417         Annabella            Figg                03/04/1945                SU4 6TH
A44119         Thomas               Riddle              31/12/1926                HG5 3RT
A44534         James                Potter              na                        M45 8TY
A48420         Hermione             Granger             19/09/1979                N2 6gh
A50560         Ginevra              Weasley             11/06/1981                OS5 2ED
A60459         Gred                 Weasley             01/04/1978                OS5 2ED
A61236         Tom                  Dodderridger        01/01/1938                DA2 8TY
A62573         Sybill               Trelawny            09/03/1960                HG5 3RT
A63685         Minerva              Urquart             04/10/1907                HG5 3RT
A70296         Argus                Filch               01/01/1900                HG5 3RT
A71751         Ron                  Weasley             01/03/1980                OS5 2ED
A72749         Jessica              Malfoy              02/02/1955                WT4 5CV
A74043         Freoge               Weasley             01/04/1978                OS5 2ED
A74502         Horace               Ziugho              28/04/1901                HG5 3RT
A79723         Dedalus              Diggle              23/09/1964                K34 7YH
A84774         Rubeus               Hagrid              06/12/1920                HG5 3RT
A87124         Molly                Weasley             30/10/1940                OS5 2ED
A9019063       Harry                Potter              31/07/1980                SU4 6TH
A91109         Sirius               White               03/11/1959                C2 6TY
A92266         Nearly               Nick                31/10/1492                HG5 3RT
A92341         Dung                 Fletcher            na                        na
A92362         Alistaire            Moody               na                        HG5 3RT
A92675         Miranda              na                  07/06/1921                na
A92970         Lucius               Malfoy              03/06/1954                WT4 5CV
A95075         Amelia               Bones               12/08/1976                NO8 2ET
A95533         Cuthbert             Bones               na                        HG5 3RT
A97267         Lucius               Malfoy              05/06/1980                WT4 5CV
A98275         Bellatrix            LeStrange           na                        ZZ99 3Vz
============== ==================== =================== ========================= ====================

.. _section-4:

Step 2: Create random pairings between records from dataframe 1 and
dataframe 2:

==== ============== ============== ==================== ==================== =================== =================== ======================== ========================= ==================== ====================
Rand Dataframe_1_ID Dataframe_2_ID Dataframe_1_Forename Dataframe_2_Forename Dataframe_1_Surname Dataframe_2_Surname Dataframe_1_Dateof birth Dataframe_2_Date of Birth Dataframe_1_Postcode Dataframe_2_Postcode
==== ============== ============== ==================== ==================== =================== =================== ======================== ========================= ==================== ====================
1    19812          A10552         Filius               Fomona               Flitwick            Sprout              17/10/1958               15/04/1900                HG5 3RT              HG5 3RT             
2    98639          A16467         Aberforth            Steven               Dumbledore          Smith               04/07/1881               09/01/1960                HG1 5FG              HG5 3RT             
3    55629          A17203         Albus                I can't remember     Dumbledore          Don't know          01/01/1881               01/01/1960                HG5 3RT              ZZ99 3WS            
4    61758          A17407         Harry                Firenze              Potter              na                  31/07/1980               na                        SU4 6TH              na                  
5    99727          A19273         Pomona               Stanley              Sprout              Shunpike            15/04/1900               13/09/1975                HG5 3RT              HA3 1WE             
6    25916          A19310         Arthur               A                    Weasley             Dumbledore          06/02/1950               04/07/1881                OS5 2ED              HG1 5FG             
7    90267          A20470         Ronald               Phillip              Weasley             Flitwick            01/03/1980               17/10/1958                OS5 2ED              HG5 3RT             
8    52925          A21378         Minerva              Remus                McGonnagal          Lupin               04/10/1935               10/03/1960                HG5 3RT              WT4 RFG             
9    62114          A24048         Arabella             Arthur               Figg                Weasley             03/04/1945               06/02/1950                SU4 6TH              OS5 2ED             
10   65747          A25344         Argus                Madam Rolanda        Filch               Hooch               01/01/1950               01/01/1918                HG5 3RT              HG5 3RT             
11   50789          A26780         Lee                  Tonks                Thomas              Tonks                                        02/04/1973                                     WT4 RFG             
12   70110          A34782         Miranda              Fleur                Goshawk             Weasley             01/01/1921               01/01/1921                                     PB4 2ER             
13   27670          A36313         Nymphadora           Susie                Tonks               Bones               02/04/1973               09/01/1960                MN2 8YU              NO8 10OP            
14   15256          A37768         Sirius               na                   Black               Puddifoot                                    na                        ZZ99 3Vz             HG1 5XQ             
15   68887          A37927         Lord                 Professor            Voldemort           Dumbledore          31/12/1926               01/01/1881                ZZ99 3WS             HG5 3RT             
16   93768          A38417         Seamus               Annabella            Fineegan            Figg                                         03/04/1945                D19 4RT              SU4 6TH             
17   98120          A44119         Firenze              Thomas                                   Riddle                                       31/12/1926                                     HG5 3RT             
18   70165          A44534         Dedalus              James                Diggle              Potter              01/01/1964               na                        K34 7YH              M45 8TY             
19   83700          A48420         Sybill               Hermione             Trelawny            Granger             09/03/1960               19/09/1979                HG5 3RT              N2 6gh              
20   19536          A50560         Amelia               Ginevra              Bones               Weasley             12/08/1976               11/06/1981                NO8 2ET              OS5 2ED             
21   11611          A60459         Madam                Gred                 Puddifoot           Weasley                                      01/04/1978                HG1 5XQ              OS5 2ED             
22   63974          A61236         Cuthbert             Tom                  Binns               Dodderridger                                 01/01/1938                HG5 3RT              DA2 8TY             
23   34093          A62573         Ginny                Sybill               Weasley             Trelawny            11/06/1981               09/03/1960                OS5 2ED              HG5 3RT             
24   55678          A63685         Dean                 Minerva              Thomas              Urquart             05/10/1979               04/10/1907                                     HG5 3RT             
25   30499          A70296         Reuben               Argus                Hagrid              Filch               06/12/1928               01/01/1900                HG5 3RT                                  
26   34652          A71751         Remus                Ron                  Lupin               Weasley             10/03/1960               01/03/1980                HG5 3RT              OS5 2ED             
27   50610          A72749         Molly                Jessica              Weasley             Malfoy              30/10/1940               02/02/1955                OS5 2ED              WT4 5CV             
28   37732          A74043         Hermione             Freoge               Granger             Weasley             19/09/1979               01/04/1978                N2 6gh               OS5 2ED             
29   423756         A74502         Chang                Horace               Cho                 Ziugho                                       28/04/1901                G34 7EY              HG5 3RT             
30   97740          A79723         Horace               Dedalus              Slughorn            Diggle              28/04/1901               23/09/1964                HG5 3RT              K34 7YH             
31   77125          A84774         Ms                   Rubeus               LeStrange           Hagrid              01/01/1951               06/12/1920                HG5 3RT                                  
32   22847          A87124         Susan                Molly                Bones               Weasley             01/09/1980               30/10/1940                NO8 10OP             OS5 2ED             
33   38679          A9019063       Rolanda              Harry                Hooch               Potter              01/01/1918               31/07/1980                HG5 3RT              SU4 6TH             
34   24603          A91109         Professor Gilderoy   Sirius               Lockheart           White               26/01/1964               03/11/1959                HA5 4EW              C2 6TY              
35   31954          A92266         Malfoy               Nearly               Lucius              Nick                                         31/10/1492                WT4 5CV              HG5 3RT             
36   60168          A92341         Draco                Dung                 Malfoy              Fletcher            05/06/1980               na                        WT4 5CV              na                  
37   25317          A92362         George               Alistaire            Weasley             Moody               01/04/1978               na                        DA2 7YH              HG5 3RT             
38   78884          A92675         Sir Nicholas         Miranda              de Mimsy-Porpington na                  31/10/1492               07/06/1921                HG5 3RT              na                  
39   57862          A92970         Fred                 Lucius               Weasley             Malfoy              01/04/1978               03/06/1954                DA2 7YH              WT4 5CV             
40   56723          A95075         Neville              Amelia               Longbottom          Bones               30/07/1980               12/08/1976                L3 5TH               NO8 2ET             
41   48007          A95533         Narssica             Cuthbert             Black               Bones               02/02/1955               na                        C2 6TY               HG5 3RT             
42   49559          A97267         Severus              Lucius               Snape               Malfoy              09/01/1960               05/06/1980                HG5 3RT              WT4 5CV             
\                   A98275                              Bellatrix                                LeStrange                                    na                                             ZZ99 3Vz            
==== ============== ============== ==================== ==================== =================== =================== ======================== ========================= ==================== ====================

Step 3: Define matching variables

Forename, Surname, Date of Birth, Postcode

Step 4: For each matching variable, calculate proportion of pairs in the
training data where there is exact agreement between the dataframe 1 and
2 record

Number of agreeing pairs (:math:`P_{i}`) – For each variable, count the
number of pairs where the variable agrees exactly.

Number of disagreeing pairs (:math:`P_{j}`)– For each variable, count
the number of pairs where the variable does not agree exactly.

Proportion (U) = :math:`\frac{P_{i}}{(P_{i + \ P_{j})}}` = The
proportion of the total random non match pairs where the variable agrees
exactly.

Where U = 0, we substitute in 0.0000000001 or lower otherwise
fellegi-sunter fails due to division by 0.

============= ================================================= ================================================= ==============
Variable      Number of agreeing matching pairs (:math:`P_{i}`) Number of disagreeing match pairs (:math:`P_{j}`) Proportion (U)
============= ================================================= ================================================= ==============
Forename      0                                                 42                                                0.000000001
Surname       0                                                 42                                                0.000000001
Date of birth 1                                                 41                                                0.023809524
Postcode      2                                                 40                                                0.047619048
============= ================================================= ================================================= ==============

..

   4. Export the proportions as U values ready for use in probabilistic
   scoring

======== ===========
Variable U
======== ===========
Forename 0.000000001
Surname  0.000000001
DoB      0.023809524
Postcode 0.047619048
======== ===========

Scenarios to be Tested
~~~~~~~~~~~~~~~~~~~~~~

Standard method testing.

.. _section-5:

Treatment of Special Cases
~~~~~~~~~~~~~~~~~~~~~~~~~~

There are two special cases to consider, the first, missing values, is
generally present in all datasets, the second, fuzziness, is up to user
choice.

Treatment of missing values:

Missing values are present in nearly all datasets. In our calculations,
missingness does not count towards agreement. Thus, if we have a random
pair that both have a missing bate of birth, this is *not* recorded as
agreement and so does not contribute towards the count of agreeing pairs
in our U value calculation.

Fuzzy agreement refers to cases where a match pair does not agree
exactly, however there is some similarity between the values. Examples
could be a match pair where postcode area matches but the full postcode
does not, or a shortened version of a name to a fullname such as Anna
and Annabell. If users wish, they can calculate U values (and M values)
of these derived variables so that fuzzy agreement does not cause a
record pair to get a disagreement score but also not get the full
agreement score either (e.g. a score in between disagreement/agreement
is computed).

.. _section-6:

Example (Synthetic) Data 
~~~~~~~~~~~~~~~~~~~~~~~~~

Synthetic Dataframe 1:

============== ==================== ====================== =================== ================ ========================================== ========================================== ====================
Dataframe_1_ID Dataframe_1_Forename Dataframe_1_Middlename Dataframe_1_Surname DF1 Sex at Birth Dataframe_1_Date of birth                  DF1 first line of address                  Dataframe_1_Postcode
============== ==================== ====================== =================== ================ ========================================== ========================================== ====================
61758          Harry                James                  Potter              1                31/07/1980                                 The cupboard under the stairs              SU4 6TH
90267          Ronald               Bilius                 Weasley             1                01/03/1980                                 The Burrow, Ottery St Catchpole            OS5 2ED
37732          Hermione             Jean                   Granger             2                19/09/1979                                 4 Hampstead Garden                         N2 6gh
68887          Lord                                        Voldemort                            31/12/1926                                 Bulgaria                                   ZZ99 3WS
49559          Severus              Tobias                 Snape               1                09/01/1960                                 Hogwarts School of Witchcraft and Wizardry HG5 3RT
55629          Albus                Percival Wulfric Brian Dumbledore          1                01/01/1881                                 Headmasters Office                         HG5 3RT
30499          Reuben                                      Hagrid              1                06/12/1928                                 Gameskeepers Hut                          
22847          Susan                                       Bones               2                01/09/1980                                 Flat 2 Regly Road                          NO8 10OP
19536          Amelia                                      Bones               2                12/08/1976                                 12 Wizengamot Road                         NO8 2ET
24603          Professor Gilderoy   Lockheart              1                   26/01/1964       6 Kelpie Road                              HA5 4EW                                   
98120          Firenze                                                         1                                                                                                     
70165          Dedalus                                     Diggle              1                01/01/1964                                 12 Kent Rd                                 K34 7YH
62114          Arabella                                    Figg                2                03/04/1945                                 6 Privet Drive                             SU4 6TH
70110          Miranda                                     Goshawk             2                01/01/1921                                                                           
60168          Draco                Lucius                 Malfoy              1                05/06/1980                                 Malfoy Manor                               WT4 5CV
31954          Malfoy               Lucius                 Lucius              1                                                           Malfoy Manor                               WT4 5CV
48007          Narssica                                    Black               2                02/02/1955                                 12 Grimmauld Place                         C2 6TY
15256          Sirius                                      Black               1                                                           UNKNOWN                                    ZZ99 3Vz
77125          Ms                                          LeStrange           2                01/01/1951                                 Cell 666                                  
52925          Minerva                                     McGonnagal          2                04/10/1935                                 Griffindor Tower                           HG5 3RT
19812          Filius               F                      Flitwick            1                17/10/1958                                 Ravenclaw Tower                            HG5 3RT
99727          Pomona                                      Sprout              2                15/04/1900                                 Hufflepuff Office                          HG5 3RT
65747          Argus                                       Filch               1                01/01/1950                                 Hogwarts School of Witchcraft and Wizardry HG5 3RT
78884          Sir Nicholas         de Mimsy-Porpington    1                   31/10/1492       Hogwarts School of Witchcraft and Wizardry HG5 3RT                                   
83700          Sybill               Patricia               Trelawny            2                09/03/1960                                 Astology Tower                             HG5 3RT
63974          Cuthbert                                    Binns               1                                                           4F                                         HG5 3RT
97740          Horace               Eugene                 Slughorn            1                28/04/1901                                 Head of Slytherin's Office, The Dungeons   HG5 3RT
38679          Rolanda                                     Hooch               2                01/01/1918                                 Flight Instructor's Office                 HG5 3RT
98639          Aberforth            Dumbledore             1                   04/07/1881       Hog's Head                                 HG1 5FG                                   
11611          Madam                                       Puddifoot           2                                                           3 High Street                              HG1 5XQ
27670          Nymphadora           Tonks                  2                   02/04/1973       Office 42                                  MN2 8YU                                   
34652          Remus                                       Lupin               1                10/03/1960                                 Defense Against Dark Arts Office           HG5 3RT
50610          Molly                                       Weasley             2                30/10/1940                                 The Burrow, Ottery St Catchpole            OS5 2ED
34093          Ginny                Molly                  Weasley             2                11/06/1981                                 The Burrow, Ottery St Catchpole            OS5 2ED
57862          Fred                 Arthur                 Weasley             1                01/04/1978                                 Weasley's Wizard Wheezes                   DA2 7YH
25317          George               Arthur                 Weasley             1                01/04/1978                                 Weasley's Wizard Wheezes                   DA2 7YH
25916          Arthur                                      Weasley             1                06/02/1950                                 The Burrow, Ottery St Catchpole            OS5 2ED
56723          Neville                                     Longbottom          1                30/07/1980                                 the Leaky Cauldron                         L3 5TH
55678          Dean                                        Thomas              1                05/10/1979                                                                           
93768          Seamus                                      Fineegan            1                                                           5 Cairn Road                               D19 4RT
50789          Lee                                         Thomas              1                                                                                                     
423756         Chang                                       Cho                 2                                                           4 Glasgow Road                             G34 7EY
============== ==================== ====================== =================== ================ ========================================== ========================================== ====================

Synthetic Dataframe 2:

============== ==================== ====================== =================== ======= ========================= ========================================== ====================
Dataframe_2_ID Dataframe_2_Forename Dataframe_2_Middlename Dataframe_2_Surname DF2 Sex Dataframe_2_Date of Birth DF2 first line of address                  Dataframe_2_Postcode
============== ==================== ====================== =================== ======= ========================= ========================================== ====================
A10552         Fomona               na                     Sprout              2       15/04/1900                Basement                                   HG5 3RT
A16467         Steven               Tobias                 Smith               1       09/01/1960                Head of Slytherin's Office, The Dungeons   HG5 3RT
A17203         I can't remember     na                     Don't know          na      01/01/1960                St Mungo's                                 ZZ99 3WS
A17407         Firenze              na                     na                  1       na                        na                                         na
A19273         Stanley              Stan                   Shunpike            1       13/09/1975                Room 2                                     HA3 1WE
A19310         A                    na                     Dumbledore          1       04/07/1881                Room 1                                     HG1 5FG
A20470         Phillip              na                     Flitwick            1       17/10/1958                Ravenclaw Tower                            HG5 3RT
A21378         Remus                na                     Lupin               1       10/03/1960                Star Cottage                               WT4 RFG
A24048         Arthur               na                     Weasley             1       06/02/1950                The Burrow                                 OS5 2ED
A25344         Madam Rolanda        na                     Hooch               2       01/01/1918                Hogwarts School of Witchcraft and Wizardry HG5 3RT
A26780         Tonks                na                     Tonks               2       02/04/1973                Star Cottage                               WT4 RFG
A34782         Fleur                na                     Weasley             2       01/01/1921                Shell Cottage                              PB4 2ER
A36313         Susie                na                     Bones               2       09/01/1960                2 Regly Road                               NO8 10OP
A37768         na                   na                     Puddifoot           2       na                        3 High Street                              HG1 5XQ
A37927         Professor            Albus                  Dumbledore          1       01/01/1881                Hogwarts School of Witchcraft and Wizardry HG5 3RT
A38417         Annabella            na                     Figg                2       03/04/1945                6 Privet Dr                                SU4 6TH
A44119         Thomas               Malvolo                Riddle              1       31/12/1926                The Dungeons                               HG5 3RT
A44534         James                na                     Potter              1       na                        Godric's Hollow                            M45 8TY
A48420         Hermione             Jean                   Granger             2       19/09/1979                4 hampstead Garden                         N2 6gh
A50560         Ginevra              na                     Weasley             2       11/06/1981                The Burrow                                 OS5 2ED
A60459         Gred                 Freoge                 Weasley             1       01/04/1978                The Burrow                                 OS5 2ED
A61236         Tom                  na                     Dodderridger        1       01/01/1938                Leaky Cauldron                             DA2 8TY
A62573         Sybill               Potter                 Trelawny            2       09/03/1960                Hogwarts School of Witchcraft and Wizardry HG5 3RT
A63685         Minerva              na                     Urquart             2       04/10/1907                Deupty Head's Office                       HG5 3RT
A70296         Argus                na                     Filch               2       01/01/1900                Hogwarts School of Witchcraft and Wizardry HG5 3RT
A71751         Ron                  B                      Weasley             1       01/03/1980                The Burrow                                 OS5 2ED
A72749         Jessica              na                     Malfoy              2       02/02/1955                Malfoy Manor                               WT4 5CV
A74043         Freoge               Gred                   Weasley             1       01/04/1978                The Burrow                                 OS5 2ED
A74502         Horace               Eugene                 Ziugho              1       28/04/1901                Head of Slytherin's Office, The Dungeons   HG5 3RT
A79723         Dedalus              na                     Diggle              2       23/09/1964                12 Kent Road                               K34 7YH
A84774         Rubeus               na                     Hagrid              1       06/12/1920                Hogwarts School of Witchcraft and Wizardry HG5 3RT
A87124         Molly                na                     Weasley             2       30/10/1940                The Burrow                                 OS5 2ED
A9019063       Harry                James                  Potter              1       31/07/1980                4 privet drive                             SU4 6TH
A91109         Sirius               Potter                 White               1       03/11/1959                12 Grimmauld Place                         C2 6TY
A92266         Nearly               Headless               Nick                1       31/10/1492                Hogwarts School of Witchcraft and Wizardry HG5 3RT
A92341         Dung                 na                     Fletcher            1       na                        na                                         na
A92362         Alistaire            na                     Moody               1       na                        Hogwarts School of Witchcraft and Wizardry HG5 3RT
A92675         Miranda              Goshawk                na                  2       07/06/1921                na                                         na
A92970         Lucius               na                     Malfoy              1       03/06/1954                Malfoy Manor                               WT4 5CV
A95075         Amelia               na                     Bones               2       12/08/1976                12 Wizengamot Road                         NO8 2ET
A95533         Cuthbert             na                     Bones               1       na                        Hogwarts School of Witchcraft and Wizardry HG5 3RT
A97267         Lucius               Draco                  Malfoy              1       05/06/1980                Malfoy Manor                               WT4 5CV
A98275         Bellatrix            na                     LeStrange           2       na                        na                                         ZZ99 3Vz
============== ==================== ====================== =================== ======= ========================= ========================================== ====================

.. _section-7:

Code
~~~~

.. code-block:: python

   # Pyspark/Python pseudo code script for calculating U values from training data that contains the true_match pairs between dataframe 1 and dataframe 2

   # Author: datalinkage@ons.gov.uk

   # v: 1.0

   # --- Set Up --- #

   # This stage will import packages, start spark, import data and input your desired matching variables

   import pandas as pd

   from pyspark.sql import SparkSession

   # Create a sparksession to read in parquet or other large files

   spark = SparkSession.builder.appName("default-session").getOrCreate()

   # Import your two datasets to be linked as spark dataframes

   df1 = spark.read.parquet("folder/file_location_for_df1")

   df2 = spark.read.parquet("folder/file_location_for_df2")

   # ---- Randomly pair up records from each dataframe ---- #

   # Feel free to use your own method to do this, the below isn't ideal

   # Add a column to each dataframe with a random ID in, but note that this number needs to be present in both dataframes

   df1 = df1.withColumn('pseudo_number',monotonically_increasing_id())

   df2 = df2.withColumn('pseudo_number',monotonically_increasing_id())

   # Join df1 and df2 together on this random number to produce random pairs

   training_data = df1.join(df2, on= 'pseudo_number', how = 'inner').drop('pseudo_number')

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

   # This stage will iterate through the list of matching variables and spit out a U value for each one

   # U value calucation will be: number of pairs where matching variables agreed exactly / total number of pairs

   # Define the total number of pairs

   total_pairs = training_data.count()

   # create empty list to append M values to

   u_list = []

   # Iterate through each variable to calculate a U value and save it to
   u_list

   for variable in matching_vars:

	   matching_pairs = training_data.filter(col("dataframe_1_" + variable)
	   == col("dataframe_2_" + variable)).count()

   u_value = matching_pairs / total_pairs

   u_list.append(m_value)

   # Transform list of M_values into a dataframe using pandas

   U_values = pd.DataFrame()

   U_values["matching_variable"] = matching_vars

   U_values["U"] = u_list

   U_values.write.csv("folder/m_values",header=True)

References
~~~~~~~~~~

`Link to data linkage training
course <https://learninghub.ons.gov.uk/enrol/index.php?id=1301>`__

`Link to Fellegi-Sunter Paper on probabilistic
linkage <https://www.tandfonline.com/doi/abs/10.1080/01621459.1969.10501049>`__

`Link to discussion on probabilistic linkage by Doidge and
Harron <https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6281162/>`__

.. _section-8:


