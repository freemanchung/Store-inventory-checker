This Java project is used by three stores: WordWorld, PawPantry and Greengrocer. These stores each have their unique products, discounts and codes. This program takes the **store name**, **balance** , a list of **store products** and a list of **orders**. It processes each order and outputs the updated records of the store. 

The input format is: ``` #Store name
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
...```
