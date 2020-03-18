# GlobInv | Product Management System
GlobInv is a product management system which manages different categories of products along with stock-tracking, filtering and billings. A REST API driven system built on `node js` with `swagger` documentation.

## Overview 

- [Solution Framing](#solution-framing)
- [System Features](#system-features)
- [Resources](#resources)
- [Endpoints](#endpoints)
- [Quick Start](#quick-start)
- [Client SDK](#client-sdk)

## Solution Framing
### The Problems
  1. `Problem 1`: **How can we manage different categories of product?**<br/>
    `Scenario`: Suppose we have two categories as ``Men's Shirts `` and ``Men's Shoes``, any product under these categories will have some properties say ``material``, ``size``, ``type``, ``color``, etc. Moreover, these properties will vary with category say for example in Men's Shirts category will have material like Cotton, Denim, Silk, etc. whereas in the case of Men's Shoes will have material as Canvas, Leather, Rubber, etc. Similarly, other properties will vary with the category.
  2. `Problem 2` : **Managing consistency among the products**<br/>
    `Scenario`: Suppose we have two products under Men's shirts of different brands. The size of these products labeled as "2XL" and "XXL".  As we can see the significance is the same, but the problem is consistency.
  3. `Problem 3` : Grouping Categories
### Solution for the above problems: Step-By-Step
  1. `Solution Iteration-1`: According to `Problem-1` it can inferred that different category will have different properites. Let's call these properties as `Category-specfic` properites. To incorporate these properties we introduce `productProps` under category schema.  Thus schema of category will look like:
  ```javascript
category = {
  ...otherProps
   productProps: [...listOfProperties ]
}
```
 2. `Solution Iteration-2`: From `Problem-2 Consistency Problem`. The solution will be to pre-define some set of values for each property and `enforce` the products of this category to have these as attributes as property and the value of the attribute will be from the set of pre-defined values(thus acting as options to choose). Incorporating  this in the category schema: 
 ```javascript
category = {
  ...otherProps
   productProps: [ { 
                    key: prop1 , //Example: material
                    values: [...listOfPreDefinedValues] // ["Cotton","Denim","Silk"] 
                   },{ 
                    key: prop2 ,  //Example: standardSize
                    values: [...listOfPreDefinedValues] // ["2XL", "XL", "L", "M","S"]
                   },{
                    key: prop3  
                    values: null //Allowing Custom value
                   }
                 ]
    }
```
 3. `Solution Iteration-3`: Using `Hierarchical approach` allowing a category to have multiple child categories. Incorporating this in the category schema as ``parentCategoryId``. For parent category ``parentCategoryId`` is ``null``.
 
  ```javascript
category = {
  ...otherProps
   parentCategoryId: categoryId // null in case of parent category
   productProps: [ { 
                    key: prop1, 
                    values: [...listOfPreDefinedValues] 
                   },{ 
                    key: prop2, 
                    values: [...listOfPreDefinedValues] 
                   },{
                    key: prop3
                    values: null //Allowing Custom value
                   }]
    }
```
## System Features
+  A system that can manage ``Different`` types of products. 
+ ``Hierarchical`` based categorical grouping.
+ ``Category-filter`` to ease the search of products.
+  Managing different ``Brands``.
+  Managing different ``Suppliers``.
+  Tracking Products through ``Reorder Points `` and generating pre-order notification for out-of-stocks products.
+  Complete ``Billing`` facility, Invoice generation with detailed ``Price Break Down`` [Discounts, tax(GST)].
+  Managing ``Offers`` of different products.
+  Managing different Tax slabs as ``GST taxation`` of  ``India``.

**Note** : here managing means ``CRUD`` operations, filtering via ``Query paramaeters`` in context to API documentation.

## Resources
The resources for the system are: 
  - [Categories](#categories)
  - [Products](#products)
  - [Brands](#brands)
  - [Suppliers](#suppliers)
  - [ReorderPoints](#reorder-points)
  - [Invoices](#invoices)
  - [Offers](#offers)
  - [GSTSlabs](#gstSlabs)
  - [Auth](#auth)
  - [Users](#users)
  - [Images](#images)

### Descriptions
### Categories
  **Description**: A way of grouping similar products. <br/>
  **Schema**:         `*`: required
   |Attribute| type | Description |
   |---------|------| ----------- |
   | categoryId* | ``string``<br/> ``pattern``: *'^[A-Z]{3}[1-9][0-9]{3}$*'| Unique Identifier |
   | name* | ``string``| Name of the category |
   | gstSlabId* | ``string``<br/> ``pattern``: *'^GST[0-9]{2}$*'| tax category Id defined in GSTSlabs|
   | parentCategory* | ``string``<br/> ``pattern``: *'^[A-Z]{3}[1-9][0-9]{3}$*'<br/> `nullable`: ``true`` | #id of parent category (Hierarchical grouping). Refer [Solution Framing](#solution-framing)|
   | productProps | ``array`` <br/> items: type: ``object `` <br/> ``{ key: 'prop1', values: [...listOfPreDefinedValues ] }`` |  It represent ``category-specific`` property which helps in managing different type of products. Refer [Solution Framing](#solution-framing) |
   
 ### Products
  **Description**: items in the system. <br/>
  **Schema**:   `*`:required
   |Attribute| type | Description |
   |---------|------| ----------- |
   |productSKU* | `string` | ``SKU``: stock keeping unit as unique identifer|
   | name* | `string` | name of the product |
   | categoryId* | ``string``<br/> ``pattern``: *'^[A-Z]{3}[1-9][0-9]{3}$*' | Category the product belonging to |
   | keywords |``array`` <br/> items: ``string`` | Keywords related to product, facilitate search by keywords|
   | brandId* | ``string``<br/> ``pattern``: *'^[A-Z]{2}[0-9]{3}$*'| Brand of product |
   | price* | ``number`` <br/> minimum: 0 | Baseprice of product |
   | imageURIs | ``array`` <br/> items: ``string($uri)`` | Images as URIs of product |
   | stock* | ``integer32`` <br/> minimum: 0 | Total quantity of product in the inventory | 
   | availability* | type: string <br/> ``enum``: ``[online, offline, both]`` | Availability for the sale |
   | ratings* | ``number`` <br/> minimum: 0 | Cumulative rating of product |
   | prop1* | ``string`` | ``category-specific`` property declared in Category defination as ``key`` in ``productProps``. Its value will, be from the pre-defined ``values`` in ``productProps``. ``Example: material: "Denim" ``. Refer [Solution Framing](#solution-framing) for Logic description of using this.|
   | prop2* | ``string`` | ``category-specific`` property declared in Category defination as ``key`` in ``productProps``. Its value will, be from the pre-defined ``values`` in ``productProps``. ``Example: standardSize: "XL" ``. Refer [Solution Framing](#solution-framing) for Logic description of using this.|
   |lastUpdated* | `date-time`| Date-time when the stock was last updated. |
   | additionalProp1 | `string` | A ``product-specific`` property.|
   | additionalProp2 | `string` | A ``product-specific`` property.|
   
  ### Brands
  **Description**: Companies manufacturing products. <br/>
  **Schema**:   `*`:required
 |Attribute| type | Description |
 |---------|------| ----------- |
 | brandId* | ``string``<br/> ``pattern``: *'^[A-Z]{2}[0-9]{3}$*' | Unique Identifier |
 | name* | ``string``| Company name |
 | website | ``string($uri)`` | Website of company. | 
 | email* | ``string($email)`` | Support email from the company. |

 ### Suppliers
  **Description**: Vendors supplying the products. <br/>
  **Schema**:   `*`:required
 |Attribute| type | Description |
 |---------|------| ----------- |
 | supplierId* | ``string``<br/> ``pattern``: *'^[A-Z]{2}[0-9]{3}$*' | Unique Identifier |
 | name* | ``string``| Supplier name |
 | address | ``string($uri)`` | address of supplier. | 
 | email* | ``string($email)`` | Support email from the supplier. |
 
### ReorderPoints
  **Description**: Vendors supplying the products. <br/>
  **Schema**:   `*`:required
  

 
 
 
   
   
    
   
  



 
