# Ratio of Means Specification

## Overview

### Support Area

Methodology - Editing & imputation <Editing.and.Imputation.expert.group@ons.gov.uk>

### Method theme

Imputation

### Method classification

Ratio imputation

### Status

Partially tested, draft (not published)

### Inputs

+-------+-------+-------+-------+-------+-------+-------+-------+
| Var   | Type  | F     | Exp   | Me    | Exp   | Freq  | Com   |
| iable | of    | ormat | ected | aning | ected | uency | ments |
| defin | var   | of    | range | of    | level |       |       |
| ition | iable | spe   | of    | the   | of    |       |       |
|       |       | cific | the   | v     | a     |       |       |
|       |       | var   | v     | alues | ggreg |       |       |
|       |       | iable | alues |       | ation |       |       |
|       |       | (if   |       |       |       |       |       |
|       |       | a     |       |       |       |       |       |
|       |       | pplic |       |       |       |       |       |
|       |       | able) |       |       |       |       |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| U     | Any   |       | \     | \"A   | Repo  |       | Used  |
| nique | Any   |       | "A\", | \"/\" | rting |       | to    |
| ident | Date  |       | \     | Annua | unit  |       | part  |
| ifier | Nu    |       | "M\", | lly\" | level |       | ition |
| Imput | meric |       | \     | =     | Repo  |       | the   |
| ation | Nu    |       | "Q\", | data  | rting |       | data  |
| class | meric |       | \"A   | pro   | unit  |       | into  |
| P     | Nu    |       | nnual | vided | level |       | imput |
| eriod | meric |       | ly\", | ann   | Repo  |       | ation |
| T     | Nu    |       |       | ually | rting |       | cla   |
| arget | meric |       | \"    |       | unit  |       | sses. |
| var   | Nu    |       | Month | \"    | level |       | Mul   |
| iable | meric |       | ly\", | M\"/\ | Repo  |       | tiple |
| Auxi  | S     |       | \"Q   | "Mont | rting |       | co    |
| liary | tring |       | uarte | hly\" | unit  |       | lumns |
| var   |       |       | rly\" | =     | level |       | can   |
| iable | Data  |       |       | data  | Repo  |       | be    |
| For   | Frame |       |       | pro   | rting |       | used  |
| wards |       |       |       | vided | unit  |       | to    |
| imput | Bo    |       |       | on a  | level |       | d     |
| ation | olean |       |       | mo    | Repo  |       | efine |
| l     |       |       |       | nthly | rting |       | the   |
| ink\* |       |       |       | basis | unit  |       | imput |
| Back  |       |       |       |       | level |       | ation |
| wards |       |       |       | \"Q\  | Repo  |       | c     |
| imput |       |       |       | "/\"Q | rting |       | lass. |
| ation |       |       |       | uarte | unit  |       |       |
| l     |       |       |       | rly\" | level |       | The   |
| ink\* |       |       |       | =     | Repo  |       | m     |
| Co    |       |       |       | data  | rting |       | ethod |
| nstru |       |       |       | pro   | unit  |       | will  |
| ction |       |       |       | vided | level |       | a     |
| imput |       |       |       | on a  |       |       | ssume |
| ation |       |       |       | quar  | Repo  |       | pr    |
| l     |       |       |       | terly | rting |       | esent |
| ink\* |       |       |       | basis | unit  |       | v     |
| P     |       |       |       |       | level |       | alues |
| eriod |       |       |       |      |       |       | are   |
| icity |       |       |       |  will | Repo  |       | ret   |
| (fun  |       |       |       | > be  | rting |       | urned |
| ction |       |       |       |      | unit  |       | v     |
| call  |       |       |       |  prod | level |       | alues |
| param |       |       |       | uced. |       |       | and   |
| eter, |       |       |       |       |       |       | blank |
| not a |       |       |       |       |       |       | cells |
| vari  |       |       |       |       |       |       | are   |
| able) |       |       |       |       |       |       | to be |
|       |       |       |       |       |       |       | imp   |
| Back  |       |       |       |       |       |       | uted. |
| data  |       |       |       |       |       |       |       |
|       |       |       |       |       |       |       | Opt   |
| in    |       |       |       |       |       |       | ional |
| clusi |       |       |       |       |       |       | vari  |
| on\_m |       |       |       |       |       |       | able. |
| arker |       |       |       |       |       |       | S     |
|       |       |       |       |       |       |       | hould |
|       |       |       |       |       |       |       | be    |
|       |       |       |       |       |       |       | pro   |
|       |       |       |       |       |       |       | vided |
|       |       |       |       |       |       |       | if    |
|       |       |       |       |       |       |       | the   |
|       |       |       |       |       |       |       | user  |
|       |       |       |       |       |       |       | would |
|       |       |       |       |       |       |       | like  |
|       |       |       |       |       |       |       | to    |
|       |       |       |       |       |       |       | for   |
|       |       |       |       |       |       |       | wards |
|       |       |       |       |       |       |       | i     |
|       |       |       |       |       |       |       | mpute |
|       |       |       |       |       |       |       | v     |
|       |       |       |       |       |       |       | alues |
|       |       |       |       |       |       |       | using |
|       |       |       |       |       |       |       | an    |
|       |       |       |       |       |       |       | a     |
|       |       |       |       |       |       |       | ltern |
|       |       |       |       |       |       |       | ative |
|       |       |       |       |       |       |       | for   |
|       |       |       |       |       |       |       | wards |
|       |       |       |       |       |       |       | imput |
|       |       |       |       |       |       |       | ation |
|       |       |       |       |       |       |       | link. |
|       |       |       |       |       |       |       | Can   |
|       |       |       |       |       |       |       | co    |
|       |       |       |       |       |       |       | ntain |
|       |       |       |       |       |       |       | n     |
|       |       |       |       |       |       |       | ulls. |
|       |       |       |       |       |       |       | Opt   |
|       |       |       |       |       |       |       | ional |
|       |       |       |       |       |       |       | vari  |
|       |       |       |       |       |       |       | able. |
|       |       |       |       |       |       |       | S     |
|       |       |       |       |       |       |       | hould |
|       |       |       |       |       |       |       | be    |
|       |       |       |       |       |       |       | pro   |
|       |       |       |       |       |       |       | vided |
|       |       |       |       |       |       |       | if    |
|       |       |       |       |       |       |       | the   |
|       |       |       |       |       |       |       | user  |
|       |       |       |       |       |       |       | would |
|       |       |       |       |       |       |       | like  |
|       |       |       |       |       |       |       | to    |
|       |       |       |       |       |       |       | back  |
|       |       |       |       |       |       |       | wards |
|       |       |       |       |       |       |       | i     |
|       |       |       |       |       |       |       | mpute |
|       |       |       |       |       |       |       | v     |
|       |       |       |       |       |       |       | alues |
|       |       |       |       |       |       |       | using |
|       |       |       |       |       |       |       | an    |
|       |       |       |       |       |       |       | a     |
|       |       |       |       |       |       |       | ltern |
|       |       |       |       |       |       |       | ative |
|       |       |       |       |       |       |       | back  |
|       |       |       |       |       |       |       | wards |
|       |       |       |       |       |       |       | imput |
|       |       |       |       |       |       |       | ation |
|       |       |       |       |       |       |       | link. |
|       |       |       |       |       |       |       | Can   |
|       |       |       |       |       |       |       | co    |
|       |       |       |       |       |       |       | ntain |
|       |       |       |       |       |       |       | n     |
|       |       |       |       |       |       |       | ulls. |
|       |       |       |       |       |       |       | Opt   |
|       |       |       |       |       |       |       | ional |
|       |       |       |       |       |       |       | vari  |
|       |       |       |       |       |       |       | able. |
|       |       |       |       |       |       |       | S     |
|       |       |       |       |       |       |       | hould |
|       |       |       |       |       |       |       | be    |
|       |       |       |       |       |       |       | pro   |
|       |       |       |       |       |       |       | vided |
|       |       |       |       |       |       |       | if    |
|       |       |       |       |       |       |       | the   |
|       |       |       |       |       |       |       | user  |
|       |       |       |       |       |       |       | would |
|       |       |       |       |       |       |       | like  |
|       |       |       |       |       |       |       | to    |
|       |       |       |       |       |       |       | cons  |
|       |       |       |       |       |       |       | truct |
|       |       |       |       |       |       |       | v     |
|       |       |       |       |       |       |       | alues |
|       |       |       |       |       |       |       | using |
|       |       |       |       |       |       |       | an    |
|       |       |       |       |       |       |       | a     |
|       |       |       |       |       |       |       | ltern |
|       |       |       |       |       |       |       | ative |
|       |       |       |       |       |       |       | co    |
|       |       |       |       |       |       |       | nstru |
|       |       |       |       |       |       |       | ction |
|       |       |       |       |       |       |       | imput |
|       |       |       |       |       |       |       | ation |
|       |       |       |       |       |       |       | link. |
|       |       |       |       |       |       |       | Can   |
|       |       |       |       |       |       |       | co    |
|       |       |       |       |       |       |       | ntain |
|       |       |       |       |       |       |       | n     |
|       |       |       |       |       |       |       | ulls. |
|       |       |       |       |       |       |       |       |
|       |       |       |       |       |       |       | Opt   |
|       |       |       |       |       |       |       | ional |
|       |       |       |       |       |       |       | param |
|       |       |       |       |       |       |       | eter. |
|       |       |       |       |       |       |       | S     |
|       |       |       |       |       |       |       | ingle |
|       |       |       |       |       |       |       | p     |
|       |       |       |       |       |       |       | eriod |
|       |       |       |       |       |       |       | of    |
|       |       |       |       |       |       |       | data  |
|       |       |       |       |       |       |       | to be |
|       |       |       |       |       |       |       | pro   |
|       |       |       |       |       |       |       | vided |
|       |       |       |       |       |       |       | when  |
|       |       |       |       |       |       |       | prior |
|       |       |       |       |       |       |       | data  |
|       |       |       |       |       |       |       | is    |
|       |       |       |       |       |       |       | avi   |
|       |       |       |       |       |       |       | lable |
|       |       |       |       |       |       |       | for   |
|       |       |       |       |       |       |       | i     |
|       |       |       |       |       |       |       | mputa |
|       |       |       |       |       |       |       | tion. |
|       |       |       |       |       |       |       | This  |
|       |       |       |       |       |       |       | p     |
|       |       |       |       |       |       |       | eriod |
|       |       |       |       |       |       |       | must  |
|       |       |       |       |       |       |       | be    |
|       |       |       |       |       |       |       | the   |
|       |       |       |       |       |       |       | pre   |
|       |       |       |       |       |       |       | vious |
|       |       |       |       |       |       |       | p     |
|       |       |       |       |       |       |       | eriod |
|       |       |       |       |       |       |       | with  |
|       |       |       |       |       |       |       | re    |
|       |       |       |       |       |       |       | gards |
|       |       |       |       |       |       |       | to    |
|       |       |       |       |       |       |       | the   |
|       |       |       |       |       |       |       | p     |
|       |       |       |       |       |       |       | eriod |
|       |       |       |       |       |       |       | icity |
|       |       |       |       |       |       |       | of    |
|       |       |       |       |       |       |       | the   |
|       |       |       |       |       |       |       | da    |
|       |       |       |       |       |       |       | taset |
|       |       |       |       |       |       |       | being |
|       |       |       |       |       |       |       | im    |
|       |       |       |       |       |       |       | puted |
|       |       |       |       |       |       |       | and   |
|       |       |       |       |       |       |       | must  |
|       |       |       |       |       |       |       | be    |
|       |       |       |       |       |       |       | fully |
|       |       |       |       |       |       |       | im    |
|       |       |       |       |       |       |       | puted |
|       |       |       |       |       |       |       | e.g.  |
|       |       |       |       |       |       |       | if    |
|       |       |       |       |       |       |       | you   |
|       |       |       |       |       |       |       | are   |
|       |       |       |       |       |       |       | lo    |
|       |       |       |       |       |       |       | oking |
|       |       |       |       |       |       |       | to    |
|       |       |       |       |       |       |       | i     |
|       |       |       |       |       |       |       | mpute |
|       |       |       |       |       |       |       | for   |
|       |       |       |       |       |       |       | Febr  |
|       |       |       |       |       |       |       | uary, |
|       |       |       |       |       |       |       | M     |
|       |       |       |       |       |       |       | arch, |
|       |       |       |       |       |       |       | April |
|       |       |       |       |       |       |       | then  |
|       |       |       |       |       |       |       | this  |
|       |       |       |       |       |       |       | c     |
|       |       |       |       |       |       |       | olumn |
|       |       |       |       |       |       |       | r     |
|       |       |       |       |       |       |       | efers |
|       |       |       |       |       |       |       | to    |
|       |       |       |       |       |       |       | Ja    |
|       |       |       |       |       |       |       | nuary |
|       |       |       |       |       |       |       | (the  |
|       |       |       |       |       |       |       | p     |
|       |       |       |       |       |       |       | eriod |
|       |       |       |       |       |       |       | prior |
|       |       |       |       |       |       |       | to    |
|       |       |       |       |       |       |       | the   |
|       |       |       |       |       |       |       | p     |
|       |       |       |       |       |       |       | eriod |
|       |       |       |       |       |       |       | range |
|       |       |       |       |       |       |       | where |
|       |       |       |       |       |       |       | imput |
|       |       |       |       |       |       |       | ation |
|       |       |       |       |       |       |       | is    |
|       |       |       |       |       |       |       | requ  |
|       |       |       |       |       |       |       | ired) |
|       |       |       |       |       |       |       | and   |
|       |       |       |       |       |       |       | s     |
|       |       |       |       |       |       |       | hould |
|       |       |       |       |       |       |       | in    |
|       |       |       |       |       |       |       | clude |
|       |       |       |       |       |       |       | all   |
|       |       |       |       |       |       |       | data  |
|       |       |       |       |       |       |       | types |
|       |       |       |       |       |       |       | (e.g. |
|       |       |       |       |       |       |       | resp  |
|       |       |       |       |       |       |       | onse, |
|       |       |       |       |       |       |       | im    |
|       |       |       |       |       |       |       | puted |
|       |       |       |       |       |       |       | and   |
|       |       |       |       |       |       |       | c     |
|       |       |       |       |       |       |       | onstr |
|       |       |       |       |       |       |       | ucted |
|       |       |       |       |       |       |       | val   |
|       |       |       |       |       |       |       | ues). |
|       |       |       |       |       |       |       | If    |
|       |       |       |       |       |       |       | not   |
|       |       |       |       |       |       |       | prov  |
|       |       |       |       |       |       |       | ided, |
|       |       |       |       |       |       |       | the   |
|       |       |       |       |       |       |       | m     |
|       |       |       |       |       |       |       | ethod |
|       |       |       |       |       |       |       | will  |
|       |       |       |       |       |       |       | pe    |
|       |       |       |       |       |       |       | rform |
|       |       |       |       |       |       |       | sta   |
|       |       |       |       |       |       |       | ndard |
|       |       |       |       |       |       |       | i     |
|       |       |       |       |       |       |       | mputa |
|       |       |       |       |       |       |       | tion. |
|       |       |       |       |       |       |       | The   |
|       |       |       |       |       |       |       | da    |
|       |       |       |       |       |       |       | taset |
|       |       |       |       |       |       |       | must  |
|       |       |       |       |       |       |       | co    |
|       |       |       |       |       |       |       | ntain |
|       |       |       |       |       |       |       | the   |
|       |       |       |       |       |       |       | u     |
|       |       |       |       |       |       |       | nqiue |
|       |       |       |       |       |       |       | i     |
|       |       |       |       |       |       |       | denti |
|       |       |       |       |       |       |       | fier, |
|       |       |       |       |       |       |       | imput |
|       |       |       |       |       |       |       | ation |
|       |       |       |       |       |       |       | c     |
|       |       |       |       |       |       |       | lass, |
|       |       |       |       |       |       |       | pe    |
|       |       |       |       |       |       |       | riod, |
|       |       |       |       |       |       |       | o     |
|       |       |       |       |       |       |       | utput |
|       |       |       |       |       |       |       | vari  |
|       |       |       |       |       |       |       | able, |
|       |       |       |       |       |       |       | imput |
|       |       |       |       |       |       |       | ation |
|       |       |       |       |       |       |       | ma    |
|       |       |       |       |       |       |       | rker, |
|       |       |       |       |       |       |       | for   |
|       |       |       |       |       |       |       | wards |
|       |       |       |       |       |       |       | imput |
|       |       |       |       |       |       |       | ation |
|       |       |       |       |       |       |       | link, |
|       |       |       |       |       |       |       | back  |
|       |       |       |       |       |       |       | wards |
|       |       |       |       |       |       |       | imput |
|       |       |       |       |       |       |       | ation |
|       |       |       |       |       |       |       | link  |
|       |       |       |       |       |       |       | and   |
|       |       |       |       |       |       |       | co    |
|       |       |       |       |       |       |       | nstru |
|       |       |       |       |       |       |       | ction |
|       |       |       |       |       |       |       | link  |
|       |       |       |       |       |       |       | col   |
|       |       |       |       |       |       |       | umns. |
|       |       |       |       |       |       |       | This  |
|       |       |       |       |       |       |       | is an |
|       |       |       |       |       |       |       | opt   |
|       |       |       |       |       |       |       | ional |
|       |       |       |       |       |       |       | input |
|       |       |       |       |       |       |       | that  |
|       |       |       |       |       |       |       | indi  |
|       |       |       |       |       |       |       | cates |
|       |       |       |       |       |       |       | if a  |
|       |       |       |       |       |       |       | c     |
|       |       |       |       |       |       |       | ontri |
|       |       |       |       |       |       |       | butor |
|       |       |       |       |       |       |       | s     |
|       |       |       |       |       |       |       | hould |
|       |       |       |       |       |       |       | be    |
|       |       |       |       |       |       |       | exc   |
|       |       |       |       |       |       |       | luded |
|       |       |       |       |       |       |       | from  |
|       |       |       |       |       |       |       | the   |
|       |       |       |       |       |       |       | imput |
|       |       |       |       |       |       |       | ation |
|       |       |       |       |       |       |       | link  |
|       |       |       |       |       |       |       | cal   |
|       |       |       |       |       |       |       | culat |
|       |       |       |       |       |       |       | ions. |
|       |       |       |       |       |       |       | V     |
|       |       |       |       |       |       |       | alues |
|       |       |       |       |       |       |       | acc   |
|       |       |       |       |       |       |       | epted |
|       |       |       |       |       |       |       | are   |
|       |       |       |       |       |       |       | True  |
|       |       |       |       |       |       |       | or    |
|       |       |       |       |       |       |       | F     |
|       |       |       |       |       |       |       | alse, |
|       |       |       |       |       |       |       | if    |
|       |       |       |       |       |       |       | blank |
|       |       |       |       |       |       |       | then  |
|       |       |       |       |       |       |       | en    |
|       |       |       |       |       |       |       | error |
+-------+-------+-------+-------+-------+-------+-------+-------+

