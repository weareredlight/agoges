# Functional Programming in Javascript

## 1 Functional Programming

- Computation is made through evaluation of mathematical functions
- Declarative programming paradigm
- Favor expressions/declarations over statements

## 2 Why FP in Javascript?

- Object-oriented JS get tricky
- First class citizen functions
- ES6

## 3 How?

- side-effects ❌
- pure functions ✅
- loops ❌
    - reduce ✅
    - map ✅
- function composition ✅

### 3.1 Side effect

Any application state change that is observable outside the called function other than its return value

#### 3.1.1 Most common side-effects

- Modifying any external variable or object property - object
- Writing to the network - network server
- console.log - javascript console
- Writing to a file - file

#### 3.1.2 Isolate side-effects

Separate your side effects from your program logic

- extend ✅
- refactor ✅
- debug ✅
- maintain ✅ 
- test ✅

### 3.2 Pure functions

Given the same input, will always return the same output

input1 → output1

Side effects ❌

#### 3.2.1 Impure

```javascript
let state = 10;

function isValid() {
    if (state == 10)
        return 'valid';
    else 
        return 'invalid';
}
```

- Inconsistent output ❌
- Side effects ❌

#### 3.2.2 Pure

```javascript
function isValid(state) {
    if (state == 10)
        return 'valid';
    else
        return 'invalid';
}

isValid(10) // 'valid'

isValid(11) // 'invalid'
```

- Consistent output ✅
- No side effects ✅

#### 3.2.3 How (to create pure functions)?

- Add dependencies as arguments
- Erase side effects

##### 3.2.3.1 Avoid loops

- map ✅
- reduce ✅
- filter ✅
- forEach ❌
- for ❌
- for in ❌

**map**

❌
```javascript
const data = [1, 2];

function multArrBy2(array) {
  const newArr = [];

  for (let i = 0; i < array.length; i++) {
    newArr.push(array[i] * 2);
  }

  return newArr;
}

console.log(multArrBy2(data)) // [2, 4] 
```

✅
```javascript
function multiply(a) {
    return function(b) {
        return a * b;
    }
}
const multiplyBy2 = multiply(2);

console.log(
    data.map(multiplyBy2)
); // [2, 4]
```

**reduce**

```javascript
const array1 = [1, 2, 3, 4];
const reducer = (accumulator, currentValue) => accumulator + currentValue;

// 1 + 2 + 3 + 4
console.log(array1.reduce(reducer));
// expected output: 10

// 5 + 1 + 2 + 3 + 4
console.log(array1.reduce(reducer, 5));
// expected output: 15

```

**filter**

```javascript
var words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

const result = words.filter(word => word.length > 6);

console.log(result);
// expected output: Array ["exuberant", "destruction", "present"]
```

### 3.2 Currying

A curried function always returns another function with an arity of 1 until all of the arguments have been applied

```javascript
function greeting(msg, name) {
  console.log(msg + ' ' + name);
}
// greeting('Hello', 'GDG')

function greetingCur(msg) {
  return function(name) {
    console.log(msg + ' ' + name);
  }
}
// greetingCur('Hello')('GDG')


const sayHelloTo = greetingCur('Hello');
sayHelloTo('GDG')
```

### 3.3 Function composition

The process of combining two or more functions to produce a new function.

### 3.4 Testability

Pure functions are easier to test

Lots of reuse - less code to test


## 3.5 Next topics

functors, applicatives, monads

### 3.5.1 Libraries

- RamdaJS
- lodash-fp




### Do you want to know more?

- https://github.com/MostlyAdequate/mostly-adequate-guide
- https://medium.com/javascript-scene



**Francisco Magalhães**
Github: @FranciscoMSM
Medium: @franciscomags


Source: https://slides.com/franciscomagalhaes/fp-js

