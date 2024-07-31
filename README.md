# ecollapse
Extended Stata collapse with string functions

## Setup

```stata
ssc install ecollapse, replace
```

## Syntax 

```stata
ecollapse ... [(union) varlist (concat) varlist], by(varlist) [delim(string) sorted(varlist)]
```
where "..." is any other argument of baseline **collapse**.

## Description

This package extends base Stata **collapse** to summarize string variables by group. Two methods are supported so far:

+ **(concat)**. Observations of *varlist* within the same *by* level are stacked together. The user can set the concatenation order with sorted() option.
+ **(union)**. Distinct values of *varlist* within the same *by* level are stacked together. This collapse option is optimized via bitmasking. As a result, this method is only feasible for string variables with at most 25 different values.

Any other argument of **collapse** (e.g. **(max)**, **(mean)**, **(firstnm)**, ...) can be specified within **ecollapse**. Differently from traditional **collapse**, variable labels are preserved after collapsing.

Here's a visual example:

```
    +----------------------+
    | groupvar   A   B   C |
    |----------------------|    ->              +----------------------------+
    |        1   A   A   9 |    ecollapse       | groupvar       A     B   C |
    |        1   B   B   4 |    (concat) A      |----------------------------|
    |        1   A   A   7 |    (union)  B      |        1   A,B,A   A,B   9 |
    |        2   B   B   9 |    (max)    C      |        2   B,A,B   A,B   9 |
    |        2   A   A   7 |    , by(groupvar)  +----------------------------+
    |        2   B   B   9 |    ->
    +----------------------+
```
## Options

+ delim(*string*): delimiter to be used in concatenations (default = ",").
+ sorted(*varlist*): sort by *varlist* within *by* before collapsing.

## Examples

```stata
clear
set seed 0
set obs 100
gen groupvar = mod(_n-1, 5) + 1
gen numvar = uniform()
gen strvar1 = char(floor(65 + uniform() * 5))
gen strvar2 = strvar1
sort groupvar numvar
drop in 1/15
ecollapse (concat) strvar1 (union) strvar2 (mean) numvar, by(groupvar) sorted(strvar1)
```