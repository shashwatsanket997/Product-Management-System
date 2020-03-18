# GlobInv | Product Management System
GlobInv is a product management system which manages different categories of products along with stock-tracking, filtering and billings. A REST API driven system built on `node js` with `swagger` documentation.

## Overview 

- [Solution Framing](#solution-framing)
- [Features](#features)
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
   productProps: [ { key: prop1 , 
                    values: [...listOfPreDefinedValues] 
                   }, { key: prop2 , 
                    values: [...listOfPreDefinedValues] 
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
   productProps: [ { key: prop1 , 
                    values: [...listOfPreDefinedValues] 
                   }, { key: prop2 , 
                    values: [...listOfPreDefinedValues] 
                   },{
                   key: prop3
                   values: null //Allowing Custom value
                   }
                 ]
    }
```
 
