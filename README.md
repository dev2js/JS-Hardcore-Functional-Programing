# JavaScript - Hardcore Functional Programming


## Table of Contents

  1. [Currying](#currying)
  1. [Composition](#composition)

## Currying

> What? Currying refers to the process of transforming a function with multiple arity into the same function with less arity. The curried effect is achieved by binding some of the arguments to the first function invoke, so that those values are fixed for the next invocation. It can be integrated with callbacks to create higher-order ‘factory’ functions. This pattern is extremely useful in event handling, and can also replace the callback pattern that is utilized in node.js
```javascript
    function multiply(a, b, c) {
        return a * b * c;
    }
    
    multiply(1,2,3); // => 6
```

With Currying:
    
```javascript
    function multiply(a) {
        return (b) => {
            return (c) => {
                return a * b * c
            }
        }
    }
    
    multiply(1)(2)(3) // => 6   
```
- Write little code modules that can be reused and configured with ease, much with npm.

    Taking a use case while we design a POS kind of application and write functions to apply different levels of discounts / markdowns / coupons on regular priced items. We can utilize currying as below:

```javascript
    const discount = (discountPortion) => {
        return (price) => {
          return price * discountPortion;
        };
    };

    const markdown = (markdownPortion) => {
        return (price) => {
          return price * markdownPortion;
        };
    };

    const tenPercentDiscount = discount(0.1);
    const twentyPercentDiscount = discount(0.2);
    const customMarkdown = markdown(0.35);
    const associateDiscount = discount(0.095);
    
    const laysCost = tenPercentDiscount(2.85);
    const associateTotalBillAmount = associateDiscount("TOTAL BILL AMOUNT");
    
    //Direct usage of dicount method
    const customDiscountItems = discount(0.18)("price amount");
```
* Closure makes currying possible in JavaScript. It’s ability to retain the state of functions already executed, gives us the ability to create factory🏭 functions — functions that can add a specific value to their argument.

**[⬆ back to top](#table-of-contents)**

## Composition

> What? Function composition allows to combine two or more functions into a new function. A composable function should have 1 input argument and 1 output value. We can convert any function into a composable function by currying the function. This allow us to build a higher order function with combining smaller functions by repeat their main workflow. 

    // Smaller functions
    const add10 = value => value + 10;
    const add100 = value => value + 100;

    // compose above two
    const add110 = value = add10(add100(value));
    
    const compose = (function1, function2) => argument => function2(function1(argument));
    
    // rewritting add110 with above compose which execute the function from right <= Left
    const add110 = compose(add10, add100);
    
    add110(argument); => add110(200) // 310

  - Composing Multiple Function (Arbitrary Arguments). 
  > Higer order function is useful for composing two functions only. To add more functions together we can leverage reduce. 

    const compose = (...functions) => argument => functions.reduce((a, b) => argument => b(a(argument)), i => i);
Explanation for multiple functions compose :

    const compose = (...functions) => argument => ....

Capturing the array of functions argument and return to a newer function that will take single argument. By this we donot want to compose all our functions and use at once. Capturing of functions as an argument is called Closure. 

The later part of compose is 

```javascript
i => i
//Equivalent of 
function identity(i) {
  return i;
}
```
This is for a foolproof mechanism is the array of function contains single method, for compose they require more than one. To ensure we will always return a function and the function will have minimum return argument unmodified. 

Discussing about the core part of compose, which actually composing list of functions together with javascript's [Array.prototype.reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) method.
```javascript
functions.reduce((a, b) => argument => b(a(argument))

//Equivalent of 
functions.reduce((composition, nextFunc) => {
    return argument => {
        const result = nextFunc(argument)
        return composition(result);
    }
})
```

-Debugging compose:
    We can achieve debugging compose method by introducing an logger method and adding to the list of compose methods between two execution methods. 
    
```javascript
const modifiedStr = compose([boldProperty, addQuotes, convertPath, removeVowel, upperCase]);

modififiedStr(“string convert”);


//Debugger:
const customLog = (argument) => {
	return console.log(argument);
}
 
const modifiedStrWithLogger = compose ([boldProperty, customLog, addQuotes, customLog, convertPath, customLog,  removeVowel, customLog, upperCase]);

```

**[⬆ back to top](#table-of-contents)**
