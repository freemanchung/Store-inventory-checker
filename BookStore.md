# WordWorld

## Overview

WordWorld is a small bookstore. This document specifies the store‑specific
behaviour of the Cygnus program when the input begins with `STORE|WordWorld`.
The base input/output record format, normalisation rules, comment and
blank‑line handling, and general layout are defined in the Assignment 3
warmup documents (`ex01`–`ex05`) and are not repeated here.

## Store identification

The first non‑blank, non‑comment record must be `STORE|WordWorld`.

## Product catalog

Each `PRODUCT` record has fields `id|name|price|type|code|stock`.

* **id**: identifier starting with an uppercase letter; unique across the
  inventory.# Assignment 3 assessment - README first

This IntelliJ project contains the full specification for the assessed stage of Assignment 3, in which you will implement
version 2.0 of the Cygnus store manager.

There are five additional markdown files:
* GeneralRequirements.md: summarises the basic requirements for Cygnus 1.0 which were covered in the warmup.  These are
  collected in one place for ease of reference.  There are also a few important differences which are clearly marked in
  a separate section.
* BookStore.md: summarises the specific details of the book store scenario, which were also covered in the warmup, again for
  ease of reference.
* PetStore.md: summarises specific details of the pet store scenario
* Supermarket.md: summarises specific details of the supermarket scenario
* acknowledgments.md: which we ask that you complete to indicate any assistance or collaboration on this assignment (during
  the warmup phase).  This is not marked, but is helpful for us to understand how students worked during the warmup
  phase and may be helpful for designing future similar assignments.

**Important note:** Part of the specification of assignment 3 that you are to implement is provided by the automarker.  
This is because each student will be randomly assigned additional store clients, one pet store (PawPantry) and one 
supermarket (GreenGrocer), each with different requirements that are different from those assigned to any other student.

You should submit your current solution to the warmup to the assignment 3 automarker on Gradescope as soon as possible
to obtain these additional requirements.  The automarker will also provide feedback on how well your current solution meets
the general requirements specified in the warmup/GeneralRequirements.md.  The automarker will be updated later with 
additional tests that will provide feedback on your specific requirements.

If you have not worked on a solution to the warmup exercises yet, you can just submit an empty project (e.g. this one)
and you will be able to see the additional requirements.


# Submitting

Submission is through GradeScope using the Assignment 3 autograder.

You may upload a zip file created from an IntelliJ project like this one.

You can submit as many times as you want.

The autograder does two important things:

1. Tells you your additional store scenarios to implement and additional validity/cost adjustment rules.
2. Runs tests which tell you whether your solution is correct (at least on the specific tested examples)

Your eventual grade will be entirely determined by the test results.  If all tests pass, your grade for Assignment 3
will be 100%.  However, the feedback you get from the test runs does not tell you everything about the test cases or 
requirements that are being tested, and the "score" produced by the automarker is a count of the tests passed, 
which is not guaranteed to directly map to your final grade.  Passing more tests is always better, but we reserve
the right to adjust the weights of tests to reflect that some requirements are easier or harder to meet than others.

For the autograder to work properly, the following things should be true of your submission in order for it to receive
credit:

