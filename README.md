ES7 Autobinder
==============

ES7 Autobinder allows you to declaratively and automatically bind methods on ES6 classes to `this` when called. It uses ES7 decorators to mark classes and methods, and so requires an ES7-capable environment with the following features:

* Classes
* Decorators
* Symbols
* Rest Arguments

The [Babel](http://babeljs.io/) project with [stage 1 experimental features](http://babeljs.io/docs/usage/experimental/) enabled and the [Babel polyfill](http://babeljs.io/docs/usage/polyfill/) supplied can provide this.

Examples
--------

Say you have a class with a method you always want to be bound to the instance of the class.

```javascript
class Clicker {
  constructor(selector) {
    this.onClickText = "clicked!";
    $(selector).click(this.handleClick);
  }

  handleClick() {
    console.log(this.onClickText);
  }
}

const instance = new Clicker(".element");
```

Since `handleClick` is called from a jQuery event handler, `this` is the element that was clicked, and not the `Clicker` instance. You can fix this manually by either binding to the target when referring to the method:

```javascript
$(selector).click(this.handleClick.bind(this));
```

or by manually binding the method to the instance:

```javascript
this.handleClick = this.handleClick.bind(this);
$(selector).click(this.handleClick);
```

However, when there are many methods on the class you need to bind in this fashion, it's easy to forget, or to mistakenly create many such bound functions (such as in a React `render` method).

ES7 Autobinder allows you to use decorators to declaratively specify methods that should be automatically bound to the instance.

```javascript
const { autobound } = require("es7-autobinder");

class Clicker {
  constructor(selector) {
    this.onClickText = "clicked!";
    $(selector).click(this.handleClick);
  }

  @autobound
  handleClick() {
    console.log(this.onClickText);
  }
}

const instance = new Clicker(".element");
```

In addition to decorating the method, you must either decorate the class:

```javascript
const { autobound } = require("es7-autobinder");

@autobound
class Clicker {
  constructor(selector) {
    this.onClickText = "clicked!";
    $(selector).click(this.handleClick);
  }

  @autobound
  handleClick() {
    console.log(this.onClickText);
  }
}

const instance = new Clicker(".element");
```

or manually call `autobindMethods` in the class's constructor:

```javascript
const { autobound, autobindMethods } = require("es7-autobinder");

class Clicker {
  constructor(selector) {
    autobindMethods(this);
    this.onClickText = "clicked!";
    $(selector).click(this.handleClick);
  }

  @autobound
  handleClick() {
    console.log(this.onClickText);
  }
}

const instance = new Clicker(".element");
```