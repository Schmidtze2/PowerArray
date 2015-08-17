# PowerArray
Power Array extends the native Array prototype by adding many additional features to <b>[Filter](#filtering),</b><b> [Sort](#sorting) </b> and  <b>[Manipulate](#manipulation)</b> complex arrays (specially objects-array, as those that normally are exposed from a rest service). 

<b>This document is under construction!</b>
###How is implemented?
PowerArray is a single javascript file, just include PowerArray.min.js in your html and it's ready to use.<br>
By loading the library, all arrays become many extra functions that are designed to simplify and drastically reduce the amount of code that must be written to operate with arrays in javascript.<br>
Current File Size: <7KB. <br>
Dependencies : None.

####What does this script changes on the Array prototype?
Nothing, it just adds new functions and properties, and only if they not already exists. 

###What are the benefits of using this library?
You're going to write much less code. No more incomprehensible loops. <a href='TheDifference'>See the difference with an example</a>

<hr>
##Introduction
All features offered by Power Array, are specified in form of javascript functions that you can call in your own arrays.<br> 
The functionalities, are organized into different categories:
<ul>
	<li><a href="#filtering"><b>Filtering</b></a></li>
	<li><a href="#sorting"><b>Sorting</a></b></li>
	<li><a href="#manipulation"><b>Manipulation</a></b></li>
</ul>   

<a name="filtering"></a>
##Filtering Data 
Normally, to obtain a subset from any array, you have to loop, evaluate fields, collect possitive matches, etc. 
At the end you always have to write at least 10 or 20 lines, deppending on the complexity of the evaluations you have to do to find the items you're searching for. <br>
To simplify filtering tasks, Power Array relies mainly on the <b><a name="#WhereFunction">Where</a></b> function, which offers an intuitive and and easy to learn way to formulate conditions. The principle behind, is to formulate the conditions nearly as you would do it in English language. The following picture, shows (a very basic) PowerArray formulation, by using the functions "Where" and "Sort":

<img src="/detky/PowerArray/blob/master/images/BasicStatementTransformation.jpg"/>
  


<a name="WhereFunction"></a>
###The Where function
This function returns a subset of an existing array by filtering it with a Conditions-Object. A Conditions-Object is a JSON object that describes which conditions must satisfy an array item to be included on the results. <br>
<b>Using Where() you can reduce complex operations into a single, readable, and intuitive statement.</b>


####Signature
<table>
<tr>
	<td valign=top  colspan=2><b>.Where</b>(whereConditions [,keepOrder])<br></td>
</tr>
<tr>
	<td valign=top >Param</td><td valign=top ><b>whereConditions</b> - Type: <a href="#WhereFunctionWhereConditionsIsJson">Object</a>,  <a href="#WhereFunctionWhereConditionsIsArrayOfJson">Array of objects</a>,  <a href="#WhereFunctionWhereConditionsIsFunction">Function</a>, instance of <a href="#WhereFunctionWhereConditionsIsPaEqualTo">EqualTo </a> function.<br>
			One or more search criterions to be evaluated on each array element
		
		<hr>
		
		When parameter whereConditions is:<br>
		<ul>
			<li><a name="WhereFunctionWhereConditionsIsJson"></a>A <b>JSON Conditions-object</b><br>
					parameter "whereConditions" is a A JSON object with the following format:<br><br><code>{property: criterion, property: criterion, etc.}</b></code><br><br>
					Each "<b>property</b>" indicates (with his name) which field of the array items should be evaluated. The corresponding "<b>criterion</b>", indicates the condition that the "<b>property</b>" should satisfy. A "<b>criterion</b>" can be different things: <br><br>
				<ul>
					<li>A fix <b>primitive value</b> (a string, a number, a date, etc.). For example:<br><br>
						 <code><b>var result = someArray.Where({id : 31});<br>or<br>var result = someArray.Where({state : 'open',  category: 1})</b></code><br><br>
					</li>
					<li>
						Any <b>standard pa Function</b> (<a href="#StandardPaFunctions">complete list here</a>)
						Power Array have a set of out of the box functions that can be used to simplify widely used conditions. For example:<br><br>
						<code>
							<b>var result = someArray.Where({state : In(['open','pending']), amount: SmallerThan(1000)})</b><br></code><h6>Explanation: by using the previous statement, the variable result will get all items of the array "someArray" that have on the property "state" the values "open" or "pending", and a value bigger than 1000 on the property "amount"</h6>or<br><br> 
							<code><b>var result = someArray.Where({lastname : EqualTo3('MÃ¼ller'), citi : Like('B'), city : NotIn(['Bogota', 'Barcelona'])})</b>
						</code> <h6>Explanation: by using the previous statement, the variable result will get all items of the array "someArray" that have on the property "lastname" the value "MÃ¼ller" (comparison made with 3 === symbols), on the property "city" a value containing the character "B", and also on the "city" property a, value different than "Bogota" and "Barcelona"</h6><br>
					</li>
					<li>
						A <b>custom function</b>, which will get the value of the property to evaluate as first parameter.<br>For example: <br><br><code><b>someArray.Where(function(val){return val > 10 && val < 20})</b></code>
					</li>
				</ul><br>
			
			</li>
			<li><a name="WhereFunctionWhereConditionsIsArrayOfJson"></a>An <b>Array of JSON Conditions-object</b> <br>
				   parameter "whereConditions" is an array of <a hre="#WhereFunctionWhereConditionsIsJson">JSON condition-objects</a>, as defined above.<br><br>
				   When multiple condition-objects are provided, the array items must satisfy just one condition-object to be included on the result, allowing you to construct conditions that will work as OR conditions. For example:<br><br>
					
				   Let's say you have an array of Orders, each one having a properties "city" and "amount". You have to filter all orders from "Sydney" having an amount smaller than "1000" OR any order with an amount bigger than 50000 ("city" does not care). Then you could do:<br><br>
				  <code>
							<b>var result = ordersArray.Where([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{city: "Sydney", amount: SmallerThan(1001)},<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{amount : biggerThan(49999)}<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;]);</b><br></code>
						</code><br>
				 There is no limitation about the quantity of condition-objects you can pass.<br><br>
		
				   
				</li>
					<li><a name="WhereFunctionWhereConditionsIsFunction"></a>A <b>Custom function</b><br>
					parameter "whereConditions" is a custom function.<br><br> The provided function will receive as first parameter a complete array item, and it should return true or false, indicating if the current item should be included on the resultant array or not. For example: <br><br>
					<code>
						<b>someArray.Where(function(item){return item.id > 10 && item.id < 20;})</b>
					</code> <br><br>
				</li>
				<li>
					<a name="WhereFunctionWhereConditionsIsPaEqualTo"></a>An <b>EqualTo</b> function (pa standard function)<br>
				 	parameter "whereConditions" is an instance of the standard pa-function EqualTo. <br><br>This special case allows you to filter by comparing each array item with an specific Json object. For example: <br><br>
					<code>
						<b>
							var originalElement = {...};
							var result = manyElementsArray.Where(EqualTo(originalElement));
						</b>
					</code> <br><br>
					The EqualTo function, provides a default way to compare objects. 
					//TODO: document cyclic, properties order, etc.
				</li>
		</ul>
		
</td>
</tr>
<tr>
	<td valign=top >Param</td><td valign=top ><b>keepOrder</b> - Type: Boolean<br>
	Indicates if the order of the array items have to be mantained or not.
</td>
</tr>
</table>


 
To graphically explain how the function works, and how it could be used, i recommend you to take a look to the following basic examples

###Examples introduction
The following examples assumes that the variable "people" is an array of objects. Each object represents a person and each person-object have some fields (id, name, lastname, age, address, etc.), and also a method called getChilds(), that returns another array of people. Let's say:
<code><pre>
var people = [
			{id : 0, name: 'Max', lastname: 'Muster', age : 20, getChilds : function() { return [{......}]},
        	{id : 1, name: 'Juan', lastname: 'Perez', age : 31, getChilds : function() { return [{......}]},
            {id : 1, name: 'Sherlock', lastname: 'Holmes', age : 45, getChilds : function() { return [{......}]},
            etc.
}];
</pre></code>

There are many different ways to specify conditions, but the best way to understand the possibilities, is to see it in action

<ul>
      <li><b>To search for people called 'Sherlock', you could use:</b><br>
       <code>var newArray = people.Where({name: 'Sherlock'});</code></br></br>
      </li>
      <li><b>To search for people called 'Sherlock Holmes', use:</b><br>
            <code>var newArray = people.Where({name: 'Sherlock', lastname:'Holmes'});</code></br> </br>
      </li>
</ul>

PowerArray adds also some auxiliary functions to avoid writing the same snippets over and over again. All they are accesible after loading the library, [click here for a complete list](#WherePAStandardFunction).

<ul>
      <li><b>To search for people called 'Sherlock' and also older than 33 years, use:</b><br>
            <code>var newArray = people.Where({name: 'Sherlock', age : pa.biggerThan(33)});</code></br></br>
      </li>
      <li><b>To search for peoplecalled 'Sherlock', older than 33 years, and containing the characters 'H', 'l' in his lastname:</b><br>
	  <code>var newArray = people.Where({name: 'Sherlock', age : pa.biggerThan(33), lastname : pa.Like
		(['H','l'])});</code></br><br>
      </li>
        <li><b>To repeat the last example, but searching for name and lastname case insensitive, use:</b><br>            <code>var newArray = people.Where({name: pa.LikeIgnoreCase('Sherlock'), age : pa.biggerThan(33), lastname : pa.LikeIgnoreCase(['H','l'])});</code></br></br>
      </li>
</ul>

When using the Where() function to filter a big amount of data, you have to consider the performance impact the second possible parameter 

consider the order of the filter conditions you provide

<a name="StandardPaFunctions"></a>
###Auxiliar functions / Standard PA Functions
PowerArray adds also some auxiliary functions (i call them Standard PA Functions) that can be used together with the [Where](#WhereFunction) function. The idea behind the auxiliar functions is to reduce the syntax complexity of the Where function, and to standarize many small tasks that normally are x times repeated in practically any JS project.
When the power array library loads, it stores all standard functions under the pa object ("window.pa.FUNCTION_NAME" or simply "pa.FUNCTION_NAME") and if its possible, it creates a pointer for each function under the window object (allowing you for example to code <code>BiggerThan(something)</code> instead of <code>pa.BiggerThan(something)</code> or <code>window.pa.BiggerThan(something)</code>).<br>You'll get a warning on the console if a function could not be published directly in the window object, and if that happens, you HAVE to use the pa prefix.  

#####Standard PA Functions list:

<ul style="list-style:none">
<table>
<tr>
	<td valign=top  colspan=2><b>BiggerThan</b>(value)<br>
	Evaluate if the value of the property to be evaluated is bigger than the passed value.
	</td>
</tr>
<tr>
	<td valign=top >
		Param</td><td valign=top ><b>value</b> - Type: String, Number, Boolean or Date<br>
		The value to compare to 
	</td>
</tr>
<tr>
	<td valign=top >
		Return</td><td valign=top ><b>Boolean</b> - true if the evaluated value is bigger than the passed value parameter, else false.
	</td>
</tr>
<tr>
	<td valign=top >
		Example</td><td valign=top ><code>var subset = someArray.Where({age : BiggerThan(18)})</code><br>Variable subset becomes an array of all people older than 18 years.</td>
	</td>
</tr>
</table>
<table>
<tr>
	<td valign=top  colspan=2><b>SmallerThan</b>(value)<br>
	Evaluate if the value of the property to be evaluated is smaller than the passed value.
</td>
</tr>
<tr>
	<td valign=top >
		Param</td><td valign=top ><b>value</b> - Type: String, Number, Boolean or Date<br>
		The value to compare to 
	</td>
</tr>
<tr>
	<td valign=top >
		Return</td><td valign=top ><b>Boolean</b> - true if the evaluated value is smaller than the passed value parameter, else false.
	</td>
</tr>
<tr>
	<td valign=top >
		Example</td><td valign=top ><code>var subset = someArray.Where({age : SmallerThan(50)})</code><br>Variable subset becomes an array of all people younger than 50 years.</td>
	</td>
</tr>
</table>
<table>
<tr>
	<td valign=top  colspan=2><b>EqualTo3</b>(value)<br>
	Checks if the property to be evaluated is equal to the value passed on parameter "value". 
	The evaluation is made by identity (===) operator (no type conversion is done)
</td>
</tr>
<tr>
	<td valign=top >
		Param</td><td valign=top ><b>value</b> - Type: String, Number, Boolean or Date<br>
		The value to compare to
	</td>
</tr>
<tr>
	<td valign=top >
		Return</td><td valign=top ><b>Boolean</b> - true if the evaluated value is equal to the passed value, else false. 
	</td>
</tr>
<tr>
	<td valign=top >
		Example</td><td valign=top ><code>var subset = someArray.Where({age : EqualTo3(20)})</code><br>Variable subset becomes an array of all items having 20 on the property age. If an array item have "20" (as string) on the age property, it will not be included on the results (use EqualTo2 for that).</td>
	</td>
</tr>
</table>
<table>
<tr>
	<td valign=top  colspan=2><b>EqualTo2</b>(value)<br></td>
</tr>
<tr>
	<td valign=top >
		Param</td><td valign=top ><b>value</b> - Type: String, Number, Boolean or Date<br>
		Compares the affected element with the passed Value. 
	</td>
</tr>
<tr>
	<td valign=top >
		Return</td><td valign=top ><b>Boolean</b> - true if the evaluated value is equal to the passed value, else false. The evaluation is made by equality (==) operator
	</td>
</tr>
<tr>
	<td valign=top >
		Example</td><td valign=top ><code>var subset = someArray.Where({age : EqualTo2(20)})</code><br>Variable subset becomes an array of all items having 20 on the property age. If an array item have "20" (as string) on the age property, it will be also included.</td>
	</td>
</tr>
</table>
<table>
<tr>
	<td valign=top  colspan=2><b>EqualTo</b>(referenceObject, func, enforce_properties_order, cyclic)<br>Used to compare all array elements with a single object(referenceObject).</td>
</tr>
<tr>
	<td valign=top >
		Param</td><td valign=top ><b>referenceObject</b> - Type: Object<br>
		The object that will be compared with all array elements. 
	</td>
</tr>
<tr>
	<td valign=top >
		Param</td><td valign=top ><b>func - Optional</b> - Type: Function(a,b)<br>
		A custom function that will be called for each element on the array. It should return a boolean value, and accept two parameters that will be automatically passed: the first one, will be always the originally passed referenceObject, the second will be the current array item. <br><br>
		If no func parameter is provided, the comparison will be made by using a default function (the equals function of the utility class value_equals from the <a href='https://github.com/detky/toubkal/blob/master/lib/util/value_equals.js' target=_blank>the toubkal project</a>)
	</td>
</tr>
<tr>
	<td valign=top >
		Param</td><td valign=top ><b>enforce_properties_order - Optional</b> - Type: Boolean<br>
		This parameter takes effect, ONLY when the parameter func is falsy.<br>
		Pass true to check if Object properties are provided in the same order between referenceObject and each array item
	</td>
</tr>
<tr>
	<td valign=top >
		Param</td><td valign=top ><b>cyclic - Optional</b> - Type: Boolean<br>
		This parameter takes effect, ONLY when the parameter func is falsy.<br>
		Pass true to check for cycles in cyclic objects. 
	</td>
</tr>
<tr>
	<td valign=top >
		Return</td><td valign=top ><b>Boolean</b> - true if the evaluated array item is equal to the passed object, else false.
	</td>
</tr>
<tr>
	<td valign=top >
		Example</td><td valign=top >
		
		Using default comparison function:<br><br>		
		<code>var originalElement = { something : 'bla' }; <br> <br>	
		 var subset = someArray.Where(EqualTo(originalElement, undefined, true, false));
		<br></code><br>
		Variable subset becomes an array of all items that are equal to originalElement. A different order in the object properties is not considered as difference (because param "enforce_properties_order" is false). Cyclic references are not evaluated (because param "cyclic" is false).<br><br>

		<b>Using custom comparison function:</b><br><br>		
		<code>var func = function(a,b) { return a.something === b.shomething };<br><br>
		var originalElement = { something : 'bla' };</code><br><br>
		var subset = someArray.Where(EqualTo(originalElement,func));</code><br><br>Variable subset becomes an array of all items having 'bla' on the property something.<br><br>
</td>
</tr>

</table>
<table>
<tr>
	<td valign=top  colspan=2><a name="LikeFunction"><b>Like</b>(value)</a><br></td>
</tr>
<tr>
	<td valign=top >
		Param</td><td valign=top ><b>value</b> - Type: String or Array of Strings<br>
		Searches the passed value(s) to be present in the corresponding property of the affected element. 
	</td>
</tr>
<tr>
	<td valign=top >
		Return</td><td valign=top ><b>Boolean</b> - true if the passed value is present on the affected element. If value is an array, true is only returned when all array positions are found. 
	</td>
</tr>
<tr>
	<td valign=top >
		Example</td><td valign=top ><code>var subset = someArray.Where({name: pa.Like("a")});</code><br>Variable subset becomes an array of all items having the character "a" on the on the property name.<br><br>
		<code>var subset = someArray.Where({name: pa.Like(["a","b"])});</code><br>Variable subset becomes an array of all items having the character "a" and "b" on the on the property name.
	</td>
</tr>
</table>
<table>
<tr>
	<td valign=top  colspan=2><b>LikeIgnoreCase</b>(value)<br>Identical to function <a href="#LikeFunction">Like</a>, but ignoring characters case.</td>
</tr>
</table>

<br>//TODO: document missing functions: NotLIke, NotLikeIgnoreCase, NotIn, NotInIgnoreCase
</ul>


//TODO: getByProperties as auxiliar pa function
//TODO: write an example to for #TheDifference
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