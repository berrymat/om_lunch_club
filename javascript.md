# JavaScript / ES6

1. const/let
2. template strings
3. spread operator ...
4. fat arrow functions =>
5. simplified object syntax
6. destructuring
7. map/reduce & friends
8. immutable & pure

---

# const/let

1. Use `const` by default
2. Use `let` if you have to rebind a variable
3. Use `var` to signal untouched legacy code

> `var` declarations are globally scoped or function scoped while `let` and `const` are block scoped. `var` variables can be updated and re-declared within its scope; `let` variables can be updated but not re-declared; `const` variables can neither be updated nor re-declared."

Whilst `const` is good, realize that:

```javascript
const u = 10;
u = 20; // This will throw a TypeError
const v = { a: 1, b: 2, c: 3 };
v.c = 42; // This will work just fine
v = { a: 10, b: 20, c: 30 }; // This will throw a TypeError
const w = [1, 2, 3];
w[1] = 20; // This will work just fine
w = [10, 20, 30]; // This will throw a TypeError
```

---

# template strings

```javascript
const animal1 = "fox";
const animal2 = "dog";

const saying = `The quick brown ${animal1}
  jumps over the lazy ${animal2}`;
```

1. Easier than manually concatenating
2. Multiline
3. Can embed " or '

Interesting?

```javascript
const foo = function(value) {
  console.log(value);
};
foo`template string`;
```

---

# spread operator ...

## arrays

```javascript
const oneToFive = [1, 2, 3, 4, 5];
const oneToSix = [...oneToFive, 6];

const sixToNine = [6, 7, 8, 9];
const fiveToNine = [5, ...sixToNine];

const oneToTen = [...oneToFive, ...sixToNine];
```

## objects

```javascript
const oneTwoThree = { one: 1, two: 2, three: 3 };
// "Add" property
const oneTwoThreeFour = { ...oneTwoThree, four: 4 };

// "Update" properties
const update = { ...oneTwoThreeFour, one: "one", four: "four" };

// Two objects
const two = { ...update, ...oneTwoThree };
```

## more in destructuring...

---

# fat arrow functions =>

```javascript
// Using function
const addOld = function(x, y) {
  return x + y;
};
// Equivalent to
const add = (x, y) => {
  return x + y;
};
addOld(2, 2);
add(2, 2);

// add can be simplified - single line with return
const addSimple = (x, y) => x + y;

// another fat function...
const add5 = (x) => x + 5;
// Can also be simplified - single argument
const add5Simple = x => x + 5;

// Note if you want to return object use parentheses
const returnObject = x => ({ value: x });
```

## no separate this

```javascript
function Person() {
  this.age1 = 10;
  this.age2 = 20;

  setInterval(function growUp() {
    this.age1++;
    console.log("age1", this.age1);
  }, 1000);

  setInterval(() => {
    this.age2++;
    console.log("age2", this.age2);
  }, 1000);
}

const p = new Person();
```

Interesting?

Currying - a technique from functional programming.

```javascript
// Let's rewrite our add function
const add = x => y => x + y;
// This is really...
// const add = function(x) {
//   return function(y) {
//     return x + y;
//   }
// }
add(2)(2);

// Now if we want to write our add5 function
const add5 = add(5);
add5(10);
```

---

# simplified object syntax

```javascript
const first = "one";
const second = "two";

const numbers = {
  first: first,
  second: second
};

// Can be simplified
const numbersSimple = {
  first,
  second
};
```

Dynamic property keys

```javascript
const key = "first";
const value = "second";

const dynamicKey = {
  [key]: value
};
```

---

# destructuring

## objects

```javascript
const coords3D = {
  x: 27,
  y: 42,
  z: 73
};

// Destructure
const { x, y } = coords3D;
// Is equivalent to:
// const x = coords3D.x;
// const y = coords3D.y;

// Destructure with spread
const { z, ...coords2D } = coords3D;
// Note: you can use this technique to "delete"
// a property from an object.
```