* If the forward imputation link, backward imputation link and
    construction imputation link columns are provided then the links
    should be supplied for all periods and observations. Any blanks in
    these columns will be defaulted to 1.

### Output

+-------+-------+-------+-------+-------+-------+-------+-------+
| Var   | Type  | F     | Exp   | Me    | Exp   | Freq  | Com   |
| iable | of    | ormat | ected | aning | ected | uency | ments |
| defin | var   | of    | range | of    | level |       |       |
| ition | iable | spe   | of    | the   | of    |       |       |
|       |       | cific | the   | v     | a     |       |       |
|       |       | var   | v     | alues | ggreg |       |       |
|       |       | iable | alues |       | ation |       |       |
|       |       | (if   |       |       |       |       |       |
|       |       | a     |       |       |       |       |       |
|       |       | pplic |       |       |       |       |       |
|       |       | able) |       |       |       |       |       |
+=======+=======+=======+=======+=======+=======+=======+=======+
| U     | Any   |       | \"F   | \"    | Repo  |       | Con   |
| nique | Any   |       | IR\", | FIR\" | rting |       | tains |
| ident | Date  |       | \"F   | =     | unit  |       | ret   |
| ifier | Nu    |       | IC\", | for   | level |       | urned |
| Imput | meric |       | \"FI  | wards | Repo  |       | and   |
| ation | Char  |       | MN\", | im    | rting |       | im    |
| class | acter |       | \"FI  | puted | unit  |       | puted |
| P     |       |       | MD\", | value | level |       | v     |
| eriod | Nu    |       | \     | from  |       |       | alues |
| O     | meric |       | "BI\" | a     | Repo  |       |       |
| utput | Nu    |       | ,     | ret   | rting |       |       |
| var   | meric |       | \     | urned | unit  |       |       |
| iable | Nu    |       | "C\", | value | level |       |       |
| Imput | meric |       | \     |       | Repo  |       |       |
| ation |       |       | "R\", | \"    | rting |       |       |
| m     |       |       | \"M   | FIC\" | unit  |       |       |
| arker |       |       | NI\", | =     | level |       |       |
|       |       |       | \"    | for   | Repo  |       |       |
| For   |       |       | MDI\" | wards | rting |       |       |
| wards |       |       |       | im    | unit  |       |       |
| imput |       |       |       | puted | level |       |       |
| ation |       |       |       | value |       |       |       |
| link  |       |       |       | from  |       |       |       |
| Back  |       |       |       | a     |       |       |       |
| wards |       |       |       | c     |       |       |       |
| imput |       |       |       | onstr |       |       |       |
| ation |       |       |       | ucted |       |       |       |
| link  |       |       |       | value |       |       |       |
| Co    |       |       |       |       |       |       |       |
| nstru |       |       |       | \"F   |       |       |       |
| ction |       |       |       | IMN\" |       |       |       |
| imput |       |       |       | =     |       |       |       |
| ation |       |       |       | for   |       |       |       |
| link  |       |       |       | wards |       |       |       |
|       |       |       |       | im    |       |       |       |
|       |       |       |       | puted |       |       |       |
|       |       |       |       | value |       |       |       |
|       |       |       |       | from  |       |       |       |
|       |       |       |       | a     |       |       |       |
|       |       |       |       | mean  |       |       |       |
|       |       |       |       | im    |       |       |       |
|       |       |       |       | puted |       |       |       |
|       |       |       |       | value |       |       |       |
|       |       |       |       |       |       |       |       |
|       |       |       |       | \"F   |       |       |       |
|       |       |       |       | IMD\" |       |       |       |
|       |       |       |       | =     |       |       |       |
|       |       |       |       | for   |       |       |       |
|       |       |       |       | wards |       |       |       |
|       |       |       |       | im    |       |       |       |
|       |       |       |       | puted |       |       |       |
|       |       |       |       | value |       |       |       |
|       |       |       |       | from  |       |       |       |
|       |       |       |       | a     |       |       |       |
|       |       |       |       | m     |       |       |       |
|       |       |       |       | edian |       |       |       |
|       |       |       |       | im    |       |       |       |
|       |       |       |       | puted |       |       |       |
|       |       |       |       | value |       |       |       |
|       |       |       |       |       |       |       |       |
|       |       |       |       | \     |       |       |       |
|       |       |       |       | "BI\" |       |       |       |
|       |       |       |       | =     |       |       |       |
|       |       |       |       | back  |       |       |       |
|       |       |       |       | wards |       |       |       |
|       |       |       |       | im    |       |       |       |
|       |       |       |       | puted |       |       |       |
|       |       |       |       | value |       |       |       |
|       |       |       |       |       |       |       |       |
|       |       |       |       | \"C\" |       |       |       |
|       |       |       |       | =     |       |       |       |
|       |       |       |       | c     |       |       |       |
|       |       |       |       | onstr |       |       |       |
|       |       |       |       | ucted |       |       |       |
|       |       |       |       | value |       |       |       |
|       |       |       |       |       |       |       |       |
|       |       |       |       | \"R\" |       |       |       |
|       |       |       |       | =     |       |       |       |
|       |       |       |       | ret   |       |       |       |
|       |       |       |       | urned |       |       |       |
|       |       |       |       | value |       |       |       |
|       |       |       |       |       |       |       |       |
|       |       |       |       | \"    |       |       |       |
|       |       |       |       | MNI\" |       |       |       |
|       |       |       |       | =     |       |       |       |
|       |       |       |       | mean  |       |       |       |
|       |       |       |       | im    |       |       |       |
|       |       |       |       | puted |       |       |       |
|       |       |       |       | value |       |       |       |
|       |       |       |       |       |       |       |       |
|       |       |       |       | \"    |       |       |       |
|       |       |       |       | MDI\" |       |       |       |
|       |       |       |       | =     |       |       |       |
|       |       |       |       | m     |       |       |       |
|       |       |       |       | edian |       |       |       |
|       |       |       |       | im    |       |       |       |
|       |       |       |       | puted |       |       |       |
|       |       |       |       | value |       |       |       |
+-------+-------+-------+-------+-------+-------+-------+-------+