* It **MUST** have a main class called `CygnusMain` with a `public static void main(String[] args)` method.  (It is fine 
  for you to create other classes with main methods to accommodate testing or for any other reasons, but we will only
  run the `CygnusMain.main` entry point.
* When called with an empty argument list, your submission **MUST** read inputs from `System.in` and write to `System.out`
  following the specification(s).
  (It is fine to also support reading from a file whose name is provided as a command line argument for your own convenience
  in testing, but the autograder will never do this.)
* You can create whatever additional classes, interfaces and code files you want, but all .java files **MUST** be   
  immediately inside the `src/` directory of the top level directory of the zip file you submit.  Do not put your code 
  into subdirectories of `src` or in the top level.  Do not use `package` either.  
  For clarity, this means than when the zip file is unzipped, your Java files should appear at paths like this:
  ```
  Assignment3/src/CygnusMain.java
  Assignment3/src/Store.java
  ...
  ```
  You can see these paths in the submitted code in Gradescope.  If your java files are at any other path (including
  a root-level `src` directory not inside the `Assignment3` subdirectory), the automarker will not find them and you 
  will get a mysterious error message and no tests will pass.
* Your solution **MUST**  compile using Java 17 and **MUST NOT** assume any features in more recent versions of Java or rely on   
  libraries not part of the standard Java library.  We will compile only your submitted Java code in a sandboxed
  environment without any libraries you might happen to add to the IntelliJ project.
* Your solution **MUST NOT** call `System.exit` to end execution.  Because the testing framework we use loads your code 
  dynamically using reflection and calls your `main` method directly, if your code calls `System.exit` then it kills 
  the autograder too, so you won't get any feedback.  Instead, if your code encounters incorrect input, it should print
  an error message as specified in `GeneralRequirements.md`.

Please also submit a completed acknowledgments.md file with your final submission.
* **price**: positive integer pounds per unit.
* **type**: one of `book`, `children`, `stationery`, `game`.
* **code**: either `_` (no promotions apply) or an identifier made up of
  the letters below, in alphabetical order without repetition:
  * `c` — eligible for the 3‑for‑2 promotion.
  * `r` — restricted from customer discount codes.
  * `f`, `h`, `t` — reserved; no effect at present.
* **stock**: nonnegative integer unit count.

Inputs containing products that mention other product types or promotion codes should result in an error. 

## Orders

Each `ORDER` block carries:

* `ITEM` records with fields `productId|qty`, `qty` a positive integer.
* Zero or more `DISCOUNT` records carrying one of the codes:
  * `UNI` — 10% off eligible items.
  * `HEALTH` — 20% off eligible items.
  * `PROMO` — recognised but currently inactive (treated as 0%).
  * `EMPLOYEE` — recognised but currently inactive (treated as 0%).

Inputs containing discount codes that are not listed above should result in an error.

## Acceptance

An order is **accepted** if for every item, the current stock of that product is at least the 
ordered quantity.

In addition, the client will provide a few further constraints on orders which you should check too.

Otherwise it is **rejected**. The inventory seen by each order reflects
all earlier accepted orders in the same run.

## Pricing

Pricing is computed against the current inventory regardless of whether
the order is accepted, so rejected orders still report an accurate price.

**NEW:** In addition to the `c` 3 for 2 code described in the warmup (and again below), the customer has
requested two additional promotional codes be activated with additional offer types: *category discounts*
and *buy N, get percentage discount*.

### Promotional 3‑for‑2

If the total number of ordered units whose product code contains `c` is
at least 3, exactly one unit is free.  That is, the order is discounted by the
minimum price of a `c`-coded item.  
Only one unit is ever made free by this rule, no matter how many
`c`‑flagged units the order contains.

### Product type discounts

The client will provide you with a code that indicates items of a specific product type having that code should be 
discounted by a provided percentage.  To calculate the discount, we multiply the cost of each affected 
items by the percentage, round up, and sum the results (as described in `GeneralRequirements.md`).

### Buy n items, get p% discount

The client will also provide you with a code such that all items having this code (independently of category) get 
a certain percentage discount, as long as at least some given number of items having that code are purchased.  (This 
applies even if only one **kind** of product is purchased, as long as at least 3 such items are purchased.)
This works just like the category discount code but without the category constraint.  Also, recall that 
when applying percentage discounts to multiple items, we apply the discount to each item individually and round up 
to the nearest pound.


## Inventory and balance updates

When an order is accepted, for each item in the order, subtract each item's `qty` from the corresponding product's 
`stock` and add the final price to the store balance. Rejected orders leave the inventory and balance unchanged.

## Output

As per the warmup documents. The output `DISCOUNT` record for an order, when present, carries exactly the chosen
pricing discount code. If no code was needed to get the lowest price, no `DISCOUNT` record appears on that order.

## Example

The running example in `ex01-input-format.md` is a canonical worked example for
WordWorld.  However, the example output shown for this input is not necessarily the correct one for your
assignment, because the correct results may depend on the discount percentages, acceptance rules, and pricing rules
specific to your assignment.
