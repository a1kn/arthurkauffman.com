---
title: "Javascript Arrow Functions, Parentheses, Curly Braces and Other Nonsense"
date: 2020-03-19T12:21:01-04:00
draft: false 
---

Javascript is funky. Wrapping my head around nested functions feels a little
nuts after learning a language like Ruby.

Complicating it even more is the shift from ES5 to ES6, where ES5 always used
the `function` keyword and ES6 includes fancy new `arrow functions`. 

Big difference, besides less typing of the word 'function'? Implicit return.
Arrow functions, declared with a weird arrow rocket (`=>`), can implicitly return
their value when the function is called. This means writing a `return` statement
is no longer necessary if your function contains only a single expression.

But what does this look like in actual code? Take a look at this simple function
to add together all the digits of a number and return a number:

```javascript
var sum = function (integer) {
  return (
    String(integer).split('').reduce(
      function (accumulator, value) {
        return Number(accumulator) + Number(value);
      }
    )
  );
};
```

Code here is pretty straightforward: the variable `sum` is assigned to a
function which converts the integer to a string, splits it into an array, and
then calls the `reduce` method. The `reduce` method executes a function that
converts the string digits to numbers and then returns a single reduced value,
the sum of the digits.

OK, so let's give this a shot using arrow functions.

```javascript
const sum = (integer) => {
  return (
    String(integer).split('').reduce(
      (accumulator, value) => {
        return Number(accumulator) + Number(value);
      }
    )
  );
};
```

Looking good. We replaced the outdated and sometimes dangerous `var` with
`const` and we took out the `function` declarations.

But! We can also take out the implicit `return` statements because in the case
of the function being executed for the `reduce` method and the function
returning our sum, there is only one expression being returned.

```javascript
const sum = (integer) => {
  (
    String(integer).split('').reduce(
      (accumulator, value) => {
        Number(accumulator) + Number(value);
      }
    )
  );
};
```

At this point though, our code simply returns `undefined`. Why is that?

It's because if Javascript sees curly braces, it is expecting multiple
expressions and thus needs an explicit `return` statement. Implicit return only
works with no curly braces.

So lets take them out!

```javascript
const sum = (integer) => 
  (
    String(integer).split('').reduce(
      (accumulator, value) => 
        Number(accumulator) + Number(value);
    )
  );
;
```

That didn't work at all. If you try to run this code, you will get at least
three syntax errors, complaining about missing/unexpected parentheses.

Basically, Javascript is expecting an implicit return to all be on one line. The
semicolons that are still in our code are suggesting some kind of multi-line
thing going on. What if we take them all out, except for the one at the end?

```javascript
const sum = (integer) => 
  (
    String(integer).split('').reduce(
      (accumulator, value) => 
        Number(accumulator) + Number(value)
    )
  )
;
```

Tada! Finally we have usable code again. Let's refactor just slightly by moving
some stuff onto the same lines:

```javascript
const sum = (integer) => (
  String(integer).split('').reduce((accumulator, value) => (
    Number(accumulator) + Number(value)
  ))
);
```

And that's looking much better. If we don't care about long lines of code, we
can even make this an actual one-line expresion by taking out some of these
parentheses...

```javascript
const sum = (integer) => String(integer).split('').reduce((accumulator, value) => Number(accumulator) + Number(value));
```

### Recap

To recap:

- arrow functions can have an implicit return if you have a single expression in
  your function
- implicit returns *can not* use curly braces
- code inside parentheses is basically one line of code, and can't have
  semi-colons until the end
