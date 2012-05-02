# Frontend Style Guide

## JavaScript

There are a bazillion blog posts and books which cover the intricacies of JavaScript gotchas, best practices, performance improvements, etc. which we will obviously not rewrite here. Instead, what follows are some conventions and preliminary stuff. (Also note that as responsible frontends building web *sites*, we would address progressive enhancement and ensure our site is usable to users without JavaScript. But we are working on a web *application* where JavaScript is considered required, so we will not delve into that for the moment.)

* It should not be necessary to have JavaScript either inline in an element (definitely no `onclick` handlers, etc) or in the `<head>` section of individual pages (exception: something that needs to be dynamically rendered).

### Naming conventions

* CamelCase for classes/constructors.
* mixedCase for function and variable names.
* Private variables prefixed with an underscore (`_`).
* Constants are uppercase with an underscore (`_`) between words.

**Example**

    MyConstructor() // constructor
    myFunction()    // function
    _myPrivate()    // private functions (jslint complains unless nomen = false)
    var firstName   // variable
    var TAX_RATE    // constant


### CSS class names

Separate the concerns between CSS classes for styling and CSS classes for JavaScript hooks. Do not use the former in js code at all, and use a `js-` prefix on CSS classes for the latter. This helps tremendously when refactoring both visuals and behaviour.

**No**

      <a href="#" class="btn btn-save">Save</a>
      $(".btn").click(...); // "btn" is for presentation only

  **Yes**

      <a href="#" class="btn btn-save js-save-thing">Save</a>
      $(".js-save-thing").click(...);

  **No**

      <a href="#" class="js-save-thing">Save</a>
      .js-save-thing { /* no styling on js prepended classnames */
          color: green;
          padding: 10px;
      }

  **Yes**

      <a href="#" class="js-save-thing my-button">Save</a>
      .my-button {
          color: green;
          padding: 10px;
      }


### Combine `var` declarations for conciseness

**No**

    var foo = 'a';
    var bar = 'b';
    var what = {};

**Yes**

    var foo = 'a',
        bar = 'b',
        what = {};
    // let's not talk about leading commas just yet

### Always use semi-colons

Technically, JavaScript engines will do automatic semi-colon inseration when they are left out. This can lead to unintended consequences, and grammar ambiguity in the future.

For sanity's sake, always, always use semi-colons.

**No**

    var a = 1

    (function () {
      var b = a + 1
      // Do something with b...
    }())

    // ASI will insert semi-colons to the above as follows:
    //
    // var a = 1(function () {
    //     var b = a + 1;
    // }());
    //
    // Which will complain that `1` is not a function.
    // Of course, this is probably not what you intended!


**Yes**
    var a = 1;

    // The trailing semi-colon in previous statement lets the engine know to not continue it
    (function () {
      var b = a + 1;
      // Do something with b...
    }());

### Do not insert a newline before the opening curly braces for a code block

When used in the returned statement, ASI will actually insert a semi-colon after the `return`, which will return undefined.

**No**

    function foo ()
    {
        return
        {
            bar: 1
        };
    }

**Yes**
    
    function foo () {
        return {
            bar: 1
        };
    }

### Indentation and spacing

A unit of indentation is **four (4) spaces**. Please set your text editor appropriately!

Statment continuation on new lines are indented to next level, not to align with opening braces. Ensure closing braces match indentation level.

**No**

    element.find('.actions').animate({marginLeft:'-20px',
                                      width: '1px',
                                      paddingTop: '10px'},
                                      100, function(){
                                        $(this)
                                            .css('position','static')
                                            .hide();
                                      });
    });

**Yes**

    element
      .find('.actions')
      .animate({
        marginLeft:'-20px',
        width: '1px',
        paddingTop: '10px'
      }, 100, function(){
        $(this)
          .css('position','static')
          .hide();
      });

Don't bother aligning around `=` in variable declarations.

**No**

    var a                        = "",
        bar                      = 4,
        myReallyLongVariableName = "foo",
        comeOn                   = null;

**Yes**

    var a = "",
        bar = 4,
        myReallyLongVariableName = "foo",
        comeOn = null;

Spacing around function definitions. There should be one space after `function` and one space before the opening `{`.