### Description

Ratio of means imputation is a standard imputation method for business
surveys. The standard method, as outlined in examples 1 to 3 below, does not
use any form of trimming for outliers. The method is fairly robust to
outliers, unless they are exceptional and they dominate an imputation class.
In addition, the standard method does not incorporate any weights **except**
when averaging imputation links from more than one period (see
\'Exceptions\' below)

## Method Specification

### Summary

Ratio of means is an imputation method for a single numeric variable. It
uses the relationship between the variable being imputed and an
auxiliary variable. Typically, the auxiliary variable can be:

* a previous value, from the variable of interest for the
    non-responder, if it is available (see forward imputation - example 1 below)
* a consecutive value, from the variable of interest for the
    non-responder, if it is available (see backward imputation -
    example 2 below)
* a known register-based variable for the non-responder that is well
    correlated with the variable of interest (e.g. frozen turnover or
    frozen employment from the IDBR - see example 3 below)

As an example, for a unit $i$ at time $t$ which has a missing value for
the variable of interest $y$ but an available value for the auxiliary
variable $x$, the imputed value for unit $i$ is given by:

$$y_{i,t}^{*} = \frac{\sum_{j\in{impclass}}{y_{j,t}}}{\sum_{j\in{impclass}}{x_{j,t}}}x_{i,t}$$

Where $impclass$ is the set of units in a pre-defined imputation class
with responses for both $y_{j,t}$ and $x_{j,t}$. The method is called
ratio of means but it is more commonly expressed as a ratio of sums as
there is the same number of values in both the numerator and denominator
of the fraction. NOTE: this is because the ratio uses matched pairs of
respondents i.e. they must be respondents in both time periods when
using non-register based data to calculate the ratio or the imputation
link shown below.

The fraction
$\frac{\sum_{j\in{impclass}}{y_{j,t}}}{\sum_{j\in{impclass}}{x_{j,t}}}$
is called the **imputation link**.

While the imputation link calculation is based on matched pairs of
respondents, the auxiliary value that the link is then multiplied by to
create the imputed value, can be *any* status available (i.e. responded,
imputed, constructed).

It is possible to remove respondents from the imputation link
calculation by using the inclusion\_marker column. The inclusion\_marker
column is an optional input column where a user can identify respondents
that need to be excluded from the imputation link calculation. If the
inclusion\_marker column is used then the values will need to have the
Boolean type (True/False). The respondents that are included in the
imputation link calculations will need to be marked as True and
respondents that are excluded from the imputation link calculations will
need to be marked as False. If a given respondent is marked as False
under the inclusion marker, this means they will be removed from all
possible imputation links (i.e. forwards imputation, backwards
imputation, construction imputation). By definition this marker is
applied to all respondents and therefore are not imputed for; this is
purely to control for their contribution towards imputation link
calculations.

**Please note alternative notation of these formulae e.g. de Waal
et al (2011) pp244.**

### Assumptions

1. The auxiliary variable should be a good predictor of the variable of
    interest.
2. The auxiliary variable must be available for the non-respondent.

### Standard method examples

All the examples below relate to an imputation method that applies Ratio
of Means imputation to a dataframe to predict missing values.

Terminology

the below terms can be used interchangeably

* **link** = a ratio.
* **variable of interest** = target variable

### Example 1 - Forward Imputation

The most common scenario is where the current period\'s (*t*) missing
value is imputed using a previous period\'s (*t-1*) value as the
auxiliary. Only records that:

* are actual returns free of error, i.e. have been cleaned and those
    that have not been imputed by either the forward, backward or
    constructed computations in these examples.
* have responded in both periods
* marked as True if the inclusion\_marker column is an input

are used to calculate the link below.

#### Forwards Imputation Link formula

$$\text{Forward link} = \frac{\text{sum(variable of interest within strata and current period)}}{\text{sum(variable of interest within strata and previous period)}}$$

#### Forwards Imputation formula

$$\text{forward link} * \text{variable of interest that relates to the non-respondent from a previous period}$$

*the variable of interest that relates to the non-respondent from
the relative period can be any status (i.e. response, imputed,
constructed).*

### Example 2 - Backwards Imputation

In this example the method imputes a value backwards using a value from
a consecutive period (*t+1*) to the period of interest (*t*). Only
records that:

* are actual returns free of error, i.e. have been cleaned and those
     that have not been imputed by either the forward, backward or
     constructed computations in these examples.
* have responded in both periods
* marked as True if the inclusion\_marker column is an input

are used to calculate the link below. In addition, backward imputation
is only applied to records that have been imputed with a constructed
value or that were forward imputed from a constructed value.

#### Backwards Imputation Link formula

$$\text{Backward link} = \frac{\text{sum(variable of interest within strata and period of interest)}}{\text{sum(variable of interest within strata and consecutive period)}}$$

#### Backwards Imputation formula

$$\text{backward link} * \text{variable of interest that relates to the non-respondent from a consecutive period}$$

**the variable of interest that relates to the non-respondent from
the relative period can be any status (i.e. response, imputed,
constructed).**

### Example 3 - Construction Imputation

In this example, the method constructs the value to be imputed using an
auxiliary variable for a non-responding business where no previous
period auxiliary is available; for example, where a business never
responds, or is brought into the sample for the first time, or brought
back in after a break. Typically, in this case, the auxiliary variable
is a known register-based variable that is well correlated with the
variable of interest, such as frozen turnover or employment from the
business register (IDBR). If the inclusion\_marker column is an input
then only respondents marked as True will be included in the link
calculations.

#### Construction Link formula

$$\text{Construction link} = \frac{\text{sum(variable of interest within strata and current period)}}{\text{sum(aux variable within strata and current period)}}$$

#### Construction Imputation formula

$$\text{Construction link} * \text{aux variable value for non-respondent from current period}.$$

##### Exceptions

Please note the following exceptions to the method\'s standard
behaviour.
1. In some cases it may be appropriate to use an imputation link which
    is an average of imputation links for more than one.

In the simplest case this could be the average of two links. Two further
parameters would need to be specified: the lag ($k$) and the weight
($w$) given to each period. In this case the imputation link is
calculated as:

$$\text{imputation link} = w * \frac{\sum_{j\in{impclass}}{y_{j,t}}}{\sum_{j\in{impclass}}{x_{j,t}}} + (1 - w)*\frac{\sum_{j\in{impclass}}{y_{j,t-k}}}{\sum_{j\in{impclass}}{x_{j,t-k}}}$$

This could be generalised further to allow more than two links to be
included in the average.

2. In some instances, a user may want to:

    * specify that the imputed value for the given target variable is
        constructed using links that have been calculated for another
        variable named by the user within a corresponding imputation class
    * specify that a variable is backwards or forwards imputed using links
        that have been calculated for another variable named by the user
        within a corresponding imputation class or use a link of 1.

##### Imputation rules

1. Forward impute if no response available for current period but is
    available from a previous period
2. Rolling Forward impute if no response available for few rolling
    periods but is available from an earlier period
3. Forwards imputation based on a previous constructed value if
    business never responds but auxiliary data is available for that
    business
4. If a business is rotated out of the sample and then rotated back
    into the sample, values that were previously imputed (see examples 1
    to 3 above) should not be used
5. Use a returned survey response where available
6. Mean or median imputation should be used if there is no auxiliary
    information available (no previous period auxiliary, no consecutive
    period auxiliary and no register-based auxiliary variable)
7. Backwards imputation only applies to records that are constructed or
    are based on a constructed value; records imputed using mean
    imputation or based on a mean imputed value or records imputed using
    median imputation or based on a median imputed value

Mean imputation: impute a mean value for non-responders based upon
current period respondents within the same imputation class

Median imputation: impute a median value for non-responders based upon
current period respondents within the same imputation class

### Treatment of special cases

If a value of $y_{i,t}$ is missing then it is imputed in the following
way:

1. If a unit never responds, then their first value will be constructed
    and subsequent values will be forward imputed.
2. When a non-responder responds for the first time, at time
    $t = T > 1$ then the values for $t = 1, …, T-1$ are backwards
    imputed. If it is a non-responder in future periods, then these
    values will be forward imputed from a returned value or a forward
    imputed value if more than one consecutive period is missing.
3. A unit responds $t = 1$ but does not respond at a time $t = T > 1$.
    This value is forward imputed either from a returned value or a
    forward imputed value if more than one consecutive period is
    missing.

## References

**De Waal, T., Pannekoek, J. and Scholtus, S.** (2011) Handbook of Data
Editing and Imputation. New York: Wiley and Sons.

## Links
[Editing.and.Imputation.expert.group\@ons.gov.uk]: editing.and.imputation.expert.group@ons.gov.uk

