# CoffeeScript style guide

This is a [CoffeeScript](http://coffeescript.org/) style guide for Wave Accounting.

The content is an adaptation from another [CoffeeScript style guide](https://github.com/polarmobile/coffeescript-style-guide).

## Table of Contents

* [Code Layout](#code_layout)
    * [Tabs or Spaces?](#tabs_or_spaces)
    * [Maximum Line Length](#maximum_line_length)
    * [Blank Lines](#blank_lines)
    * [Trailing Whitespace](#trailing_whitespace)
    * [Encoding](#encoding)
* [Module Imports](#module_imports)
* [Whitespace in Expressions and Statements](#whitespace)
* [Comments](#comments)
    * [Block Comments](#block_comments)
    * [Inline Comments](#inline_comments)
    * [Annotations](#annotations)
* [Naming Conventions](#naming_conventions)
* [Functions](#functions)
* [Strings](#strings)
* [Conditionals](#conditionals)
* [Looping and Comprehensions](#looping_and_comprehensions)
* [Extending Native Objects](#extending_native_objects)
* [Exceptions](#exceptions)
* [Documentation](#documentation)
* [Miscellaneous](#miscellaneous)

<a name="code_layout"/>

## Code layout


<a name="tabs_or_spaces"/>

### Indentation

A unit of indentation is **two (2) spaces**. Never use tabs.


<a name="maximum_line_length"/>

### Maximum line length

We do not set a maximum line length. It is up to the coder and reviewer to make sure that the code is readable.


<a name="blank_lines"/>

### Blank lines

Use a single blank line to separate:

* Top-level function and class definitions
* Method definitions within a class
* Logical sections within a method or function


<a name="trailing_whitespace"/>

### Trailing whitespace

Do not include trailing whitespaces.


<a name="encoding"/>

### Encoding

UTF-8 is the preferred source file encoding.


<a name="module_imports"/>

## Module definition and imports

Modules must begin with a AMD `define` statement.

```coffeescript
define (require, exports, module) ->
```

Import statements using `require` should be on separate lines.

```coffeescript
Core = require('core/main')
Transactions = requre('transactions/main')
```

Import statements should be grouped in the following order:

1. Core module imports
2. Third-party imports
3. Local imports _(imports specific to this module)_


<a name="whitespace"/>

## Whitespace in Expressions and Statements

Avoid extraneous whitespace in the following situations:

* Immediately inside parentheses, brackets or braces

```coffeescript
($('body')) # Yes
( $('body')) # No
```

* Immediately before a comma

```coffeescript
console.log(x, y) # Yes
console.log(x , y) # No
```

Additional recommendations:

- Always surround these binary operators with a **single space** on either side

    - Assignment: `=`

        - _This also applies when indicating default parameter value(s) in a function declaration_

           ```coffeescript
           test: (param = null) -> # Yes
           test: (param=null) -> # No
           ```

           ```coffeescript
           foo: (a = 1, b = 2) -> # Yes
           foo: (a=1, b=2) -> # No
           ```

    - Augmented assignment: `+=`, `-=`, etc.
    - Comparisons: `is`, `<`, `>`, `<=`, `>=`, `unless`, etc.
    - Arithmetic operators: `+`, `-`, `*`, `/`, etc.

    - _Do not use more than one space around these operators_

        ```coffeescript
        # Yes
        x = 1
        y = 1
        fooBar = 3

        # No
        x      = 1
        y      = 1
        fooBar = 3
        ```

<a name="comments"/>

## Comments

When updating existing code, make sure the comments are also up-to-date if they exist.

Comments should be written as full sentences with proper punctuations.


<a name="block_comments"/>

### Block comments

Block comments apply to the block of code that follows them.

Each line of a block comment starts with a `#` and a single space, and should be indented at the same level of the code that it describes.

Paragraphs inside of block comments are separated by a line containing a single `#`.

```coffeescript
# This is a block comment. We should be describing the proceeding code.
#
# This is a second paragraph. We separate paragraphs with a blank comment
# line (single # with _no trailing whitespace_).
someView = new SomeView()
someView.$el.appendTo($content)
someView.render()
```


<a name="inline_comments"/>

### Inline comments

Inline comments are placed on the line immediately above the statement that they are describing. 
If the inline comment is sufficiently short, it can be placed on the same line as the statement (separated by a single space from the end of the statement).

All inline comments should start with a `#` and a single space.

Avoid inline comments that describe the _how_ of the code when they do not improve clarity.

```coffeescript
# No
x += 1 # Increment x
```

Note that the _why_ of the code are generally good.

```coffeescript
# Yes
x += 1 # Compensate for border
```

<a name="annotations"/>

### Annotations

Use annotations to describe a specific action that must be taken against the block of code immediately 
following the comment.

The annotation keyword is in all caps, followed by a colon and space.

If multiple lines are required by the description, indent subsequent lines with two spaces:

```coffeescript
# TODO: This is a long description of what needs to be done for this TODO. The next line
#   is proceeded by two spaces.
```

Other annotation types:

- `TODO`: describe missing functionality that should be added at a later date
- `FIXME`: describe broken code that must be fixed
- `OPTIMIZE`: describe code that is inefficient and may become a bottleneck
- `REVIEW`: describe code that should be reviewed to confirm implementation


<a name="naming_conventions"/>

## Naming Conventions

Use `camelCase` to name all variables, methods, and object properties.

Use `CamelCase` to name all classes and modules.

For "constants", use all uppercase with underscores:

```coffeescript
CONSTANT_LIKE_THIS
```

Methods and variables that are intended to be "private" should begin with a leading underscore:

```coffeescript
_privateMethod: ->
```

<a name="functions"/>

## Functions

_(These guidelines also apply to the methods of a class.)_

When declaring a function that takes arguments, always use a single space after the closing parenthesis of the arguments list:

```coffeescript
foo = (arg1, arg2) -> # Yes
foo = (arg1, arg2)-> # No
```

Do not use parentheses when declaring functions that take no arguments:

```coffeescript
bar = -> # Yes
bar = () -> # No
```

In cases where method calls are being chained and the code does not fit on a single line, each call should be placed on a separate line and indented by one level (i.e., two spaces), with a leading `.`.

```coffeescript
[1..3]
  .map((x) -> x * x)
  .concat([10..12])
  .filter((x) -> x < 11)
  .reduce((x, y) -> x + y)
```


### Omitting parentheses


When calling functions, omit or include parentheses in a way that optimizes readability.


For example, calling functions with anonymous function arguments is a case where parentheses omission is usually better.


```coffeescript
_.map [1..5], (x) -> x * x

foo.save (callback) ->
    # Do something then execute callback function

    # ...        

    callback()

```

The following is an example where omitting parentheses worsens readability.

```coffeescript
new Foo a: 1, b: 2, 'bar'
```

The code is more clear when written with parentheses and braces.

```coffeescript
new Foo({a: 1, b: 2}, 'bar')
```

_There is no hard and fast rule for omitting or including parentheses. Use your own judgement._


<a name="strings"/>

## Strings

Use string interpolation instead of string concatenation:

```coffeescript
"this is an #{adjective} string" # Yes
"this is an " + adjective + " string" # No
```

Prefer single quoted strings (`''`) instead of double quoted (`""`) strings, unless features like string interpolation are being used for the given string.


<a name="conditionals"/>

## Conditionals

Favor `unless` over `if` for negative conditions.

Instead of using `unless...else`, use `if...else` because it reads better in English.

```coffeescript
# Yes
if true
  ...
else
  ...

# No
unless false
  ...
else
  ...
```

Multi-line if/else clauses should use indentation:

```coffeescript
# Yes
if true
  ...
else
  ...

# No
if true then ...
else ...
```


<a name="looping_and_comprehensions"/>

## Looping and Comprehensions

Take advantage of comprehensions whenever possible:

```coffeescript
# Yes
result = (item.name for item in array)

# No
results = []
for item in array
  results.push item.name
```

To filter:

```coffeescript
result = (item for item in array when item.name is "test")
```

To iterate over the keys and values of objects:

```coffeescript
object = one: 1, two: 2
alert("#{key} = #{value}") for key, value of object
```

<a name="extending_native_objects"/>

## Extending Native Objects

Never extend native objects and prototypes.

For example, do not modify `Array.prototype` to introduce `Array#forEach`.


<a name="exceptions"/>

## Exceptions

Do not suppress exceptions.


<a name="documentation"/>

## Documentation

Use code blocks before classes, methods, and functions for documentation.

Markdown syntax is encouraged.

### What to document

1. How an object/function is used giving an example.
2. What are the expected input/output of the object (could just be an example like the one given above).
3. What exceptions does the object raise on failure
4. What events are triggered by the class and what they mean.

### Documentation examples

```coffeescript
# Base class for all animals. Based on the Animal class example from the CoffeeScript documentation.
#
# See: http://jashkenas.github.com/coffee-script/#classes
# 
# Events triggered:
#
# * `move` - After the animal has moved.
class Animal

  # Arguments:
  #
  # * name - Name of the animal. Can be a string or function that returns a string.
  constructor: (@name) ->
    if _.isFunction @name
      @name = @name()

  # Move the animal, triggering a `move` event.
  move: (metres) ->
    alert "#{@name} moved #{metres} metres"

# Class representation of a snake.  It moves 5 metres at a time.
class Snake extends Animal
  # Moves the snake ahead by 5 metres.
  move: ->
    alert 'Slithering'
    super(5)
```

<a name="miscellaneous"/>

## Miscellaneous

`and` is preferred over `&&`.

`or` is preferred over `||`.

`is` is preferred over `==`.

`isnt` is preferred over `!=`.

`not` is preferred over `!`.

`or=` should be used when possible.

```coffeescript
foo = (message) ->
    message or= 'Hello World' # Yes
    message = message or 'Hello World' # No
```

`?=` should be used for existential assigment when possible.

```coffeescript
a = null
a ?= 1 # Yes
a = a or 1 # No
a = if a == null then 1 else a # No
```

_Note that `or=` and `?=` can only be used on variables that have already been declared._

Prefer shorthand notation (`::`) for accessing an object's prototype:

```coffeescript
Array::slice # Yes
Array.prototype.slice # No
```

Prefer `@property` over `this.property`.

```coffeescript
return @property # Yes
return this.property # No
```

However, avoid the use of **standalone** `@`:

```coffeescript
return this # Yes
return @ # No
```

Omit `return` in single-line functions.

```coffeescript
square = (x) -> x * x # Yes
square = (x) -> return x * x # No
```

Use splats (`...`) when working with functions that accept variable numbers of arguments:

```coffeescript
foo = (a, b, c, rest...) ->
```
