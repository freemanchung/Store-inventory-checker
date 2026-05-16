# PawPantry

## Overview

PawPantry is a pet shop. Its Cygnus configuration follows the same
general file format as WordWorld (see the Assignment 3 warmup documents).

## Store identification

The first non‑blank, non‑comment record must be `STORE|PawPantry`.

## Product catalog

Each `PRODUCT` record has fields `id|name|price|type|code|stock`, with
the interpretation of `price` and `stock` similar to that for `WordWorld`.

Other conventions:

* **id**: identifier starting with an uppercase letter; unique.
* **type**: one of `food`, `toy`, `accessory`, `grooming`.
* **price** must be positive; **stock** must be nonnegative.
* **code**: `_` or an identifier made up of the following letters, in
  alphabetical order without repetition:
  * `r`: restricted from customer discount codes.
  * `b`, `k`, `s`, `z`: custom promotion codes which will be provided by the client. 

Inputs containing products that mention other product types or promotion codes should result in an error.


## Orders

Each `ORDER` block carries:

* `ITEM` records with fields `productId|qty` where `qty` is the positive quantity ordered.
* Zero or more `DISCOUNT` records carrying:
  * `LOYALTY`
  * `VET`
  The exact discount percentages for these discount codes are specified by the client.

Inputs containing discount codes that are not listed above should result in an error.


## Acceptance

An order is **accepted** if for every item, the current stock is at least the ordered quantity.

The client will also provide some store-specific acceptance criteria which you should implement.

Otherwise the order is rejected. As in WordWorld, inventory seen by an
order reflects earlier accepted orders in the same run.

## Pricing

Pricing is computed against the current inventory regardless of
acceptance, according to the general algorithm defined in GeneralRequirements.md.

The client will specify some additional promotional codes/offers.  The kinds of discounts offered by the PetStore
work in a similar way to those for BookStores, but the details (product types, numbers, percentages) will generally
be different.

## Inventory and balance updates

When an order is accepted, subtract each item's `qty` from the
corresponding product's `stock`, and add the final price to the store balance.

## Output

As per the warmup documents. The output `DISCOUNT` record, when present, carries the chosen pricing discount code.

## Example

The following is a typical input for this store. We do not give a sample output, because the correct results may depend 
on the discount percentages, acceptance rules, and pricing rules specific to your assignment.

### Input
~~~
STORE|PawPantry

INVENTORY|200
PRODUCT|F001|Kibble|13|food|r|5
PRODUCT|F002|Salmon_Treats|8|food|_|1
PRODUCT|T001|Rope_Toy|6|toy|b|10
PRODUCT|T002|Squeaky_Ball|4|toy|b|8
PRODUCT|A001|Collar|12|accessory|_|5
ENDINVENTORY

ORDER|1
ITEM|F001|1
ITEM|T001|1
ITEM|T002|1
DISCOUNT|LOYALTY
ENDORDER

ORDER|2
ITEM|F002|2
ENDORDER


### Expected output
STORE|PawPantry

ORDER|1|accepted|23
ITEM|F001|1
ITEM|T001|1
ITEM|T002|1
ENDORDER

ORDER|2|rejected|16
ITEM|F002|2
ENDORDER

INVENTORY|223
PRODUCT|A001|Collar|12|accessory|_|5
PRODUCT|F001|Kibble|13|food|r|4
PRODUCT|F002|Salmon_Treats|8|food|_|1
PRODUCT|T001|Rope_Toy|6|toy|b|9
PRODUCT|T002|Squeaky_Ball|4|toy|b|7
ENDINVENTORY
~~~

