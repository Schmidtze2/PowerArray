# PowerArray
Working with Arrays in Javascript requires manual iteration, is error-prone, difficult to read, repetitive and time-consuming. PowerArray simplifies almost everything that has to do with that bads. 

## Simple example
The following example filters an array, sort the results and iterates the first 10 matches. It uses the **Where**, **Sort**, **Take** and  **RunEach** PowerArray functions.

>Given an array of objects (representing persons), called 'peopleArray' 
```javascript
// With PowerArray:
peopleArray
    .Where({ age: Between(18,70), gender: 'M' })                                // filter
    .Sort({ lastName: Sort.AscendingIgnoringCase })                              // sort
    .Take(10)                                                                   
    .RunEach(function(item, i) { console.debug(i + ' ' + item.name); } );       // do stuff

// In plain Javascript
var result = [], i = 0, l, item;
for(l = peopleArray.length; i < l; i++) {
	item = peopleArray[i];
	if(item.age > 18 && item.age < 70 && item.gender === 'M') {             // filtering
		result.push(item);
	}
}

result.sort(function (a, b) {
    return a.lastName.toLowerCase().localeCompare(b.lastName.toLowerCase());    // sorting
});

for(i = 0, l = result.length; i < l && i < 10; i++) {
    console.debug(i + ' ' + result[i].name);                                    // do stuff
}
```

