node-style-guide
================

This guide created by Jim Lake for Boingo NodeJS projects.  It sources some
items from [Felix Geisendörfer](http://felixge.de/) and is
licensed under the [CC BY-SA 3.0](http://creativecommons.org/licenses/by-sa/3.0/)
license. You are encouraged to fork this repository and make adjustments
according to your preferences.

![Creative Commons License](http://i.creativecommons.org/l/by-sa/3.0/88x31.png)

## 4 Spaces for indention

Use 4 spaces for indenting your code and swear an oath to never mix tabs and
spaces - a special kind of hell is awaiting you otherwise.

## Newlines

Use UNIX-style newlines (`\n`), and a newline character as the last character
of a file. Windows-style newlines (`\r\n`) are forbidden inside any repository.

## No trailing whitespace

Just like you brush your teeth after every meal, you clean up any trailing
whitespace in your JS files before committing. Otherwise the rotten smell of
careless neglect will eventually drive away contributors and/or co-workers.

## Use Semicolons

According to [scientific research][hnsemicolons], the usage of semicolons is
a core value of our community. Consider the points of [the opposition][], but
be a traditionalist when it comes to abusing error correction mechanisms for
cheap syntactic pleasures.

[the opposition]: http://blog.izs.me/post/2353458699/an-open-letter-to-javascript-leaders-regarding
[hnsemicolons]: http://news.ycombinator.com/item?id=1547647

## 80 characters per line

Limit your lines to 80 characters. This is not a hard/fast rule but try when
you can.

## Use double quote for human strings

Use double quotes in general for strings.

*Right:*

```js
var foo = "bar";
```

*Wrong:*

```js
var foo = 'bar';
```

## Opening braces go on the next line

Your opening braces go on the next line as the statement.

*Right:*

```js
if( true ) 
{
    console.log('winning');
}
```

*Wrong:*

```js
if( true ) {
    console.log('losing');
}
```

Also, notice the use of whitespace before and after the condition statement.

## Declare one variable per var statement

Declare one variable per var statement, it makes it easier to re-order the
lines.  Don't line up your equal signs.  That is not useful and leads to people
playing with whitespace all day.

*Right:*

```js
var keys = ["foo", "bar"];
var values = [23, 42];

var object = {};
while( keys.length ) 
{
    var key = keys.pop();
    object[key] = values.pop();
}
```

*Wrong:*

```js
var keys   = ["foo", "bar"],
    values = [23, 42],
    object = {},
    key;

while( keys.length ) 
{
    key = keys.pop();
    object[key] = values.pop();
}
```

## Use lower_lower for variables, properties and function names

Variables, properties and function names should use `lowerCamelCase`.  They
should also be descriptive. Single character variables and uncommon
abbreviations should generally be avoided.

*Right:*

```js
var admin_user = db.query('SELECT * FROM users ...');
```

*Wrong:*

```js
var adminUser = db.query('SELECT * FROM users ...');
```

## Use UpperCamelCase for class names

Class names should be capitalized using `UpperCamelCase`.

*Right:*

```js
function BankAccount() {
}
```

*Wrong:*

```js
function bank_Account() {
}
```
## Use UPPERCASE for Constants

Constants should be declared as regular variables or static class properties,
using all uppercase letters.

Node.js / V8 actually supports mozilla's [const][const] extension, but
unfortunately that cannot be applied to class members, nor is it part of any
ECMA standard.

*Right:*

```js
var SECOND = 1 * 1000;

function File() 
{
}
File.FULL_PERMISSIONS = 0777;
```

*Wrong:*

```js
const SECOND = 1 * 1000;

function File() 
{
}
File.fullPermissions = 0777;
```

[const]: https://developer.mozilla.org/en/JavaScript/Reference/Statements/const

## Be very careful with module globals.

It's very annoying to have to track down the scope of a module global variable,
in general you shouldn't have them unless you specifically want global behavior.
When you do need global behavior prefix your variable with g_ to indicate its 
a global, and define it at the top of the module file.

*Right:*

```js

var g_common_list = [];

function foo(val)
{
    var local_list = [ 'foo', 'bar' ];

    if( _.contains(g_common_list,val) )
    {
        // do something
    }
}

```

*Wrong:*

```js
var common_list = [];

function foo(val)
{
    var local_list = [ 'foo', 'bar' ];

    if( _.contains(common_list,val) )
    {
        // do something
    }
}
```

## Don't use global globals

Always put anyhting you want shared between multiple modules in a module itself.
Don't use global. unless you have a very very good reason.

## Object / Array creation

Use trailing commas and put *short* declarations on a single line. Only quote
keys when your interpreter complains:

*Right:*

```js
var a = ['hello', 'world'];
var b = {
  good: 'code',
  'is generally': 'pretty',
};
```

*Wrong:*

```js
var a = [
  'hello', 'world'
];
var b = {"good": 'code'
        , is generally: 'pretty'
        };
```

## Define your functions as functions not as variables
Don't put store closures in variables or on object properties on instantiation.

*Right:*

```js
function foo() 
{  
}
function bar()
{

}
var func_map = {
    bar: bar,
}

```

*Wrong:*

```js

var foo = function()
{
}
var func_map = {
    bar: function()
    {
    }
}

```

## Append to exports at the top of your module or the bottom

Group your exports at the top of your module, this defines the "interface" for 
your module in one place.  If you're only exporting functions (which have global
name scope, so can be exported before being defined), this should be
done at the top of the file, directly after your requires.

If you are exporting values as well, you should move all your exports to the
bottom, as values can only be exported after being defined.

*Right:*

```js
var fs = require('fs');

exports.query = query;
exports.find = find;

...

function query(sql,done)
{
    // ...
}
```

*Also Right:*

```js
var fs = require('fs');

var MAX_QUERY_COUNT = fs.readFileSync('max_setting.txt');

...

function query(sql,done)
{
    // ...
}

exports.query = query;
exports.find = find;
exports.MAX_QUERY_COUNT = MAX_QUERY_COUNT;

```

*Wrong:*

```js

exports.query = function(sql,done) 
{
    // wrong!
}

exports.find = function(obj,done)
{
    // Still wrong
}
```

*Also Wrong:*

```js

var my_exports = {
    query: function(sql,done) 
    {
        // wrong!
    },
    find: function(obj,done)
    {
        // Still wrong
    }
};

module.exports = my_exports;

```

## Be safe if you extend built-in prototypes

Make sure to be safe when extending prototypes.  Also, don't extend prototype
when you could just use a built in property

*More Right:*

```js
var a = [];
if( !a.length ) 
{
    console.log('winning');
}
```

*Less right:*

```js
if( typeof Array.prototype.empty != 'function' )
{
    Array.prototype.empty = function() {
        return !this.length;
    }
}

var a = [];
if( a.empty() ) 
{
    console.log('losing');
}
```

*Wrong:*

```js
Array.prototype.empty = function() {
    return !this.length;
}

var a = [];
if( a.empty() ) 
{
    console.log('losing');
}
```


## Use descriptive conditions

Any non-trivial conditions should be assigned to a descriptively named variable or function:

*Right:*

```js
var is_valid = password.length >= 4 && /^(?=.*\d).{4,}$/.test(password);

if( is_valid ) 
{
    console.log('winning');
}
```

*Wrong:*

```js
if( password.length >= 4 && /^(?=.*\d).{4,}$/.test(password) ) 
{
    console.log('losing');
}
```

## Write small functions, usually.

Keep your functions short.  But, if you function is in fact very complicated
don't be afraid to make it long.  Common code used in multiple places should
always be refactored to use the same sub-function.  But you should endevor 
to not have a bunch functions that are only called in one place and use to make
you functions simply "shorter".

## Never return early from functions

Endevor to have single return for a function.  Else-if if your friend.  For
trivial functions this might seem like overkill, but once you right longer
functions this becomes very useful.

*Right:*

```js
function is_percentage(val) 
{
    var ret = true;

    if( val < 0 ) 
    {
        ret = false;
    }
    else if( val > 100 ) 
    {
        ret = false;
    }

    return ret;
}
```

*Wrong:*

```js
function is_percentage(val) 
{
  if (val >= 0) {
    if (val < 100) {
      return true;
    } else {
      return false;
    }
  } else {
    return false;
  }
}
```

Or for this particular example it may also be fine to shorten things even
further:

```js
function is_percentage(val) 
{
    var is_in_range = (val >= 0 && val <= 100);
    return is_in_range;
}
```

## Use single done pattern

Endevor to have single callback location for a closure.  Else-if if your friend
here as well.  Same reasons as above.

*Right:*

```js
function check_db(done) 
{
    db.query("SELECT * FROM",function(err,results)
    {
        if( err )
        {
            console.error("Damn:",err);
        }
        else if( results.length == 0 )
        {
            err = 'not_foud';
        }
        else
        {
            console.log("Whoo, results:",results);
        }
        done(err);
    }
}
```

*Wrong:*

```js
function check_db(done) 
{
    db.query("SELECT * FROM",function(err,results)
    {
        if( err )
        {
            console.error("Damn:",err);
            done(err);
        }
        else if( results.length == 0 )
        {
            done('not_found')
        }
        else
        {
            console.log("Whoo, results:",results);
            done(null);
        }
    }
}
```

## Use slashes for comments

Use slashes for both single line and multi line comments. Try to write
comments that explain higher level mechanisms or clarify difficult
segments of your code. Don't use comments to restate trivial things.

*Right:*

```js
// 'ID_SOMETHING=VALUE' -> ['ID_SOMETHING=VALUE', 'SOMETHING', 'VALUE']
var matches = item.match(/ID_([^\n]+)=([^\n]+)/));

// This function has a nasty side effect where a failure to increment a
// redis counter used for statistics will cause an exception. This needs
// to be fixed in a later iteration.
function load_user(id, cb) 
{
    // ...
}

var is_valid = (session.expires < Date.now());
if( is_valid ) 
{
    // ...
}
```

*Wrong:*

```js
// Execute a regex
var matches = item.match(/ID_([^\n]+)=([^\n]+)/));

// Usage: load_user(5, function() { ... })
function load_user(id, cb) 
{
    // ...
}

// Check if the session is valid
var is_session_valid = (session.expires < Date.now());
// If the session is valid
if( is_session_valid )
{
    // ...
}
```

## Getters and setters

Do not use setters, they cause more problems for people who try to use your
software than they can solve.  This is not Java.

Feel free to use getters that are free from [side effects][sideeffect], like
providing a length property for a collection class.

[sideeffect]: http://en.wikipedia.org/wiki/Side_effect_(computer_science)
