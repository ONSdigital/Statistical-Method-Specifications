## Cell key Perturbation Specification

### 1.0 Meta  

---

Support area - Methodology - Statistical Disclosure Control

Method theme - Statistical Disclosure Control

Status - Completed, tested, and signed off

### 2.0 Terminology  

---

* Record key -- A random number between 0-255 assigned to each record (person,
household or other statistical unit). These keys do not change. (A range of
0-255 is used for census 2021, other datasets may use a range of 0-4095).

* Cell key -- The sum of record keys for a given cell. Modulo 256 is taken so
the cell keys remain in the range 0-255. (or modulo 4096 to keep a range of
0-4095).

* Cell value -- The number of records in cell, ie the number of people with
given characteristics

* pvalue -- perturbation value. The value of noise added to cells, e.g. +1, -1

* pcv -- perturbation cell value. This is an amended cell value needed to
merge on the ptable

* ptable -- perturbation table. The lookup file containing the pvalues, this
determines which cell get perturbed.

### 3.0 Introduction  

---

Cell key perturbation adds noise ('perturbation') to cell counts in frequency
tables. This noise introduces uncertainty in individual values, and thereby
introduces uncertainty to potential disclosures. The method is specifically
designed to protect against disclosure by differencing. This method was used
in ONS Census 2021.

### 4.0 Assumptions  

---

Cell-Key Perturbation works based on the following assumptions:

* The analyst is producing a frequency table that requires cell key
perturbation protection.

* The analyst has a ptable available to them, relevant to their dataset,
supplied by the SDC team or data owners.

* The data used contains one row per record (person, household, business, or
other individual) and one column for each variable/characteristic.

The method is only suitable for producing frequency tables, and cannot be
applied without a ptable file. It may or may not be possible to apply the
method to data that does not contain one row per record and one column per
variable.

### 5.0 Data records  

---

The following field types will be present in the input and output records.

### 5.1 Input records  

---

<!-- markdownlint-disable MD037 -->



 | Variable Definition |Type of Variable| Format of specific variable (if applicable)| Expected range of the values | Meaning of the values| Expected level of aggregation | Comments | 
 |:---       |:---     |:---     |:---   |:--- |:--- |:--- |
 | Record key| Integer | Integer | 0-255 |The numbers are random and have no meaning. The 'keys' are used as inputs in perturbation to apply consistency| One per statistical unit (person, household, business) |  The name of the record key column is supplied to the function. Record key variables may be named as record_key, record_key_person etc |



<!-- markdownlint-enable MD037 -->

* The microdata used is expected to contain one row per record (person,
household, business, or other individual) and one column for each
variable/characteristic.

* The microdata needs a column containing record key, random numbers between
0-255.

* The method needs a ptable file, containing the perturbation/noise to be
added.

* The function call requires a list of variables to be tabulated

* The record key needs to be numeric (integer) so it can be used in
calculations (modulo sum). The other variables are expected to be categorical
(they can be numeric but categorical is more suitable for frequency tables).

### 5.2 Output records  

---

<!-- markdownlint-disable MD037 -->



 |Variable definition| Type of variable| Format of specific variable (if applicable)| Expected range of the values| Meaning of the values | Expected level of aggregation | Comments |
 |:---       |:---     |:---     |:---   |:--- |:--- |:--- |
 |ckey       | integer || 0-255   | No intrinsic meaning. This value determines the perturbation to be added and ensures consistency | One value per cell in a frequency table | | |
 |pcv        | integer || 0-750 | No intrinsic meaning. This value is an edited version of the count designed to reduce the necessary size of the ptable file. For cell values 0-750, pcv is the same as cell value | One value per cell in a frequency table | | |     
 |count     | integer  || >=0  | The cell value after perturbation. The 'count' of how many records contain a combination of characteristics, plus the noise added by perturbation. E.g. there could be 14 males aged 18-24 in an area. Noise of +1 is applied to the cell so the post perturbation 'count' is 15. | One value per cell in a frequency table | | | 


 
<!-- markdownlint-enable MD037 -->

---

* The count table will be in 'long' format sometimes known as tidy format, with
one row per combination of characteristics. One column is included for each
variable tabulated, e.g.: Local Authority, Age, Sex, count, pcv, pvalue, count;
Bristol, 16-24, 1, 503, 503, +2, 505.

### 5.3 Error handling  

---

Input data will need to be checked for errors, and that data contains record
keys in the appropriate format.

Any planned data editing should also be performed before the perturbation. The
method will assume the data is correct and produce outputs accordingly.

Where data contain missing values, the method will by default carry out
perturbation and produce results for missing categories. If categories are to
be combined or values imputed to replace missing data, this should be done
before the perturbation.

### 6.0 Overall Method  

---

The code produces a frequency table, counting how many records contain
combinations of each of the supplied variables. In the same step, it takes the
sum of the record keys of those records and takes modulo 256. The modulo is
used so that cell keys are uniform in the range 0-255 (or 0-4095), which allows
the method to specify and control the perturbation rate.

To prevent the need for a very large ptable file, the rows 501-750 are also
used for higher cell counts. Row 501 is used for cell counts 501, 751, 1001, 
1251... Row 502 is used for cell counts 502, 752, 1002. To achieve this effect,
a column named \'pcv\' (perturbation cell value) is created and cell counts
above 750 are transformed by subtracting 1, taking modulo 250 (to get the range
0-249) then adding 501 (to get the range 501-750).

The ptable file is merged on using a left join, so every cell in the table is
covered, matching on values of cell-key and pcv. The post-perturbation count
is created by adding the pre-perturbation count and the designated noise from
the ptable, the 'pvalue' column (perturbation value).

The end result is a table of combinations of variables, provided by the user, 
and the 'count' column has had the required noise from the ptable applied.

### 7.0 Algorithm  

---

The user supplies the data to be analysed, variables to be tabulated including
geography, and the ptable.

A standard frequency table is produced containing combinations of the provided
variables and the number of records with those characteristics.

A similar table is produced on the same variables taking the sum of the record
keys for each cell (row) of the frequency table. These tables are merged so the
table now contains both the number of records in a cell and the sum of the
record keys of those records.

Modulo 256 is taken of the sum of record keys to produce 'cell keys' which are
uniform distributed in the range 0-255. (Or modulo 4096 to keep a range of
0-4095)"

A 'pcv' is created: Where the cell value <= 750, pcv = cell value where cell
value >750, pcv = modulo((cell value -1), 250), +501.

The ptable, containing pvalues, is merged onto the frequency table matching on
pcv and cell key.

The post perturbation value = cell value + pvalue. This post-SDC value is used
in outputs.