**Both codes do exactly the same**, but the first is (at least for me), much easier to understand. Power array adds also global auxiliar functions, that are ready to be embedded on [Conditions-Object](#ConditionsObjectDescription).

This library helps to simplify code, to make it intuitive and readable.

**PowerArray** extends the Array prototype by adding additional features to work with **any** array. 
 
[Worried about changes on the Array Prototype?](#ArrayPrototypeChanges)

<a name="#usage"></a>
# Usage

//TODO

PowerArray adds also some auxiliary functions to avoid writing the same snippets over and over again. All they are accesible after loading the library, [click here for a complete list](#WherePAStandardFunction).

<a name="#WhereFunction"></a>
## Filtering - The **.Where()** function
To simplify filtering tasks, PowerArray relies mainly on the **Where** function, which offers a standard mechanism to formulate filtering conditions.

> The return value of a **where** function call, ***is ALWAYS a new array***, in which each position is a reference to an item on your original array that fulfilled the conditions. The *Where* function doesn't change anything on the original array, it just creates a new array of references to all matching elements. Because it returns an array, it's also chainable with other PowerArray functions like <a href="#SortDescription">Sort</a> or <a href="#RunEachDescription">RunEach</a>.  

You can pass conditions to the *Where* function (first argument in all signatures) as functions or by using [**Conditions-objects**](#ConditionsObjectDescription)</a>. It is necessary to understand [**Conditions-objects**](#ConditionsObjectDescription) before you start using the **Where** function. 

### Signatures:
> **anyArray.Where**(conditionsObject ,*keepOrder*)
* **conditionsObject** => type `Object` (a Conditions-Object)
* **keepOrder** => type `boolean`, indicating if the original order should be kept or not. Optional, default false.

Examples:
```Javascript
    //given an Array of objects called 'peopleArray' 

    //Filtering by first level properties 'name' and 'lastname'
    var result = peopleArray.Where({ name: 'John', lastName: 'Lennon' }); 

    //Filtering by first level property 'name' and by second level property 'partner' 
    var result = peopleArray.Where({ 
        name: 'John', 
        partner: { name: 'Yoko'}  // 'partner' is an object, and it's expected to have a property name 
    }); 

    //Multiple conditions and auxiliar functions 
    var result = peopleArray.Where({ 
        age: Between( 40, 50) , 
        hobbies: Contains('Swimming'), // 'hobbies' is an array of strings. Auxiliar function 'Contains' used
        favoriteColor: In('Black','Blue') // 'favoriteColor' is a string. Auxiliar function 'In' used       
    }); 
``` 

> **.Where**([conditionsObjects] ,*keepOrder*)
* **conditionsObject** => type `Array` (of Conditions-Objects)
* **keepOrder** => type `boolean`, indicating if the original order should be kept or not. Optional, default false.

This signature accepts an array of Conditions-Objects. If an item fulfill all conditions of at least one 
Condition-Object, it will be included in the results (and other Condition-Objects will not be evaluated for that item). 
This Signature is ideal to build (a kind of) `OR` statements that cannot be expressed in a single Conditions-Object. 
Examples:

```Javascript
    // Given an Array of objects representing persons, called 'peopleArray' 
    // Task: find all single men between 20 and 30 years old and all married woman between 25 and 35 years old   
    var result = peopleArray.Where([
        { gender : 'M', age : Between(20,30), status: 'Single' }, // Auxiliar function 'Between' used
        { gender : 'W', age : Between(25,35), status: 'Married' } 
    ]); 
    
    // Given an array of orders called 'ordersArray'
    // Task: find all invoices "from location "Sydney" having an amount <= 1000" or "having amount > 50000, regardless location"
    var result = ordersArray.Where(
        { amount : SmallerOrEqualThan(1000), location : 'Sydney' }, // Auxiliar function 'SmallerOrEqualThan' used
        { amoung : GreatherThan(50000) } // Auxiliar function 'GreatherThan' used
    );

    // In this two exmaples, we have multiple criterions that overlap each oder (age on the first example, amount on the second). 
    // This overlap makes impossible to formulate both criteria on a single Conditions-Object, and that's why it's neccesary to use this signature in such cases.
``` 

> **.Where**(func ,*keepOrder*)
* **func** type Function.  
* **keepOrder** type boolean, indicating if the original order should be kept or not. Optional, default false.

This signature can be used when filtering arrays of any type. Pass a function which will receive the item to evaluate,
the index of that item on the original array, and the array self as parameters in that order. 

Examples:
```Javascript
    //Filtering arrays of primitives
    [1, 2, 3, 4, 5, 6, 7].Where(BiggerThan(4))); 
    // returns [5,6,7]

    ["Red","Green", "Black", "Blue"].Where(Like('e')); //using auxiliary function Like
    //returns ["Blue", "Green", "Red"]

    // Given an Array of objects representing persons, called 'peopleArray'  
    var somePeople = peopleArray.Where(function (person, index, array) {
        //parameter person => an item of people array 
        //parameter Index => the index of the current person item in the original array
        //array => a reference to the array we are filtering (array[index] is always equal to person)
        if(/*conditions*/) 
            return true; //returning true (or any truthy value), the item will be included on the result.
        return false; //return false to exclude it.
    });
    
``` 

<a name="ConditionsObjectDescription"></a>
### What is a **Conditions-Object**?
It's a plain Javscript object containing N filtering criteria, that can be used to filter objects arrays. Each property present on a given Conditions-Object, 
represents a filtering criteria for a property that should be present on each array item in which the filtering is applied.

In the following code:
```Javascript
var result = peopleArray.Where({favoriteColor: 'Red'});
```
the **Conditions-Object** is `{favoriteColor: 'Red'}`. It's expected that peopleArray be an array of objects, each having a property called 'favoriteColor'.

Within a single Conditions-Object, you can can also target items sub-properties if they have (mostly on objects-arrays), for Example:
```Javascript
var result = peopleArray.Where({ //peopleArray is an array of objects representing persons
    favoriteColor: 'Red', //each person has a first-level String property called "FavoriteColor". We pass the value we are searching for as explicit primitive
    Childs: { // to another first-level property called "childs", which contains an array of objects, we pass a new Contdition-object.
        name: In('John','Juan', 'Joan') //on the new condition object, we can filter by property name. In this case by using the auxiliar function 'In'
    }
});
```

<a name="ArrayPrototypeChanges"></a>
### What does this script changes on the Array prototype? 

It just adds new functions, and only if the desired names (or pointers) are not already taken.

#### how does it works:
Basically, PowerArray loads everything he needs to work on his own global object called "pa", as many frameworks do. The "pa" object is a container, 
in which there are multiple functions. Some of this functions, are designed to work with any object with having Array as prototype or (array like object) object, and others 
designed to operate globally (defined at pa.auxiliaryFunctions). 
During the initialization process, each of such functions is is evaluated, to check if the name is already in use before modifying anything. 
Only if they are free, a pointer to the corresponding pa function is set on the prototype array, or the global scope. 
If any name is already occupated by other framework, library or whatever, PowerArray don't change anything, just sends a warning to the console.
The only consequence will be a change on the syntax when using that specific PowerArray function:

<ul>
	<li>
		For global functions (as all auxiliary functions GreaterThan, SmallerThan, In, InIgnoreCase, Like, NotLike, etc.) you have to use the prefix "pa.". For example by using the pa standard function <a href="#functionSmallerThan">SmallerThan</a>: <br>
		<table>
			<tr>
				<td>use</td>
				<td><code>var result = someArray.Where({ priority: <font color=red>pa.</font>SmallerThan(5)});</code></td>
			</tr>
			<tr>
				<td>instead of</td>
				<td><code>var result = someArray.Where({ priority: SmallerThan(5)});</code></td>
			</tr>
		</table>
	</li>
	
	<li>
		For prototype functions (Where, Sort, findByProperty, findIndexByProperty, etc.), you have to envolve the array in which you want to make an operation with a function call. For Example:<br>
		<table>
			<tr>
				<td>use</td>
				<td><code>var result = <font color=red>pa(</font>someArray<font color=red>)</font>.Where({ categoryId : 33});</code></td>
			</tr>
			<tr>
				<td>instead of</td>
				<td><code>var result = someArray.Where({ categoryId : 33});</code></td>
			</tr>
		</table>
	</li>
</ul>

Considering all that, if you load the PowerArray library as last in your html, the risk is strongly reduced. If there is any problem the solution will be always the same: add "pa." as prefix, or surround your array with function "pa()" before use it. 


*Under Construction (the readme file, the library works fine  :)*n


## License
The MIT License (MIT)

Copyright (c) 2015 Sebastian Menendez (sebastian.menendez[at]gmail.com)

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.*
*
