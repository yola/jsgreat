# jsgreat

[![npm Version](https://img.shields.io/npm/v/jsgreat.svg?style=flat-square)](https://www.npmjs.com/package/jsgreat)
[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](https://github.com/yola/jsgreat/master/LICENSE)

## Table of Contents

* [Introduction](#introduction)
  - [Changes](#changes)
  - [Linting](#linting)
  - [Installation](#installation)
* [Rules](#rules)
  - [Naming](#naming)
  - [Variables](#variables)
    * [Variable statements](#variable-statements)
    * [Unused Variables](#unused-variables)
    * [Undeclared Variables](#undeclared-variables)
    * [Using Variables Before Declaration](#using-variables-before-declaration)
    * [Aligning Assignments](#aligning-assignments)
  - [Constants](#constants)
  - [Functions](#functions)
    * [Anonymous Functions](#anonymous-functions)
  - [Modules](#modules)
    * [Exporting](#exporting)
    * [Importing](#importing)
  - [Promises](#promises)
  - [JSDoc](#jsdoc)
  - [Visibility](#visibility)
  - [Indentation](#indentation)
  - [Manipulating `this` context](#manipulating-this-context)
  - [Curly Braces](#curly-braces)
  - [Strict Mode](#strict-mode)
  - [Coerced Types and Equality](#coerced-types-and-equality)
  - [Trailing Whitespace](#trailing-whitespace)
  - [80 Column Limit](#80-column-limit)
* [License](#license)

## Introduction

This guide outlines the style for Yola's JavaScript code.
When editing existing code, and where possible, said code should be updated to conform to the style guide.

### Changes

Proposed style changes should be discussed in a written format that can referenced.
The style guide must be updated to reflect all changes.

### Linting

Much of this guide can be enforced by using a linter such as [JSHint](https://github.com/jshint/jshint).
The provided [`.jshintrc`](https://github.com/yola/jsgreat/blob/master/.jshintrc) file should be used to help maintain conformity.

### Installation

Install via [npm](https://www.npmjs.com/package/jsgreat):

```bash
$ npm install jsgreat --save-dev
```

Extend the provided `.jshintrc` file:

```json
{
  "extends": "./node_modules/jsgreat/.jshintrc"
}
```

## Rules

Serving as a base, the [Google JavaScript Style Guide](https://google.github.io/styleguide/javascriptguide.xml) should be referenced for topics not addressed below.

### Naming

* Variables, constants, enums, methods, and properties should be written in `lowerCamelCase`.
* Classes and global namespaces should be written in `UpperCamelCase`.
* File names should be written in `hyphenated-lowercase.js`.

### Variables

* When available, the `let` keyword should be used instead of `var`.

#### Variable statements

* Variable statements with multiple assignments should not be used.
* Each variable assignment should exist on its own line.
* Multiple variables without assignments can be declared on a single line.

**Don't do this:**

```javascript
let foo = 'foo',
  bar = 'bar',
  baz = 'baz';
```

**Do this instead:**

```javascript
let foo = 'foo';
let bar = 'bar';
let baz = 'baz';
```

**This is okay, too:**

```javascript
let foo, bar, baz;
```

#### Unused Variables

Unused variables should be removed from the code when they are no longer needed.

Although discouraged, unused function parameters are allowed. This is due to some libraries expecting specific function signatures.

**Don't do this:**

```javascript
const doSomething = function() {
  // foo is never used
  let foo;

  return 'Hello World!';
}
```

**This is allowed:**

```javascript
const doSomething = function(error, data) {
  // data is never used
  if (error) {
    throw error;
  }
});
```

#### Undeclared Variables

All variables must be explicitly declared.

This helps catch errors due to mistyped variable names and copy & pasting.
Additionally, it helps prevent dependence on the global state.
As a result, modules are forced to be explicit about their dependencies.

For example, using an undeclared `$` variable in Backbone views may have unexpected results.
Using the global jQuery variable (`$`) accidentally, assuming it exists, will result in operations affecting the entire DOM, instead of only affecting the view.

#### Using Variables Before Declaration

Variables must be declared before they are used. Hoisting makes it possible to accidentally use a variable before it's declared.

#### Aligning Assignments

Variable assignments shouldn't be aligned by value.

**Don't do this:**

```javascript
let foo      = 'foo';
let bar      = 'bar';
let fizzbuzz = 'fizzbuzz';

let obj = {
  foo     : 'foo',
  bar     : 'bar',
  fizzbuzz: 'fizzbuzz'
};
```

**Do this instead:**

```javascript
let foo = 'foo';
let bar = 'bar';
let fizzbuzz = 'fizzbuzz';

let obj = {
  foo: 'foo',
  bar: 'bar',
  fizzbuzz: 'fizzbuzz'
};
```

### Constants

This section conflicts with the Google JavaScript Style Guide.
The following rules should be followed instead.

* The `@const` tag shouldn't be used.
* When available, the `const` keyword should be used.
* Consider [freezing](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) object constants when applicable.

### Functions

Function expressions should be used instead of function declarations.

**Don't do this:**

```javascript
function foo() {
  /// [...]
};
```

**Do this instead:**

```javascript
let foo = function() {
  /// [...]
};
```

#### Anonymous Functions

Function expressions should be used instead of anonymous functions where applicable.
[IIFEs](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression) shouldn't be used.

**Don't do this:**

```javascript
$('.someElement').click(function(){
  console.log('It was clicked!');
});
```

```javascript
fetchFoo()
  .then(function(foo) {
    // Do something
  });
```

**Do this instead:**

```javascript
const someElementClicked = function(){
  console.log('It was clicked!');
});

$('.someElement').click(someElementClicked);
```

```javascript
const calculateBar = function(foo) {
  // Do something
};

fetchFoo()
  .then(calculateBar);
```

### Modules

Modules should be small, [composable](http://en.wikipedia.org/wiki/Composability), [stateless](http://en.wikipedia.org/wiki/State_(computer_science)), and self contained.

#### Exporting

Functions and primitive types shouldn't be default exports.

**Don't do this:**

```javascript
export default function (foo, bar) {
  // Do something
};
```

**Do this instead:**

```javascript
export const doSomething = function(foo, bar) {
  // Do something
};

export default {
  doSomething
};
```

#### Importing

The members of module imports shouldn't be detached from their parent objects. This allows for mocks and stubs to be used during testing, and may allow for easier refactoring.

**Don't do this:**

```javascript
import {actions, requests} from `service-client`;

const {fetchFoo} = requests;
const {calculateBar} = actions;

let bar = fetchFoo().then(calculateBar);
```

**Do this instead:**

```javascript
import {actions, requests} from `service-client`;

let bar = requests.fetchFoo()
  .then(actions.calculateBar);
```

### Promises

Asynchronous operations should be written using promises.

* Libraries reliant on promises should adhere to the [Promises/A+ standard]([Promises](http://promises-aplus.github.io/promises-spec/)).
* [Bluebird](http://bluebirdjs.com) is Yola's promise implementation of choice.
* ["You're missing the point of promises"](http://domenic.me/2012/10/14/youre-missing-the-point-of-promises/) by Domenic Denicola
  - A valued article discussing the philosophy behind promises

### JSDoc

JSDoc shouldn't be used. Rules referring to JSDoc usage in the Google JavaScript Style Guide should be ignored.

### Visibility

Private and protected members should be prefixed with an underscore (`_`).

**Don't do this:**

```javascript
class Foo {
  /** @private */
  doSomething() {
    // Do something
  }
}
```

**Do this instead:**

```javascript
class Foo {
  _doSomething() {
    // Do something
  }
}
```

### Indentation

Two space indentation should be used for all JavaScript and JSON files.

### Manipulating `this` context

Instead of storing context within a variable, functions should be called within the required scope.

Below are examples of code where a property needs to be accessed from a seperate context.

**Don't do this:**

```javascript
class MyClass {
  constructor() {
    this.message = 'Hello World!';
  }

  fetchFoo: function(){
    // Context is stored in a variable
    var that = this;

    const getMessage = function(){
      return that.message;
    };

    return fetch('some/ajax/request')
      .then(getMessage);
  }
}
```

**Do either of these instead:**

```javascript
class MyClass {
  constructor() {
    this.message = 'Hello World!';
  }

  fetchFoo: function(){
    return fetch('some/ajax/request')
      // An arrow function is used to maintain context.
      .then(() => this.message);
  }
}
```

```javascript
class MyClass {
  constructor() {
    this.message = 'Hello World!';
  }

  fetchFoo: function(){
    let getMessage = function(){
      return this.message;
    };

    // The function is bound to the given context.
    getMessage = getMessage.bind(this);

    return fetch('some/ajax/request')
      .then(getMessage);
  }
}
```

```javascript
class MyClass {
  constructor() {
    this.message = 'Hello World!';
  }

  fetchFoo: function(){
    // The message is assigned to a local variable
    const message = this.message

    const getMessage = function(){
      return message;
    };

    return fetch('some/ajax/request')
      .then(getMessage);
  }
}
```

### Curly Braces

Curly braces are required around blocks in loops and conditionals.

**Don't do this:**

```javascript
if (foo)
  console.log('bar');
```

**Do this instead:**

```javascript
if (foo) {
  console.log('bar');
}
```

### Strict Mode

* All modules should be have strict mode enabled.
* ES6 modules have strict mode enabled by default.
* Non-ES6 modules should have `'use strict';` placed at the top of the file.

### Coerced Types and Equality

Strict comparison (`===`) should be used instead of abstract comparison (`==`).

### Trailing Whitespace

Lines shouldn't have whitespace at the end.

### 80 Column Limit

Lines should not exceed 80 characters.

In specific cases, long lines can be ignored by the linter.

**Don't do this:**

```javascript
let longTestPermalinkUrl = 'https://example.com/v1/en/2016/03/14/assets/images/1234.jpg';
```

**This is allowed:**

```javascript
let longTestPermalinkUrl;
longTestPermalinkUrl = 'https://example.com/v1/en/2016/03/14/assets/images/1234.jpg'; // jshint ignore:line
```

## License

Copyright &copy; 2016 [Yola](http://yola.com).
<br>Released under the [MIT License](https://github.com/yola/jsgreat/blob/master/LICENSE).
