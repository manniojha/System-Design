# Singleton Pattern

```
let instance;
let counter = 0;

class Counter {
  constructor() {
    if (instance) {  throw new Error("You can only create one instance!"); }
    instance = this;
  }

  getInstance() { return this; }
  getCount() { return counter; }
  increment() { return ++counter; }
  decrement() { return --counter; }
}

const singletonCounter = Object.freeze(new Counter());
export default singletonCounter;
```

# Proxy Pattern - Intercept and control interactions to target objects

A proxy object can determine the behavior whenever we're interacting with the object. Proxy objects are commonly used to
log property accesses, validate, format, or sanitize inputs, and so on.
The major use case of Reflect is to provide default forwarding behavior in Proxy handler traps. The Reflect methods provide
the reflective semantics for invoking the corresponding object internal methods. For example, we can call Reflect.get if we
don't wish to redefine the object's behavior

```
const person = {
  name: "John Doe",
  age: 42,
  nationality: "American",
};

const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    if (!obj[prop]) {
      console.log(
        `Hmm.. this property doesn't seem to exist on the target object`
      );
    } else {
      console.log(`The value of ${prop} is ${Reflect.get(obj, prop)}`);
    }
  },
  set: (obj, prop, value) => {
    if (prop === "age" && typeof value !== "number") {
      console.log(`Sorry, you can only pass numeric values for age.`);
    } else if (prop === "name" && value.length < 2) {
      console.log(`You need to provide a valid name.`);
    } else {
      console.log(`Changed ${prop} from ${obj[prop]} to ${value}.`);
      return Reflect.set(obj, prop, value);
    }
  },
});
```

# Provider Pattern

Addresses the challenge of prop drilling in complex applications. Instead
of passing data through multiple layers of components via props, the pattern
allows data to be made accessible to components that need it directly.
This is achieved by wrapping components in a Provider, which makes the
data available to all nested components. It simplifies code maintenance and
eliminates the need to rename props throughout the application when changes
are made. The Provider Pattern streamlines data sharing in large applications,
enhancing code readability and maintainability.

# Prototype Pattern: Share properties among many objects of the same type

The prototype pattern is a useful way to share properties among many objects of the same type.

```
class Dog {
  constructor(name) {
    this.name = name;
  }

  bark() {
    return `Woof!`;
  }
}

const dog1 = new Dog("Daisy");
```

When using ES6 classes, all properties that are defined on the class itself, bark in this case,
are automatically added to the prototype. We can see the prototype directly through accessing the
prototype property on a constructor, or through the `__proto__` property on any instance.

```
console.log(Dog.prototype);
// constructor: ƒ Dog(name, breed) bark: ƒ bark()

console.log(dog1.__proto__);
// constructor: ƒ Dog(name, breed) bark: ƒ bark()
```

The value of `__proto__` on any instance of the constructor, is a direct reference to the constructor's
prototype! Whenever we try to access a property on an object that doesn't exist on the object directly,
JavaScript will go down the prototype chain to see if the property is available within the prototype chain.
The prototype pattern is very powerful when working with objects that should have access to the same properties.
Instead of creating a duplicate of the property each time, we can simply add the property to the prototype,
since all instances have access to the prototype object.
Since all instances have access to the prototype, it's easy to add properties to the prototype even after creating the instances.

```
class Dog {
  constructor(name) {
    this.name = name;
  }

  bark() {
    return `Woof!`;
  }
}

const dog1 = new Dog("Daisy");

Dog.prototype.play = () => console.log("Playing now!");

dog1.play();
```

<img src="./prototype_pattern.avif">

Object.create is a simple way to let objects directly inherit properties from other objects,
by specifying the newly created object's prototype. The new object can access the new properties
by walking down the prototype chain.

```
const dog = {
  bark() {
    return `Woof!`;
  },
};

const pet1 = Object.create(dog);
```

# Observer Pattern: Use observables to notify subscribers when an event occurs

An observable object usually contains 3 important parts:

`observers`: an array of observers that will get notified whenever a specific event occurs
`subscribe()`: a method in order to add observers to the observers list
`unsubscribe()`: a method in order to remove observers from the observers list
`notify()`: a method to notify all observers whenever a specific event occurs

```
class Observable {
  constructor() {
    this.observers = [];
  }

  subscribe(f) {
    this.observers.push(f);
  }

  unsubscribe(f) {
    this.observers = this.observers.filter(subscriber => subscriber !== f);
  }

  notify(data) {
    this.observers.forEach(observer => observer(data));
  }
}

export default new Observable();
```

```
import observable from "./Observable";

function handleClick() {
  observable.notify("User clicked button!");
}

function handleToggle() {
  observable.notify("User toggled switch!");
}

function logger(data) {
  console.log(`${Date.now()} ${data}`);
}

function toastify(data) {
  toast(data);
}

observable.subscribe(logger);
observable.subscribe(toastify);

export default function App() {
  return (
    <div className="App">
      <Button variant="contained" onClick={handleClick}>
        Click me!
      </Button>
      <FormControlLabel
        control={<Switch name="" onChange={handleToggle} />}
        label="Toggle me!"
      />
      <ToastContainer />
    </div>
  );
}
```

## Intersection Observer API

The Intersection Observer API provides a way to asynchronously observe changes in the
intersection of a target element with an ancestor element or with a top-level document's viewport.

- Lazy-loading of images or other content as a page is scrolled.
- Implementing "infinite scrolling" websites, where more and more content is loaded and rendered
  as you scroll, so that the user doesn't have to flip through pages.
- Reporting of visibility of advertisements in order to calculate ad revenues.
- Deciding whether or not to perform tasks or animation processes based on whether or not the user will see the result.