**No**

    var a = function(foo,bar){ ...

    var b = function(foo,bar) { ...

    var c = function (foo,bar){ ...

**Yes**

    var d = function (foo, bar) { ...

Spaces around operators (`a = 2`, not `a=2`), after colons (`{a: 1, b: 2}`, not `{a:1,b:2}`), after comma in lists of things (`(a, b, c)`, not `(a,b,c)`).

Spacing for `for` loops.

**No**

    for (var i=0,i<10;i++) {
        ...
    }

**Yes**

    for (var i = 0, i < 10; i++) {
        ...
    }

### Prefer `instanceof` over `typeof`

JavaScript's `typeof` operator is not very useful.

    typeof function () {}   // "function"
    typeof [1, 2, 3]        // "object"
    typeof {}               // "object"
    typeof null             // "object"

    // use either `instanceof`:
    var f = function(){}; f instanceof Function    // true
    [] instanceof Array                            // true
    var o = {}; o instanceof Object                // true

    // or access the object's constructor:
    [].constructor
    "".constructor
    // note you can't access the constructor for null and undefined

### For loops

Cache the length of your array for `for` loops.

**No**

    for (var i = 0; i < myarray.length; i++) {
        // myarray is accessed each time through the loop. If it's a DOM collection, this is very expensive.
    };

**Yes**

    for (var i = 0, max = myarray.length; i < max; i++) {
        // use myarray[i]
    };


For-in loops should check if the object has the property

**No**

    for (var listName in listItems) {
        // do stuff
    }


**Yes**

    for (var listName in listItems) {
        if (listItems.hasOwnProperty(listName)) {
            // do stuff
        }
    }

### Use object and array shortcuts for instantiation

**No**

    var a = new Object();
    var myArr = new Array();

**Yes**

    var a = {};
    var myArr = [];

## jQuery

### Naming conventions

Refer to the event object as `e` (instead of `e`, `event`, `evt`...). 

Refer to generic elements as `el` or `$el` (instead of `element`, `triggerElement`...). 

Refer to callbacks as `callback` (instead of `c`, `clbk`...).


### Be aware of `return false` in jQuery and vanilla DOM

A `return false` in the event handler in jQuery is effectively calling `e.preventDefault()` **and** `e.stopPropagation()`.

In normal DOM event handlers, `return false` does not stop propagation (i.e. it still bubbles up).

Since we are using jQuery, do not `return false` unless you want both behaviours.


### Prefix variables holding jQuery objects with a dollar sign (`$`)

This makes it clear later in the code that the variable is holding a jQuery object.

**No**

    var items = $("#Foo li"),
        bar = 1,
        baz = "a";
    bar += 1;
    items.addClass('foo').show();
    baz = "b";

**Yes**

    var $items = $("#Foo li"),
        bar = 1,
        baz = "a";
    bar += 1;
    $items.addClass('foo').show();
    baz = "b";

### Cache or chain selections (this also helps with legibility)

**No**

    // you are looking up .data each time!
    $(".data").addClass('foo');
    $(".data").width(400);
    $(".data").fadeOut();

    // ZOMG brackets everywhere
    $("#foo").live('click', function(e){
        var isLinkThis = $(e.target).is('#this');
        var isLinkThat = $(e.targer).is('#that');
        var isLinkSomething = $(e.target).hasClass('something');
        ...
    });

**Yes**

    $(".data")
        .addClass('foo');
        .width(400);
        .fadeOut();
        
    var $listItems = $("#foo li"); // ... do something expensive with $listItems

    $("#foo").live('click', function(e){
        var $el = $(e.target);
        var isLinkThis = $el.is('#this');
        var isLinkThat = $el.is('#that');
        var isLinkSomething = $el.hasClass('something');
        ...
    });

### Assemble new DOM elements first and attach them once

**No**

    for (var i = 0; i < 100; i++) {
        $("#elem").append(....
    }

**Yes**

    for (var i = 0; i < 100; i++) {
        // assemble yourString
    }
    $("#elem").append(yourString)

### Detach elements while working on them

**No**

    var $table = $("#table");
    $table.addLotsOfRows();

**Yes**

    var $table = $("#table"),
        $parent = $table.parent();
      
    $table.detach(); // takes out of DOM, but leaves jQuery data intact (requires 1.4)
    $table.addLotsOfRows();
    $parent.append($table);

### Don't pass selector as context

**No**

    var foo = $(".bar", "#baz");

**Yes**

    var foo = $("#baz").find(".bar");

### Use the most optimal selector available

    // Selectors with Sizzle are read right-to-left, so put specific on right, 
    // light on left -- tag.class on right, just tag or just .class on left.
    $(".data td.someone");

    // descending from id is best
    $("#foo td.someone");

    // don't be needlessly specific
    $(".data table.people td.someone"); // don't need table.people

    // don't use universal selector
    $(".buttons > *");        // no
    $(".buttons").children(); // yes

    $(":password");      // no: has to check all DOM elements to see if they are of type password
    $("input:password"); // yes: narrows search set down considerably

### Storing data

    // normal
    $("#elem").data(key, value);

    // faster
    $.data(elem, key, value);


## Modules and AMD

Use [RequireJS](http://requirejs.org/) for defining and loading modules asynchronously.

### Defining modules

**No**

    var Cat = {
      speak: function () {
        alert('meow');
      }
    };

**Yes**

    define(function () {
      var Cat = {
        speak: function () {
          alert('meow');
        }
      };
      return Cat;
    });

### Requiring dependencies

**No**

    var Cat = {
      speak: function () {
        // Expect jQuery to be accessible globally
        $('#where').text('meow');
      }
    };

**Yes**
    
    define([
        'jquery'
      ], function ($) {
      var Cat = {
        speak: function () {
          // jQuery is explicitly loaded and named as $
          $('#where').text('meow');
        }
      };
      return Cat;
    });


