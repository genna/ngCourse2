## The case for Immutability

One of the more difficult things to manage when structuring an application is managing its state. This is especially true when your application can execute code asynchronously. You execute some piece of code, but something causes it to wait (such as an HTTP request or user input), but when it completes the state it's expecting changed while it was waiting because some other function was executed asynchronously and changed its value.

Dealing with that kind of behavior on a small scale might be manageable, but this can appear all over an application and can be a real headache as the application gets bigger with more interactions and more complex logic.

Immutability attempts to solve this by making sure that any object that's been referenced in one part of the code can't all of a sudden be changed by another part of the code unless they have the ability to rebind it directly.

## JavaScript solutions

Some new features have been added in ES6 that allow for easier implementation of immutable data patterns.

### Object.assign

`Object.assign` lets us merge one object's properties into another one, replacing values of properties with matching names. We can use this to copy an object's values without altering the existing one.

```typescript
let movie1 = {
  name: 'Star Wars',
  episode: 7
};

let movie2 = Object.assign({}, movie1);

movie2.episode = 8;

console.log(movie1.episode); // writes 7
console.log(movie2.episode); // writes 8
```
[View example](plnkr example)

As you can see, although we have some way of copying an object, we haven't made it immutable, since we were able to set the episode's property to 8. The object assign function lets us make this modification within the method call:

```typescript
let movie1 = {
  name: 'Star Wars',
  episode: 7
};

let movie2 = Object.assign({}, movie1, { episode: 8 });

console.log(movie1.episode); // writes 7
console.log(movie2.episode); // writes 8
```
[View example](plnkr example)

But we still haven't solved the problem of potential mutation.

### Object.freeze

`Object.freeze` allows us to disable object mutation.

```typescript
let movie1 = {
  name: 'Star Wars',
  episode: 7
};

let movie2 = Object.freeze(Object.assign({}, movie1));

movie2.episode = 8; // fails silently in non-strict mode,
                    // throws error in strict mode

console.log(movie1.episode); // writes 7
console.log(movie2.episode); // writes 7
```
[View example](plnkr example)

One problem with this pattern however, is how much more verbose our code is and how difficult it is to read and understand what's actually going on with our data with all of the boilerplate calls to `Object.freeze` and `Object.assign`. We need some more sensible interface to create and interact with immutable data, and that's where Immutable.js fits in

## Immutable.js basics

Immutable.js needs to at least provide immutable versions of the two core mutable types in JavaScript, object and array.

### Immutable.Map
`Map` is the immutable version of JavaScript's object structure. Due to JavaScript objects having the concise object literal syntax, it's often used as a key-value store with key being type `string` and value being `any` type. Let's revisit the previous example, but instead use `Immutable.Map`.

```typescript
import * as Immutable from 'immutable';

let movie1 = Immutable.Map<string, any>({
  name: 'Star Wars',
  episode: 7
});

let movie2 = movie1;

movie2 = movie2.set('episode', 8);

console.log(movie1.get('episode')); // writes 7
console.log(movie2.get('episode')); // writes 8
```
[View example](plnkr example)

Instead of creating binding the object literal directly to `movie1`, we pass it as an argument to `Immutable.Map`. This changes how we interact with movie1's properties.

To _get_ the value of a property, we call the `get` method, passing the property name we want, like how we would use an object's array indexer.

To _set_ the value of a property, we call the `set` method, passing the property name and the new value. Note however, that it won't mutate the existing Map object. It returns a new object with the updated property so we need to rebind the `movie2` variable to the new object.

#### Map.merge

Sometimes we want to update multiple properties. We can do this using the `merge` method.

```typescript
let baseButton = Immutable.Map<string, any>({
  text: 'Click me!',
  state: 'inactive',
  width: 200,
  height: 30
});

let submitButton = baseButton.merge({
  text: 'Submit',
  state: 'active'
});

console.log(submitButton.toObject());
```
[View Example](plnkr example)

#### Maps are iterable

Maps in Immutable.js are iterable, meaning that you can `map`, `filter`, `reduce`, etc. each key-value pair in the map.

// quick example here

### Immutable.List

`List` is the immutable version of JavaScript's array structure.

```typescript
interface Movie {
  name: string;
  released: number;
  rating: number;
}

let movies = Immutable.List([
  {
    name: 'The Fellowship of the Ring',
    released: 2001,
    rating: 8.8
  },
  {
    name: 'The Two Towers',
    released: 2002,
    rating: 8.7
  },
]);
```


### Official documentation

For more information on Immutable.js, visit the official documentation at [https://facebook.github.io/immutable-js/](https://facebook.github.io/immutable-js/).