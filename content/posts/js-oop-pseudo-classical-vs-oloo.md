---
title: "Object-Oriented Javascript Methods: Comparing Pseudo-Classical, OLOO, 
and the 2015 Class Syntax"
date: 2020-04-19T17:04:40-04:00
draft: false 
---
Javascript's prototypal inheritance is simple; so simple, in fact, that it seems
at time overly-complicated. But at it's heart, JS has only one construct -- 
objects. Objects can be linked with other objects in a prototype chain.

The focus of this article is not to go into detail about the exact
implementation of JS's OOP. MDN's stellar documentation, as always, provides
in-depth and easily parsed documentation. If you're trying to wrap your head
around JS's OOP implementation, start with [MDN's Object-oriented JavaScript for
beginners](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object-oriented_JS)
and when you're ready, make sure to read [Inheritance and the prototype
chain](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain). 

I also won't go into depth about *all* the ways in which JS OOP might be
implemented in the wild. [John
Dugan](https://john-dugan.com/object-oriented-javascript-pattern-comparison/)
does a stellar job of taking us through a myriad of approaches.

Instead, I'm comparing two approaches that many agree are best-practice: the
pseudo-classical model, and Kyle Simpson's OLOO model. I'll show examples of the
classical model using more conventional code, and the updated 2015 syntax using
`class`.

First up, let's look at the most common implementation of JS's OOP, the
so-called pseudo-classical model, which looks similar in syntax to many
classical approaches to OOP, such as those found in Ruby, Python, and Java.

### Pseudo-Classical Example:

```javascript
function Animal(name) {
  this.name = name;
}
// constructor function is defined

function Dog(name) {
  Animal.call(this, name);
}
// a second constructor function is defined, utilizing `call` to call the
// `Animal` function with `this` and `name` argument

Dog.prototype = Object.create(Animal.prototype);
// `Dog` prototype is set to be a copy of `Animal` prototype

Dog.prototype.constructor = Dog;
// fix the constructor, so that objects created from `Dog` will correctly show
// `Dog` as the constructor

Dog.prototype.barks = function() {
  console.log(`${this.name} barks!`);
}
// Add a `bark` method to the `Dog` prototype

const sparky = new Dog('Sparky');
sparky.bark();
```

#### Advantages:
- `constructor` works and can be checked by calling `sparky.constructor` which
  will return `Dog`.
- clear to see what's happening in the code: two constructor functions are
  declared, and then one's prototype is set to the other's, which sets up a link
  in the prototype chain

#### Disadvantages:
- can be hard to read, especially if program gets larger
- could be argued that the implementation of constructor functions and then 
  objects being created from those functions is needlessly complex


### Example Using 2015's Class Syntax:

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
}
// constructor function defined

class Dog extends Animal {
  constructor(name) {
    super(name);
  }

  barks() {
    console.log(`${this.name} barks!`);
  }
}
// a second constructor function defined, using `super` to pass arguments to the
first constructor function, and implementing the `bark` method

const sparky = new Dog('Sparky');
sparky.barks();
```

#### Advantages:
- has all the functionality of the first example, including `constructor` and
  proper prototype chaining
- easy to read and write. Wowzers, no need to fiddle with prototypes or
  constructors!
- `super` replaces the need for `call`

#### Disadvantages:
- un-JS-like syntax; under the hood, all the same prototype stuff is going on,
  but it *feels* very un-Javascript-ey
- new syntax, so not supported in IE 11 and earlier
- has the same problem with complexity as the first example, constructor 
  functions need to be declared in order to instantiate objects


### OLOO (Objects Linked to Other Objects) Example: 

```javascript
const Animal = {
  init(name) {
    this.name = name;
    return this;
  },
};
// notice this is an object, and not a function
// also notice the `init` method, which sets initial values

const Dog = Object.create(Animal);
// creating an object from the first object, which establishes a link in the
// prototype chain

Dog.bark = function() {
  console.log(`${this.name} barks!`);
};
// add a method to the `Dog` object

const sparky = Object.create(Dog).init('Sparky');
// creates a new object from `Dog` and passes in the name `Sparky`

sparky.bark();
```

#### Advantages: 
- no constructor functions necessary
- no need to worry about `call`
- greatly simplified code

#### Disadvantages:
- can be hard to read/understand for someone coming from another language
- doesn't [care about](https://frontendmasters.com/courses/advanced-javascript/oloo-questions/)
  `constructor`, so `sparky.constructor` actually returns
  `Object`


## Summary

- Javascript's prototypal nature brings great flexibility into the way new
  objects are instantiated, which means that there are numerous approaches to JS
  OOP
- Pseudo-classical model uses constructor functions and the `new` keyword to
  instantiate new objects that are linked in the prototype chain
- OLOO uses `Object.create` to create new objects from other objects and largely
  does away with the classical idea of hierarchal inheritance

## Recommendations
- use OLOO as much as possible
- if you must use pseudo-classical, use `class` syntax if you don't care about 
  IE 11 browser support, which is less of a worry with every passing year
- otherwise, use pseudo-classical

