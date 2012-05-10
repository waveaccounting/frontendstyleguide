# Frontend Style Guide

## Software Development Infrastructure

This document provides an overview of the infrastructure we have in place for frontend development.


### Directory Structure

We are using the following directory layout:

    +-static/
      +-js/
      | +-app/          # Source code
      |   +-templates/  # JavaScript templates
      |     +-...
      +-fixtures/       # Fixtures for testing
      |
      +-lib/            # Third-party modules for app
      |
      +-reports/        # JUnit, coverage, lint reports
      |
      +-spec/           # Jasmine specs
      |
      +-tools/          # Build script, spec runner, etc.

Each component will be described in detail in this document.


### CoffeeScript

All new modules are implemented using [CoffeeScript](http://coffeescript.org/). CoffeeScript is preferred
over JavaScript because it allows modules to be written in a faster and safer way.


### Module System

Frontend modules are defined and loaded using [RequireJS](http://requirejs.org/). RequireJS is an implementation
of the Asynchronous Module Definition (AMD) API.

The template for defining a module is as follows:

    define (require, exports, module) ->
      # Load a dependency
      A = require('a')

      # Create the new module
      B = 
        doSomething: ->
          # ...

      # Export public module
      exports = B


The usage of `exports` is similar to the CommonJS spec.


### Testing

Testing is done using Jasmine and [Sinon](http://sinonjs.org/). 

All specs should go into the `spec` folder and the filename must end with `.spec.coffee`. Otherwis they will not
be picked up by the spec runner.

To accommodate for AMD, begin each spec with the `define` function.

**Example:**

    define (require, exports, module) ->
      A = require('a')
      
      describe 'A', ->
        
        # Setup
        beforeEach ->
          @a = new A()

        # Teardown
        afterEach ->
          # Run any cleanup if necessary
          @a.cleanUp()

        it 'should return 1 when foo is called', ->
          expect(@a.foo()).toBe(1)


Use Sinon for spying.

**Example:**

    define (require, exports, module) ->
      A = require('a')
      
      describe 'A', ->
        
        beforeEach ->
          @spy = sinon.spy(A.prototype, 'bar')
          @a = new A()

        afterEach ->
          @spy.restore()

        it 'should call bar once when foo is called', ->
          @a.foo()
          expect(@spy).toHaveBeenCalledOnce()


#### Headless Spec Runner

There is a headless spec runner in the `tools` folder that can run Jasmine specs without a browser.

You will need to install Node.js and the following requirements (via `npm`).

    $ sudo npm install -g coffee-script requirejs jasmine-node jsdom dive optimist


Run the specs using the `tools/runspecs.coffee` script:

    $ tools/runspecs.coffee

Use the help command to see all the options:

    $ tools/runspecs.coffee help

##### Examples

Generate **JUnit XML** reports:

    $ node tools/runspecs.js --junitreport

The reports are under the reports folder.

Run a **single spec**:

    $ node tools/runspecs.js common
    $ node tools/runspecs.js common --junitreport

Run verbose with colour output:

    $ node tools/runspecs.js -vc


### Building/Optimizing

Optimization is handled using the `r.js` script that comes with RequireJS.

Run the `tools/build.sh` script inside the js directory:

    $ static/js/tools/build.sh

This will create a new folder `js/built` with all of the optimized scripts.

**Note:** This build step must be run before the `collectstatic` command.

e.g. Running a server with built modules

    $ static/js/tools/build.sh
    $ ./manage.py collectstatic --noinput
    $ ./manage.py compress --force
    $ ./manage.py runserver 0.0.0.0:8000 --insecure


### Structural Patterns

There are four patterns we are using in the application.

1. Facade
2. Mediator
3. MV*
4. Mixins


#### Facade

The facade pattern allows us to group commonly used modules (e.g. jQuery, Zepto, Underscore) behind an API.
Theoretically this will allow us to switch implementations without affecting the rest of the system, as
long as the API remains consistent. For example, we can switch jQuery with Zepto and just add in some of the 
missing features (such as Deferreds) because returning the `$` object.


Example of using the Facade:

    Facade = require('facade')
    $ = Facade.$

    # This could be jQuery or Zepto... or something else?
    $('#foo').show()


#### Mediator

The mediator acts as a central area where modules can indirectly communicate with each other using
`subscribe` and `publish` functions on channels of interest.

This removes inter-modular dependencies from disparate modules.

    
##### Example

**Module A**

    define (require, exports, module) ->
      Mediator = require('mediator')

      class A
        constructure: (@name) ->

        doSomething: ->
          Mediator.publish('somethingDone', @)
      
      exports = A

**Module B**

    define (require, exports, module) ->
      Mediator = require('mediator')

      class B
        constructor: ->
          Mediator.subscribe('somethingDone', @handleSomething)

        handleSomething: (who) =>
          console.log('Something was done by ' + who.name)
      
      exports = B

**Runtime**

    define (require, exports, module) ->
      A = require('a')
      B = require('b')
    
      a = new A()
      b = new B()
    
      # This will cause b.handleSomething to be called
      a.doSomething()


Notice that even though `A` and `B` are not dependent on each other, an event from A indirectly affected B.


#### MV*

MV* is done using the [Backbone](http://documentcloud.github.com/backbone/) library.

All of our UIs need to be built using Models and Views.

**Templates** are rendered using [Handlebars](http://handlebarsjs.com/) and are loaded 
asychronously using the `text` plugin for RequireJS.

Example:

    TextTemplate = require('text!templates/foo.html')


##### Data-Binding

Data-binding is handled using the [Synapse](http://bruth.github.com/synapse/docs/) library.


#### Mixins

The mixin pattern allows us to define mixin objects with reusable functions that can be copied to other classes.

An example where this can be useful is in View classes. A navigation mixin with `open`, `close`, and `toggle`
functions can be shared among multiple views.

**mixin.coffee**

    # ... snip

    Mixins.Navigation =
      open:
        @$el.show()

      close:
        @$el.hide()

      toggle:
        @$el.toggle()


**Menu**

    # ... snip
    class Menu extends Backbone.View
      @include Mixins.Navigation
    


**Runtime**

    menu = new Menu()
    menu.toggle() # Copied from the Navigation mixin


##### Mixins Outside of Backbone Classes

In our `vendor.coffee` module we augment the Backbone classes with the `include` function.

If you need to create new classes that do no subclass from Backbone classes, you can reuse the install
function from the `Facade` module.

Example:

    Facade = require('facade')
    include = Facade.include

    class MyModule
      # Need to call with this object
      include.call @, Mixins.Navigation

    myModule = new MyModule()
    myModule.toggle()

