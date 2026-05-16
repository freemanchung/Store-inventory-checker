# GreenGrocer

## Overview

GreenGrocer is a grocery store. Its Cygnus configuration uses the same
general file format as the warmup (see `ex01`–`ex05`) but with slightly richer
promotional, discount, and acceptance rules.

## Store identification

The first non‑blank, non‑comment record must be `STORE|GreenGrocer`.

## Product catalog

Each `PRODUCT` record has fields `id|name|price|type|code|stock`.

* **id**: identifier starting with an uppercase letter; unique.
* **price**: positive integer pounds per unit.
* **type**: one of `produce`, `dairy`, `bakery`, `pantry`, `alcohol`.
* **code**: `_` or an identifier made up of the following letters, in
  alphabetical order without repetition:
  * `r`: restricted items which are not eligible for individual discounts
  * `a`, `j`, `m`, `y`: other promotional codes that will be defined later by the client. 
* **stock**: nonnegative integer unit count.

Inputs containing products that mention other product types or promotion codes should result in an error.

## Orders

Each `ORDER` block carries:

* `ITEM` records with fields `productId|qty`, `qty` a positive integer.
* Zero or more `DISCOUNT` records, as follows
  * `LOYALTY`: for loyalty card holders
  * `STAFF`: for staff members
  The exact discount amounts for these codes will be provided by the client.

Inputs containing discount codes that are not listed above should result in an error.


## Promotional codes

In addition to the kinds of promotions seen in the BookStore and PetStore settings, the Supermarket
has a "meal deal" promotion code such that purchasing three items of different given categories having this code 
results in a fixed discount amount (not a percentage).

## Acceptance

An order is **accepted** if for every item, the current stock is at least the ordered quantity.

In addition, the client will provide some additional store-specific acceptance criteria.

Otherwise the order is rejected. Inventory visible to an order reflects
earlier accepted orders.


## Inventory and balance updates

When an order is accepted, subtract each item's `qty` from the
corresponding product's `stock` and add the final price to the store
balance.

## Output

As per the warmup documents.

## Example

The following is a typical input for this store. We do not give a sample output, because the correct results may depend
on the discount percentages, acceptance rules, and pricing rules specific to your assignment.

### Input
~~~
STORE|GreenGrocer

INVENTORY|1000
PRODUCT|P001|Apples|2|produce|a|40
PRODUCT|D001|Milk|2|dairy|a|30
PRODUCT|B001|Sourdough|3|bakery|j|15
PRODUCT|B002|Croissant|2|bakery|my|20
PRODUCT|W001|Red_Wine|12|alcohol|_|10
ENDINVENTORY

ORDER|1
ITEM|P001|2
ITEM|D001|1
ITEM|B001|1
ITEM|B002|3
DISCOUNT|LOYALTY
ENDORDER

ORDER|2
ITEM|W001|1
ENDORDER

ORDER|3
ITEM|W001|1
ENDORDER

ORDER|4
ITEM|B002|8
DISCOUNT|LOYALTY
ENDORDER
~~~
