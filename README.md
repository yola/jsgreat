Our JavaScript code will (mostly) follow the [Google JavaScript Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml). A few slight variations and additions are laid out below. There will also be exceptions to these rules in some existing code, but where possible, existing code should be updated as it is edited to conform to the style guide.

Many of these options will be enforced by [this .jshintrc file](https://github.com/yola/wiki/blob/master/Files/.jshintrc). This file can be used by tools that support JSHint integration. For most projects, it is strongly recommended to use Grunt and the grunt-contrib-jshint plugin to run jshint.

The following additions and variations will be enforced:

## Strings
The guide [prefers `'` over `"`](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml?showone=Strings#Strings). We only require that quotes are used consistently within the file. This will be [enforced by jshint](http://www.jshint.com/docs/options/#quotmark).

## Comments
The guide [requires JSDoc comments for modules, classes, and functions](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml?showone=Comments#Comments). We do not require any comments, and prefer that comments are reserved for explaining surprising or unexpected code.

## Indentation
Prefer 2-space indentation for all Javascript and JSON files.

## Multiple Vars
Prefer multiple `var` statements, each on their own line, for any variables that have assignments. Multiple variables without assignments can be `var`ed together on a single line.

**Don't do this:**
```javascript
var foo = "foo",
  bar = "bar",
  baz = "baz";
```
**Do this instead:**
```javascript
var foo = "foo";
var bar = "bar";
var baz = "baz";
```
**This is okay, too:**
```javascript
var foo, bar, baz;
```

## Function Declarations vs. Function Expressions

Prefer `var`ed function expressions over function declarations.

**Don't do this:**
```javascript
function() {
   function foo() { ... };
}();
```
**Do this instead:**
```javascript
function() {
  var foo = function() { ... };
}();
```

## Aligning Assignments

Variable assignments should not be aligned by value.
**Don't do this:**
```javascript
var foo      = "foo";
var bar      = "bar";
var fizzbuzz = "fizzbuzz";

var obj = {
  foo     : "foo",
  bar     : "bar",
  fizzbuzz: "fizzbuzz"
};
```
**Do this instead:**
```javascript
var foo = "foo";
var bar = "bar";
var fizzbuzz = "fizzbuzz";

var obj = {
  foo: "foo",
  bar: "bar",
  fizzbuzz: "fizzbuzz"
};
```

## Anonymous Callback Functions

Prefer `var`ed function expressions over anonymous functions for callbacks where it makes sense.

**Don't do this:**
```javascript
$(".someElement").click(function(){
  console.log("It was clicked!");
});
```
**Do this instead:**
```javascript
var someElementClicked = function(){
  console.log("It was clicked!");
});
$(".someElement").click(someElementClicked);
```
This is especially important in `new`ed objects like Backbone Models and Views, where scope is important, and callbacks often reference other properties on the instance.

## Manipulating `this` context

Prefer calling functions in the required scope vs. saving `this` in a `that` or `self` variable.

**Don't do this:**
```javascript
// Code that needs access to local variables and `this` in an anonymous callback
var MyModule = {
  message: "Hello World!",
  doGetAndCallback: function(callback){
    var that = this;
    $.get("some/ajax/request", function(){
      callback(that.message);
    });
  }
};
```
**Do either of these instead:**
```
// Save just the info you need
var MyModule = {
  message: "Hello World!",
  doGetAndCallback: function(callback){
    var message = this.message;
    $.get("some/ajax/request", function(){
      callback(message);
    });
  }
};

// Change the scope to make sure it is called in the proper context
var MyModule = {
  message: "Hello World!",
  doGetAndCallback: function(callback){
    $.get("some/ajax/request", _.bind(function(){
      callback(this.message);
    }, this));
  }
};
```
**Or, better yet, return a Deferred/Promise, and let the caller setup the callback on their end:**
```javascript
var MyModule={
  message: "Hello World!",
  doGet: function(){
    return $.get("some/ajax/request");
  }
};
```

## Curly Braces

Curly braces are required around blocks in loops and conditionals. This is [enforced by jshint](http://www.jshint.com/docs/options/#curly).

**Don't do this:**
```
if(foo)
  console.log("bar");
```
**Do this instead:**
```
if(foo){
  console.log("bar");
}
```

## Strict Mode

All modules should be wrapped in IIFEs or RequireJS CommonJS define calls, and use strict mode. This will be enforced by JSHint
```
(function(){
  "strict mode";
  //Code goes here
})();
```
Or
```
define(function(require, exports, module)){
  "strict mode";
  //code goes here
});
```

## Unused Variables

Unused variables should be removed from the code when they are no longer needed. This will be [enforced by jshint](http://www.jshint.com/docs/options/#unused).

Because some libraries expect function arguments with specific names and signatures, function parameters that are not used will not be ignored.

**Don't do this:**
```
var x;  //x is never used
```
**But this is okay:**
```
define(function(require, exports, module){
  //module is never used
});
```

## Coerced Types and Equality

Prefer `===` over `==`. This will be [enforced by JShint](http://www.jshint.com/docs/options/#eqeqeq).

## Undefined Variables

All variables must be explicitly defined. This option will be [enforced by JSHint](http://www.jshint.com/docs/options/#undef).

This is a simple way to catch mistyped variable names and copy/paste errors, but it also helps to prevent over-dependence on the global namespace, and forces modules to be more explicit about their dependencies.

One specific example of why this can be important is in Backbone views, which use a scoped `$` function to find elements inside the view. If you accidentally use the global `$(".selector")` instead of `this.$(".selector")`, you're searching the full DOM instead of just your view.

## Using Variables Before Definition

Variables must be defined earlier in the code than they are used. (Hoisting makes it possible to accidentally use a variable before it's defined.) This will be [enforced by JSHint](http://www.jshint.com/docs/options/#latedef).

## Trailing whitespace

Lines shouldn't have whitespace at the end. We've always enforced this. Now [JSHint will enforce it](http://www.jshint.com/docs/options/#trailing).
