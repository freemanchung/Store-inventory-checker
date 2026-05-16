# General requirements

## Cygnus 1.0 recap

This section gathers requirements for Cygnus 1.0 that were presented in the warmup exercises.  Examples and additional
observations are not repeated here, so please consult the warmup exercise documents for more details.

Cygnus takes inputs consisting of store inventory and order specifications and  does three main things:
1. Read inputs possibly not in the cleanest form, and standardise them to a clean form, for example avoiding
   leading zeros, blank lines, and so on.
2. Check that the store inventory and orders are consistent, and process each order to determine whether it can be 
   fulfilled and what its total price is.
3. After each order is processed, update the store state to reflect this, and ultimately print out a record of the 
   store's activity for the day.


## Input format

Input to Cygnus is read from the standard input stream which you can access using `System.in` with the `Scanner` or
`BufferedReader` classes.  When there are no command line arguments, input should **only** be read from `System.in` and 
**only** be written to `System.out`.

Generally, Cygnus input files are sequences of records.  Each non-empty line is one record.  Fields are separated by |.
Lines starting with # are comments and all text on the remainder of that line is to be ignored.
Your program should ignore blank lines.

Formally we can describe the general structure (once comments and blank lines are removed) using a grammar as follows:
```
records ::= record records
         |  EOF
record ::= identifier values '\n'
values ::= '|' value values
        | ϵ // nothing
value ::= identifier | integer
identifier ::= [a-zA-Z_][a-zA-Z0-9_]* 
integer ::= [0-9]+ 
```

Cygnus uses a number of additional conventions to organize the data in its files, not reflected in the above
description.   The general structure of a Cygnus input file is as follows:

```
#Store name
STORE|<storename>

INVENTORY|<balance>
PRODUCT|<productId>|<name>|<price>|<type>|<code>|<stock>
...
ENDINVENTORY

# Order 1
ORDER|<orderId>
ITEM|<productId>|<quantity>
...
DISCOUNT|<discountCode>
...
ENDORDER
# Transaction 2
...
```
It is OK for there to be no orders.  
The file should not end where the `INVENTORY` record has appeared and `ENDINVENTORY` has not, and likewise should not
end in the middle of an order.

If syntactic or structural constraints are violated, then the program should terminate and print an error message
(see below).  

### Inventory and Products

The `INVENTORY` record begins the inventory and has a balance field which gives the amount of cash on hand at the beginning
of the day.

Product records have a product id, name, price, type, code, and stock (the type and code are typically used to annotate
products that have some temporary discount).  In product records, `price` is
an integer number of pounds, `name`, `type` and `code` are identifiers, and `stock` is a nonnegative integer representing
the number of items of this product in stock.

Identifiers are alphanumeric strings (using a-z, A-Z, 0-9 or underscores), and must begin with a letter or underscore.

The `ENDINVENTORY` record ends the inventory section and has no data.

### Input validation and errors

For completeness, we rephrase the validation rules already mentioned in exercise 1, which should still be checked and lead
to an error if violated.

1. The first record must be a `STORE` record and this should be the only such record.
2. There must be exactly one inventory block  `INVENTORY` with an ending `ENDINVENTORY` containing only `PRODUCT` lines
3. Each `ORDER` should have a matching `ENDORDER` line containing only `ITEM` and `DISCOUNT` lines.
4. Each line should have the expected fields described in exercise 1.  However, extra fields are
   allowed and should just be ignored.
5. Fields in a line must have the required types or forms as described in exercise 1 (prices/order IDs should be
   positive integers, quantities should be nonnegative integers, other fields should be identifiers, product
   identifiers must start with a capital letter)
6. Orders with the same order ID are not allowed.
7. Product ids appearing in an `ITEM` record must appear in an inventory entry.
8. Product ids must not appear in more than one `PRODUCT` record in the inventory.
9. Only records  `STORE`, `INVENTORY`, `ENDINVENTORY`, `ORDER`, `ENDORDER`, `ITEM` and `DISCOUNT` can be used.

Each store type will have some additional input validation rules as well.  When such a rule is violated the output
should be a single line starting with `ERROR`, followed by any more descriptive error message you wish to provide.

### Orders

The `ORDER` record begins an order, and assigns it an order id.
The order id is used to keep track of the order.  This is a positive integer and no two orders
in a given input should have the same id.  An order can contain one or more items, each including a product id
(identifier) and quantity (positive integer).  Orders might contain multiple `ITEM` records with the same
product ID and this means the same thing as if there were one `ITEM` record with the quantities combined.

`DISCOUNT` records add a possible discount to an order.  More than one `DISCOUNT` record can appear, if so, the eventual
price of the order will be reduced by applying each discount separately, and taking the minimum resulting price.  This
will be explained in more detail later, for now, all that is needed is to know that the discount codes are identifiers.

The `ITEM` and `DISCOUNT` records can be in any order without affecting the meaning of the order.  The order is terminated
by `ENDORDER`.


## Order validation

Once we have read in the store inventory and order list, we will process each order in turn to decide whether it is
valid and can be fulfilled, calculate the price of the order, apply the resulting changes to the inventory, and save
the modified order.  In this section we describe the first part: validation. An order is valid if for each product 
mentioned in it, the number requested in the order is less than or equal to the number of items currently in stock.

