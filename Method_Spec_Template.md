# <Method Name> Specification

## 1.0 Meta

* Support area – <Who it helps, and what it helps to achieve>
* Method theme – <What does the method do>
* Status – <Is the Method fully released or still a WIP>

## 2.0 Terminology

< This Section must contain:
A List of the different data encountered in this method, with an explanation of what it is
>

## 3.0 Introduction

< This section must contain:

An Introduction to the methiod, describing the purpose of the method and an example of 
the sort of data it could be used on, the primary business use-case of the method,
any other potential use-cases as well as any limitations of the method >

## 4.0 Assumptions

<This section must contain:
Any requirements of the data that would be required for the method to work,
e.g an integer value may be assumed to be a positive integer >

## 5.0 Method Input and Output

### 5.1 Input Records
<This Section must contain:

A List of the input data required to run the method, including any types that are specified 
with examples where needed, e.g a type of any may want an example of what may be used within 
the field >

Additionally include a list of any optional values or "variable" values, (e.g if one of two columns
must be populated, identify which columns these are).

Finally, add in anything else note-worthy about the inputs (e.g if an input is a "switch" value, where the
input should be 0, 1, or 2 for instance). >
### 5.2 Output Records

<This Section must contain:

Similar to the previous section, A list of output data created by the method, including any
types and where relevant examples.

Again, anything note-worthy about specific outputs should be noted, (e.g if an output provides a character
output that specifies what the method did, if it successfully completed or not, or if it completed, if it amended data)

## 6.0 Overall Method

<This method must contain:

An overall description of what the method does from start to finish, how it interacts with the
data presented to it, and an explanation of what different outputs it may produce, depending on 
what is triggered within the method,

Additionally, you may want to go into further detail of the rationale of the method, e.g on an editing method
you may want to explain why you would be making automatic corrections to data, and what are the boundaries for
doing this sort of correction
 
>

### 6.x any other specifics

<This section and following subsections would contain: 

Any specifics of the method that warrants its own section, e.g what a method does in zero cases if
there is any specific behaviour, or when a method applies error correction if it does so, or how 
the method handles exceptions when they occur.
>

## 7.0 Calculations

<This section must contain:
subsections containing any calculations that the method does, these calculations should be written
in both mathjax and asciimath, see the below from totals_and_components as an example >
### 7.1 Error Detection Calculations

For all error detection approaches as described in section 6, the
 method initially identifies contributors where the following is
 not satisfied for *n* components at time *t*:

$$ \large y_{1, t} + \dots + y_{n, t} = y_{total, t} $$

```asciimath
y_{1, t} + ... + y_{n, t} = y_{total, t}
```

If the above is satisfied, then no error is detected, and the method stops.
Else, the error detection process will proceed depending on the chosen approach.