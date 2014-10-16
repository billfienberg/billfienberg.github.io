---
layout: post
title: "DRYing Up Your Code: Using the JavaScript Call Function to Chain Constructors for an Object"
date: 2014-10-16 02:15:19 -0500
comments: true
categories: 

---
I wasn't satisfied with my understanding of JavaScript's [call()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) function. So I dug through the docs and expanded upon one of the examples.


Below is a Product constructor with one property (price) that is used as the prototype for the Food and Toy function, but **does not use the call() function**.

  * 26 lines total
  * Lines 2, 12 and 19 are identical

``` javascript Constructor with One Property (price) - Without call()
function Product(price) {
  this.price = price;

  if (price < 0) {
    throw RangeError('Cannot create product ' + this.name + ' with a negative price');
  }

  return this;
}

function Food(price) {
  this.price = price;
  this.category = 'food';
} 

Food.prototype = Object.create(Product.prototype);

function Toy(price) {
  this.price = price;
  this.category = 'toy';
}

Toy.prototype = Object.create(Product.prototype);

var cheese = new Food(5);
var fun = new Toy(40);
```

Next is a Product constructor with the same property (price) that is used as the prototype for the Food and Toy function, but **does use the call() function**.

  * Also 26 lines
  * Lines 12 & 19 are identical

``` javascript Constructor with One Property (price) - With call()
function Product(price) {
  this.price = price;

  if (price < 0) {
    throw RangeError('Cannot create product ' + this.name + ' with a negative price');
  }

  return this;
}

function Food(price) {
  Product.call(this, price);
  this.category = 'food';
}

Food.prototype = Object.create(Product.prototype);

function Toy(price) {
  Product.call(this, price);
  this.category = 'toy';
}

Toy.prototype = Object.create(Product.prototype);

var cheese = new Food(5);
var fun = new Toy(40);
```

However, when you start adding more properties to the Product, the code becomes less dry.

In the next example, I added one additional property (name) to the Product, and once again I **did not use call()**. 

  * 29 lines total
  * Lines 2, 13 and 21 are identical
  * Lines 3, 14 and 22 are identical

``` javascript Constructor with Two Properties (price and name) - Without call()
function Product(name, price) {
  this.name = name;
  this.price = price;

  if (price < 0) {
    throw RangeError('Cannot create product ' + this.name + ' with a negative price');
  }

  return this;
}

function Food(name, price) {
  this.name = name;
  this.price = price;
  this.category = 'food';
} 

Food.prototype = Object.create(Product.prototype);

function Toy(name, price) {
  this.name = name;
  this.price = price;
  this.category = 'toy';
}

Toy.prototype = Object.create(Product.prototype);

var cheese = new Food('feta', 5);
var fun = new Toy('robot', 40);
```

This time I kept the name property, but I **did use call()**.

  * 27 lines total
  * Only lines 13 and 20 are identical

``` javascript Constructor with Two Properties (price and name) - With call()
function Product(name, price) {
  this.name = name;
  this.price = price;

  if (price < 0) {
    throw RangeError('Cannot create product ' + this.name + ' with a negative price');
  }

  return this;
}

function Food(name, price) {
  Product.call(this, name, price);
  this.category = 'food';
}

Food.prototype = Object.create(Product.prototype);

function Toy(name, price) {
  Product.call(this, name, price);
  this.category = 'toy';
}

Toy.prototype = Object.create(Product.prototype);

var cheese = new Food('feta', 5);
var fun = new Toy('robot', 40);
```

In the next examples, I added a third property (origin), but **did not use call()**.

  * 32 lines total
  * Lines 2, 14 and 23 are identical
  * Lines 3, 15 and 24 are identical
  * Lines 4, 16 and 25 are identical

``` javascript Constructor with Three Properties (price, name and origin) - Without call()
function Product(name, price, origin) {
  this.name = name;
  this.price = price;
  this.origin = origin;

  if (price < 0) {
    throw RangeError('Cannot create product ' + this.name + ' with a negative price');
  }

  return this;
}

function Food(name, price, origin) {
  this.name = name;
  this.price = price;
  this.origin = origin;
  this.category = 'food';
} 

Food.prototype = Object.create(Product.prototype);

function Toy(name, price) {
  this.name = name;
  this.price = price;
  this.origin = origin;
  this.category = 'toy';
}

Toy.prototype = Object.create(Product.prototype);

var cheese = new Food('feta', 5, 'Europe');
var fun = new Toy('robot', 40, 'China');
```

In the final example, I included the origin property, but I **did use call()**.

  * 28 lines total
  * Only lines 14 and 21 are identical

``` javascript Constructor with Three Properties (price, name and origin) - With call()
function Product(name, price, origin) {
  this.name = name;
  this.price = price;
  this.origin = origin;

  if (price < 0) {
    throw RangeError('Cannot create product ' + this.name + ' with a negative price');
  }

  return this;
}

function Food(name, price, origin) {
  Product.call(this, name, price, origin);
  this.category = 'food';
}

Food.prototype = Object.create(Product.prototype);

function Toy(name, price, origin) {
  Product.call(this, name, price);
  this.category = 'toy';
}

Toy.prototype = Object.create(Product.prototype);

var cheese = new Food('feta', 5, 'Europe');
var fun = new Toy('robot', 40, 'China');
```

The following table illustrates how using call() can DRY up your code. 
<br>
<table>
  <thead>
    <tr>
      <th style="border:1px solid black; width: 20%; text-align: center; font-weight: bold;">Condition</th>
      <th style="border:1px solid black; width: 20%; text-align: center; font-weight: bold;"># of Properties</th>
      <th style="border:1px solid black; width: 20%; text-align: center; font-weight: bold;">Total Lines</th>
      <th style="border:1px solid black; width: 20%; text-align: center; font-weight: bold;"># of Identical Lines</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border:1px solid black; width: 20%; text-align: center;">Without call()</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">1</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">26</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">3</td>
    </tr>
    <tr>
      <td style="border:1px solid black; width: 20%; text-align: center;">Without call()</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">2</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">29</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">6</td>
    </tr>
    <tr>
      <td style="border:1px solid black; width: 20%; text-align: center;">Without call()</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">3</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">32</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">9</td>
    </tr>
    <tr>
      <td style="border:1px solid black; width: 20%; text-align: center;">With call()</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">1</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">26</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">2</td>
    </tr>
    <tr>
      <td style="border:1px solid black; width: 20%; text-align: center;">With call()</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">2</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">27</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">2</td>
    </tr>
    <tr>
      <td style="border:1px solid black; width: 20%; text-align: center;">With call()</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">3</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">28</td>
      <td style="border:1px solid black; width: 20%; text-align: center;">2</td>
    </tr>
  </tbody>
</table>

<br>
By not using call(), it's clear to see that the code becomes less DRY as the number of properties and/or the number of functions that inherit from the constructor increases. 

If you found this blog helpful, please follow [@BillFienberg](https://twitter.com/BillFienberg) on Twitter.