Additional store-specific rules constraining order validity (for example, what discount codes, promotional code 
characters, and product categories are allowed) are described in `BookStore.md`, `PetStore.md`, and 
`Supermarket.md`, and you may also be assigned some additional client-specific rules.

Once we determine whether an order is valid, we mark the order record as "accepted" or "rejected", as this eventually needs
to be printed out.

## Order pricing


We will now consider the cost of each order.  By default, the price of an order is the sum of the products of the 
prices of each item in the order and their quantity.  However, different stores have their own policies for discounting 
either based on the order content (3 for 2, meal deal, etc.), or
individual customer discount codes, or both.  

In general, stores all follow this approach:
1. The order is split into "restricted" and "unrestricted" items.  Restricted items are those having an 'r' in their 
   promotion code.  Individual discounts do not apply to restricted items.
2. Calculate the price of the unrestricted items using the best applicable individual discount code.
3. Calculate the price of the restricted items using the promotional discounting rules.  We try each rule in 
   alphabetical order of its code and use the *first* applicable rule.  For example, if the conditions for discount codes 'c'
   and 'z' are both met, only discount 'c' is actually applied.
4. Calculate the price of *all* items using the promotional discounting rules, taking the first applicable rule in 
   alphabetical order.
5. Compare the sum of the prices found in steps 2 and 3 with the total price found in step 4, and report the minimum.
6. If the minimum price is found by using steps 2 and 3, also report the applicable individual discount code used in 
   step 2, as this needs to be reported in the order.  If there is a tie, report the first such discount code in 
   alphabetical order.  (The promotional discount code used, if any, does not need to be reported.)

Note that this is a mild generalization of the pricing approach described in the warmup: it allows for multiple 
promotional code rules.  Moreover, for each store type, you will be assigned some custom promotional rules that you 
have to implement.

Once the price is calculated, we record the price of the order, and update the discount code in the order to reflect the 
code that provided the best price (if any), according to the store's pricing policy.  

### Calculating percentage discounts

When calculating percentage discounts, we apply the percentage to each applicable item individually,
round up to the next integer value, and subtract the rounded value from the original price.  For example, if there are two
items with price £9 and £20 to which a 20% discount applies, then the discount on the £9 item is £2 (since 20% of 9 is
1.8) and the discount on the £20 item is £4 (since 20% of £20 is exactly £4).  Thus the total price is (£9 - £2) + (£20-£4) = £23.


## Output format

The output format of Cygnus is similar to the input format, with the following differences:

1.  Products in the inventory should be printed out in increasing order of product id.
2.  The input allows numbers like 007 which have leading zeros, which do not affect the value of the number.  Number
    values should be printed without any leading zeros.
3.  The `STORE` record should have exactly one blank line separating it from the first `ORDER` and similarly there
    should be exactly one blank line between `ENDORDER` and the next `ORDER` or `INVENTORY` line.
4.  Orders should be printed out in the same order in which they appeared in the input, not sorted by order id.
5.  In an order, the `ITEM` records should appear first, then the `DISCOUNT` records.
6. `ITEM` records should be sorted by product id and any duplicate product id records should be merged, adding their
   quantities together.
7. `DISCOUNT` records should be sorted by discount code and if a discount appears more than once in the input it should
   only appear once in the output.


In addition, the completed orders should be printed out first, then the inventory.
The orders and inventory should be printed out as follows
reflecting the results of validation and price calculation.

* `ORDER` records have two extra fields, `status` and `price`.  The `status` is an identifier (`accepted`/`rejected`) 
  reflecting whether the order is acceptable.  The `price` is likewise the calculated price of the order (even if the 
  order is rejected).  
* In the unlikely (but possible) event that applying discounts results in an order with negative price, print 0 here instead.
* `ORDER` records in the output should have just one `DISCOUNT` showing the discount code that was actually applied to 
  get the best price. If using no `DISCOUNT` code resulted in a price equal to the best price with such a code, no 
  `DISCOUNT` line should appear.
* The `balance` in `INVENTORY` and the `stock` values in `PRODUCT` should reflect the changes resulting from all accepted 
  orders being filled.


# New for Cygnus 2.0

In Cygnus 1.0, we supported only `BookStore`s.  Your implementation should continue to support them (with any changes 
as explained below).  
You should look out for any additional requests for WordWorld leading to new requirements you need to implement.  

Despite its 1980s-vintage, purely text input and output approach, Cygnus has proven wildly popular with our 
bookstore clients.  We now want to expand from supporting book stores to other types of stores.
Cygnus 2.0 needs to support these additional kinds of stores:
* `PetStore`s: which are fairly similar to BookStores but have some different validity and discount rules. 
* `Supermarket`s: which have several different kinds of customer offers and deals.

You will be assigned one new client of each type.  These clients have come up with some additional rules
to take into account both for order validity and for calculating prices using various discount and promotional offers,
which differ from those you have already implemented for WordWorld.

Your assigned stores, and associated customized rules, are provided in teh autograder response when you submit a project 
to the autograder.  (The project does not have to contain any code, just exporting this project to a zip file and 
submitting that is fine.) 
Everyone doing this assignment will be assigned different store scenarios to implement.  