## arrays

```javascript
const fibonacci = [1, 1, 2, 3, 5, 8];

// Destructure
const [first, second] = fibonacci;
// Is equivalent to:
// const first = fibonacci[0];
// const second = fibonacci[1];

// Destructure with spread
const [head, ...rest] = fibonacci;
```

## function arguments

```javascript
const coords3D = {
  x: 27,
  y: 42,
  z: 73
};

const goUp = ({ z, ...rest }, v) => ({ ...rest, z: z + v });

goUp(coords3D, 10);

// Not really destructuring,
// but variable numbers of function arguments

const sumIt = (...numbers) => {
  let sum = 0;
  for (i = 0; i < numbers.length; i++) {
    sum += numbers[i];
  }
  return sum;
};
sumIt(1, 2, 3, 4, 5);
```

---

# map/reduce & friends

Not really ES6, but useful to know!

## map

When you want to perform an operation on each member of an array and get an array.

```javascript
const gamePieces = [
  { x: 10, y: 20, z: 30 },
  { x: 30, y: 25, z: 10 },
  { x: 20, y: 10, z: 20 },
  { x: 25, y: 30, z: 0 }
];

const goUp = ({ z, ...rest }, v) => ({ ...rest, z: z + v });

const newPositions = gamePieces.map(piece => goUp(piece, 10));

// We can use our currying here by redefining goUp
const goUpCurry = v => ({ z, ...rest }) => ({ ...rest, z: z + v });
const newPositions2 = gamePieces.map(goUpCurry(10));
```

## reduce

When you want to perform an operation on each member of an array and different structure.

```javascript
// Classic example
const numbers = [1, 2, 3, 4];
const total = numbers.reduce((state, n) => state + n, 0);

// Another one
const moves = [10, 5, -8, 20];
const position = { x: 10, y: 20, z: 30 };
const newPosition = moves.reduce((state, v) => goUp(state, v), position);

// Combination
const positions = [
  { x: 10, y: 20, z: 30 },
  { x: 30, y: 25, z: 10 },
  { x: 20, y: 10, z: 20 },
  { x: 25, y: 30, z: 0 }
];
const newPositions = moves.reduce(
  (state, v) => state.map(goUpCurry(v)),
  positions
);

// Or even...
const moveActions = [
  { type: "x", value: 10 },
  { type: "z", value: -5 },
  { type: "y", value: 20 }
];
const reducer = (state, action) => {
  const { type, value } = action;
  switch (type) {
    case "x":
      return { ...state, x: state.x + value };
    case "y":
      return { ...state, y: state.y + value };
    case "z":
      return { ...state, z: state.z + value };
    default:
      return state;
  }
};
const newPosition = moveActions.reduce(reducer, position);
```

## friends

Array.filter - to get subset of array

```javascript
const positions = [
  { x: 10, y: 20, z: 30 },
  { x: 30, y: 25, z: 10 },
  { x: 20, y: 10, z: 20 },
  { x: 25, y: 30, z: 0 }
];
const highPositions = positions.filter(p => p >= 20);
```

Array.find - to get one matching element

```javascript
const positions = [
  { x: 10, y: 20, z: 30 },
  { x: 30, y: 25, z: 10 },
  { x: 20, y: 10, z: 20 },
  { x: 25, y: 30, z: 0 }
];
const atGroundLevel = positions.find(p => p === 0);
```

---

# immutable & pure

Really not directly about ES6, but a good idea!

- Immutable is about not directly updating things, so `const` helps.
- Pure functions are those that only depend on their arguments and given the same argument values will always return the same results. They have no `side-effects`.

```javascript
// Consider this...
// If we look back to our goUp function, a critisism of this might
// be that it is innefficient because it creates a whole new object.
// We could rewrite it as...
const position = { x: 1, y: 2, z: 3 };
const badGoUp = v => {
  position.z += v;
};
badGoUp(10);
// Although this is a very trivial example - why is this bad?
```
