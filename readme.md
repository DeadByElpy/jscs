# JavaScript Code Style

This is a guide for writing consistent and aesthetically pleasing JavaScript code.
It is inspired by what is popular within the community, and flavored with some personal opinions.

## <a name='TOC'>Table of Contents</a>

  1. [Objects](#objects)
  1. [Arrays](#arrays)
  1. [Strings](#strings)
  1. [Functions](#functions)
  1. [Properties](#properties)
  1. [Variables](#variables)
  1. [Conditional Expressions & Equality](#conditionals)
  1. [Blocks](#blocks)
  1. [Comments](#comments)
  1. [Whitespaces](#whitespaces)
  1. [Commas](#commas)
  1. [Semicolons](#semicolons)
  1. [Type Casting & Coercion](#type-coercion)
  1. [Naming Conventions](#naming-conventions)
  1. [Accessors](#accessors)
  1. [Constructors](#constructors)
  1. [Events](#events)
  1. [Modules](#modules)
  1. [Resources](#resources)


## <a name='objects'>Objects</a>

Use the literal syntax for object creation.
> Both ways do the same thing, but literal notation takes less space. It's clearly recognizable as to what is happening, so using `new object()`, is really just typing more.

```javascript
// bad
var item = new Object();
```

```javascript
// good
var item = {};
```

Don't use [reserved words](http://es5.github.io/#x7.6.1) as keys.
> It won't work in [IE8](https://github.com/airbnb/javascript/issues/61).

```javascript
// bad
var superman = {
    default: {clark: 'kent'},
    private: true
};
```

```javascript
// good
var superman = {
    defaults: {clark: 'kent'},
    hidden: true
};
```

Use readable synonyms in place of reserved words.

```javascript
// bad
var superman = {
    klass: 'alien'
};
```

```javascript
// good
var superman = {
    type: 'alien'
};
```


## <a name='arrays'>Arrays</a>

Use the literal syntax for array creation.
> `new Array(len)` creates an array with len holes. On some JavaScript engines, this operation lets you pre-allocate arrays, giving you performance benefits for small arrays (not for large ones). In most cases, performance doesn’t matter and you can avoid the redundancy introduced by a preallocation. If it fits the problem, an array literal initializing all elements at once is preferable.

```javascript
// bad
var items = new Array();
```

```javascript
// good
var items = [];
```

To append some value to array use [Array.push](http://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push).

```javascript
// bad
items[items.length] = 'abracadabra';
```

```javascript
// good
itmes.push('abracadabra');
```

When you need to copy an array use [Array.slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice).

```javascript
itemsCopy = items.slice();
```

To convert an [array-like](http://www.2ality.com/2013/05/quirk-array-like-objects.html) object to an array, use it as well.

```javascript
function trigger() {
    var args = Array.prototype.slice.call(arguments);
    ...
}
```


## <a name='strings'>Strings</a>

Use single quotes `''` for strings.
> The difference between single and double quotes is that you don't need to escape single quotes in double quotes, or double quotes in single quotes. That is the only difference, if you do not count the fact that you must hold the Shift key to type `"`.

```javascript
// bad
var name = "Bob Parr",
    fullName = "Bob " + this.lastName;
```

```javascript
// good
var name = 'Bob Parr',
    fullName = 'Bob ' + this.lastName;
```

Strings longer than 100 characters should be written across multiple lines using string concatenation.
> In general use of such long lines should be avoided. If overused, long strings with concatenation could impact performance.
> [jsPerf](http://jsperf.com/ya-string-concat) & [Discussion](https://github.com/airbnb/javascript/issues/40).

```javascript
// good
var errorMessage = 'Lorem ipsum dolor sit amet, consectetur adipisicing elit, \
sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. \
Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris.';

// also good
var errorMessage = 'Lorem ipsum dolor sit amet, consectetur adipisicing elit, ' +
'sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. ' +
'Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris.';
```

When programatically building up complicated strings, use [Array.join](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join). String concatenation is good for simple cases.
> Modern browsers are optimized to do string concatenations so performance is [not an issue](http://stackoverflow.com/questions/7299010/why-is-string-concatenation-faster-than-array-join) anymore.

```javascript
// good
var i, str = "";
for ( i = 30000; i > 0; i-- ) {
	str += 'String concatenation. ';
}

// also good
var i, str = '', sArr = [];
for ( i = 30000; i > 0; i-- ) {
	sArr.push('String concatenation.');
}
str = sArr.join(' ');
```


## <a name='functions'>Functions</a>

Function expressions:

```javascript
// anonymous function expression
var anonymous = function() {
    return true;
};

// named function expression
var named = function named() {
    return true;
};

// immediately-invoked function expression (IIFE)
(function() {
    return true;
})();
```

Never declare a function in a non-function block (if, while, etc).
> Assign the function to a variable instead. Browsers will allow you to do it, but they all interpret it differently.

```javascript
// bad
if ( currentUser ) {
    function test () {
        console.log('Nope.');
    }
}

// good
var test;
if ( currentUser ) {
    test = function test () {
        console.log('Yup.');
    };
}
```

Never name a parameter `arguments`.
> This will take precedence over the `arguments` object that is given to every function scope.

```javascript
// bad
function nope ( name, options, arguments ) {
    ...
}

// good
function yup ( name, options, args ) {
    ...
}
```


## <a name='properties'>Properties</a>

Use dot notation when accessing properties wherever possible.
> Dot notation is faster to write and clearer to read.
> Square bracket notation allows access to properties containing special characters and selection of properties using variables.

```javascript
// bad
var data = items['pear'];
```

```javascript
// good
var data = items.pear;

// also good
var name = 'pear',
    data = items[name];
```

Delete properties with assigning to `null`.
> In modern JavaScript engines, changing the number of properties on an object is much slower than reassigning the values. The delete keyword should be avoided except when it is necessary to remove a property from an object's iterated list of keys, or to change the result of `if ( key in obj )`.

```javascript
// think twice
Foo.prototype.dispose = function () {
    delete this.someProperty;
};
```

```javascript
// good in most cases
Foo.prototype.dispose = function () {
    this.someProperty = null;
};
```


## <a name='variables'>Variables</a>

Always use `var` to declare variables.
> Not doing so will result in global variables. We want to avoid polluting the global namespace.

```javascript
// bad
superPower = new SuperPower();
```

```javascript
// good
var superPower = new SuperPower();
```

Use one `var` declaration for multiple variables.  
There should be only one `var` block for each scope.

```javascript
// bad
var items   = getItems();
var isFresh = true;
var topHtml = 'z';
```

```javascript
// good
var items   = getItems(),
    isFresh = true,
    topHtml = 'z';
```

Declare unassigned variables last.
> This is helpful when later on you might need to assign a variable depending on one of the previous assigned variables.

```javascript
// bad
var i, len,
	items = getItems(),
	goSportsTeam = true,
	dragonball;
```

```javascript
// good
var items = getItems(),
	goSportsTeam = true,
	dragonball, length, i;
```

Group your vars by meaning and try to align them.
```javascript
var items = [],
    goods = {},
    maps  = null,
    topMarks  = [5,6,7],
    topGrades = [56,57,58], 
	topCars   = null,
	// all best categories
	bestBooks, bestFilms, bestSongs,
	// everything about customer
	customerTickets, customerLogin, customerLinks;
```

Assign variables at the top of their scope.
> This helps avoid issues with variable declaration and assignment hoisting related issues.

```javascript
// bad
function nope () {
	test();

	// other stuff ...

	var name = getName();

	if ( name === 'test' ) {
		return false;
	}

	return name;
}
```

```javascript
// good
function yep () {
	var name = getName();

	test();

	// other stuff ...

	if ( name === 'test' ) {
		return false;
	}

	return name;
}
```

```javascript
// bad
function nope () {
	var name = getName();

	if ( !arguments.length ) {
		return false;
	}

    // other stuff ...

	return true;
}
```

```javascript
// good
function yep () {
	var name;
	
	if ( !arguments.length ) {
		return false;
	}

	name = getName();

    // other stuff ...

	return true;
}
```

In complex cases use JavaScript native approach.
> Variable and function declarations get hoisted to the top of their scope, their assignment does not.  
> For more information refer to [JavaScript Scoping & Hoisting](http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting)

```javascript
// bad
function nope ( isReady ) {
	if ( !isReady ) return;
	
	var name = getName(),
	    a, b, c;

    // other stuff ...
}

// also bad
function nope ( isReady ) {
	var name = getName(),
	    a, b, c;

    if ( !isReady ) return;

    // other stuff ...
}
```

```javascript
// good
function yep ( isReady ) {
    var name, a, b, c;
    
	if ( !isReady ) return;
	
	name = getName();

    // other stuff ...
}
```


## <a name='conditionals'>Conditional Expressions & Equality</a>

Use `===` and `!==` over `==` and `!=`.
> This helps to maintain data type integrity throughout your code and has a better performance.  
> For more information see [Truth Equality and JavaScript](http://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108).

Conditional expressions are evaluated using coercion with the `ToBoolean` method and always follow these simple rules:

+ `Objects` evaluate to `true`
+ `Undefined` evaluates to `false`
+ `Null` evaluates to `false`
+ `Booleans` evaluate to the value of the boolean
+ `Numbers` evaluate to `false` if `+0`, `-0`, or `NaN`, otherwise `true`
+ `Strings` evaluate to `false` if an empty string `''`, otherwise `true`

Use shortcuts.

```javascript
// bad
if ( name !== '' && collection.length > 0 ) {
    // stuff ...
}
```

```javascript
// good
if ( name && collection.length ) {
    // stuff ...
}
```

Use simple single `if` for complex expressions.

```javascript
// bad
if ( a === 1 ) {
    if ( b === 2 ) {
        if ( a1 === 1 ) {
            if ( b1 === 2 ) {
                c = 1;
            }
        }
    }
}

// also bad
(a === 1) && (b === 2) && (a1 === 1) && (b1 === 2) && (c = 1);
```

```javascript
// good
if ( a === 1 && b === 2 && a1 === 1 && b1 === 2 ) {
    c = 1;
}
```


## <a name='blocks'>Blocks</a>

Always use braces with all blocks.

```javascript
// bad
if ( isReady )
    return true;

// bad
if ( isReady ) return true;
```

```javascript
// good
if ( isReady ) {
    return true;
}

// good
if ( isReady ) { return true; }
```


## <a name='comments'>Comments</a>

Use `/** ... */` for multiline comments and `//` (with a space) for single line comments.  
For var/function/class declarations [JSDoc](http://usejsdoc.org/) should be used.

```javascript
//bad

// also
// bad
```

```javascript
// good

/**
 * good
 * when there is a lot to comment
 */
```

Use `//FIXME:` to annotate problems.
> Helps other developers quickly understand if you're pointing out a problem that needs to be revisited.
```javascript
function build () {
    //FIXME: shouldn't use a global here
    total = 0;
}
```

Use `//TODO:` to annotate solutions to problems.
> Helps other developers to see if you're suggesting a solution to the problem that needs to be implemented.
```javascript
function build () {
    //TODO: total should be configurable by an options param
    this.total = 0;
}
```


## <a name='whitespaces'>Whitespaces</a>

Use one single tab character for one-level indentation.

```javascript
// bad
function test () {
∙∙∙∙var name;
}

// bad
function test () {
∙∙var name;
}
```

```javascript
// good
function test () {
    var name;
}
```
Place one space before the leading brace.

```javascript
// bad
function test(){
    // ...
}

// bad
dog.set('attr',{
    age: '1 year',
    breed: 'Bernese Mountain Dog'
});
```

```javascript
// good
function test () {
    // ...
}

// good
dog.set('attr', {
    age: '1 year',
    breed: 'Bernese Mountain Dog'
});
```

Separate function name and its parameters in declaration.

```javascript
// bad
function test(){
    // ...
}

// bad
function test(a, b){
    // ...
}
```

```javascript
// good
function test ( a, b ) {
    // ...
}

// but no leading/trailing spaces on execution
test(128, 'qwe');
```

The same for `if`, `if/else`, `switch`, `try/catch` blocks.

```javascript
// bad
if(isReady){
    // ...
}

// bad
try{
    // ...
}catch(e){
    // error handling
}
```

```javascript
// good
if ( isReady ) {
    // ...
}

// good
try {
    // ...
} catch ( e ) {
    // error handling
}
```

Place an empty newline at the end of a file.
> Each line should be terminated with a newline character, including the last one. Some programs have problems processing the last line of a file if it isn't newline terminated.

Use line breaks and indentation when making long method chains.

```javascript
// bad
$('#items').find('.selected').highlight().end().find('.open').updateCount();
```

```javascript
// good
$('#items')
    .find('.selected')
        .highlight()
        .end()
    .find('.open')
        .updateCount();
```


## <a name='commas'>Commas</a>

No Leading commas.

```javascript
// bad
var once
    , upon
    , aTime;

// bad
var hero = {
	firstName: 'Bob'
  , lastName: 'Parr'
  , heroName: 'Mr. Incredible'
  , superPower: 'strength'
};
```

```javascript
// good
var once,
    upon,
    aTime;

// good
var hero = {
    firstName:  'Bob',
    lastName:   'Parr',
    heroName:   'Mr. Incredible',
    superPower: 'strength'
};
```

No Additional trailing comma.
> This can cause problems with IE6/7 and IE9 if it's in quirksmode. Also, in some implementations of ES3 would add length to an array if it had an additional trailing comma. This was clarified in [ES5](http://es5.github.io/#D).

```javascript
// bad
var hero = {
    firstName: 'Kevin',
    lastName: 'Flynn',
};

// bad
var heroes = [
    'Batman',
    'Superman',
];
```

```javascript
// good
var hero = {
    firstName: 'Kevin',
    lastName: 'Flynn'
};

// good
var heroes = [
    'Batman',
    'Superman'
];
```


## <a name='semicolons'>Semicolons</a>

Semicolons use is mandatory.
> Relying on [ASI](http://es5.github.io/#x7.9) (Automatic Semicolon Insertion) can cause subtle, hard to debug [problems](http://benalman.com/news/2013/01/advice-javascript-semicolon-haters/).

```javascript
// bad
a = b + c
test()

// bad
(function () {
    var name = 'Skywalker'
    return name
})()
```

```javascript
// good
a = b + c;
test();

// good
(function() {
    var name = 'Skywalker';
    return name;
})();
```

Semicolons should be included at the end of function expressions, but not at the end of function declarations.

```javascript
var foo = function () {
    return true;
};  // semicolon here

function foo () {
    return true;
}  // no semicolon here
```


## <a name='type-coercion'>Type Casting & Coercion</a>

Perform type coercion at the beginning of the statement.

```javascript
// bad
var totalScore = intValue + '';

// bad
var totalScore = '' + intValue + ' total score';
```

```javascript
// good
var totalScore = '' + intValue;

// good
var totalScore = intValue + ' total score';
```

Use `parseInt` for Numbers and always with a radix for type casting.

```javascript
var inputValue = '4';

// bad
var val = new Number(inputValue);

// bad
var val = +inputValue;

// bad
var val = inputValue >> 0;

// bad
var val = parseInt(inputValue);
```

```javascript
// good
var val = Number(inputValue);

// good
var val = parseInt(inputValue, 10);
```

Booleans.

```javascript
var age = 0;

// bad
var hasAge = new Boolean(age);
```

```javascript
// good
var hasAge = Boolean(age);

// good
var hasAge = !!age;
```


## <a name='naming-conventions'>Naming Conventions</a>

Avoid single letter names. Be descriptive with your naming.

```javascript
// bad
function q () {
    // ...
}
```

```javascript
// good
function query () {
    // ...
}
```

Use camelCase when naming objects, functions, and instances.

```javascript
// bad
var OBJEcttsssss = {};
var this_is_my_object = {};
function c() {};
var u = new user({
    name: 'Bob Parr'
});
```

```javascript
// good
var thisIsMyObject = {},
    user = new User({
        name: 'Bob Parr'
    });

function thisIsMyFunction() {
    // ...
};
```

Use PascalCase when naming constructors or classes.

```javascript
// bad
function user ( options ) {
    this.name = options.name;
}

var bad = new user({
    name: 'nope'
});
```

```javascript
// good
function User ( options ) {
    this.name = options.name;
}

var good = new User({
    name: 'yup'
});
```

Use a leading underscore `_` when naming private properties.

```javascript
// bad
this.__firstName__ = 'Panda';
this.firstName_ = 'Panda';
```

```javascript
// good
this._firstName = 'Panda';
```

When saving a reference to `this` use `self`.

```javascript
// bad
function () {
    var that = this;
    return function () {
        console.log(that);
    };
}
```

```javascript
// good
function () {
    var self = this;
    return function () {
        console.log(self);
    };
}
```

Name your functions. This is helpful for stack traces.

```javascript
// bad
var log = function ( msg ) {
    console.log(msg);
};
```

```javascript
// good
var log = function log ( msg ) {
    console.log(msg);
};
```

Use leading `$` to indicate that a DOM element is assigned to this variable.

```javascript
this.$body = document.querySelector('div.page');
```

## <a name='accessors'>Accessors</a>

Accessor functions for properties are not required. If you do make accessor functions use `getVal()` and `setVal('hello')`.
It’s okay to create `get()` and `set()` functions, but be consistent.

```javascript
// bad
dragon.age();    // getter
dragon.age(25);  // setter
```

```javascript
// good
dragon.getAge();
dragon.setAge(25);
```

If the property is a boolean, use `isVal()` or `hasVal()`.

```javascript
// bad
if ( !dragon.age() ) {
    return false;
}
```

```javascript
// good
if ( !dragon.hasAge() ) {
    return false;
}
```


## <a name='constructors'>Constructors</a>

- Assign methods to the prototype object, instead of overwriting the prototype with a new object. Overwriting the prototype makes inheritance impossible: by resetting the prototype you'll overwrite the base!

```javascript
function Jedi() {
  console.log('new jedi');
}

// bad
Jedi.prototype = {
  fight: function fight() {
	console.log('fighting');
  },

  block: function block() {
	console.log('blocking');
  }
};

// good
Jedi.prototype.fight = function fight() {
  console.log('fighting');
};

Jedi.prototype.block = function block() {
  console.log('blocking');
};
```

- Methods can return `this` to help with method chaining.

```javascript
// bad
Jedi.prototype.jump = function() {
  this.jumping = true;
  return true;
};

Jedi.prototype.setHeight = function(height) {
  this.height = height;
};

var luke = new Jedi();
luke.jump(); // => true
luke.setHeight(20) // => undefined

// good
Jedi.prototype.jump = function() {
  this.jumping = true;
  return this;
};

Jedi.prototype.setHeight = function(height) {
  this.height = height;
  return this;
};

var luke = new Jedi();

luke.jump()
  .setHeight(20);
```


- It's okay to write a custom toString() method, just make sure it works successfully and causes no side effects.

```javascript
function Jedi(options) {
  options || (options = {});
  this.name = options.name || 'no name';
}

Jedi.prototype.getName = function getName() {
  return this.name;
};

Jedi.prototype.toString = function toString() {
  return 'Jedi - ' + this.getName();
};
```

**[[⬆]](#TOC)**


## <a name='events'>Events</a>

When attaching data to events it's always should be only one parameter.
Pass a hash instead of a raw value in case there are more then one parameter.
> This approach allows `EventEmitter` implementation with better performance 

```javascript
// bad
button.trigger('click', data1, data2, data3);

button.on('click', function ( data1, data2, data3 ) {
    // do something with arguments
});
```

```javascript
// good
button.trigger('click', value);

button.on('click', function ( value ) {
    // do something with value
});
```

```javascript
// good
button.trigger('click', {val1: data1, val2: data2, val3: data3});

button.on('click', function ( data ) {
    // do something with data.val1, data.val2 and data.val3
});
```


## <a name='modules'>Modules</a>

Use [CommonJS](http://wiki.commonjs.org/wiki/Modules) modules.
[Browserify](http://browserify.org/), [Webmake](https://github.com/medikoo/modules-webmake) or similar are advised to bundle modules for web browsers.


## <a name='commits'>Commits</a>

Every commit message, pull request title or issue discussion must be in English.
> English is the universal language nowadays, if you use any other language you're excluding potencial contributors.

Don't use Past or Present Continuous tenses for commit messages, those should be in Imperative Present tense.
> This preference comes from [Git itself](http://git.kernel.org/cgit/git/git.git/tree/Documentation/SubmittingPatches?id=HEAD#n111).

```javascript
// bad
Added feature X
Adding feature X
```

```javascript
// good
Add feature X
Fix problem Y
```


## <a name='resources'>Resources</a>

* [Google JavaScript Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
* [jQuery JavaScript Style Guide](http://contribute.jquery.org/style-guide/js/)
* [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
