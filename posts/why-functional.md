# Why you should start writing functional javascript

Functional javascript has gotten a lot of attention the last few years. There are lots of great articles explaining how to write functional javascript so instead I want to address the question of why we should write functional javascript.

## The calm zen garden of pure functions
Pure functions are functions that follows two simple rules.

1. For any given input there can only be one possible output.
  If f(1) returns 2 the first time you call it then f(1) should return 2 every time.
2. A pure function must not have any side effects. It must not mutate shared state, and it must not call non-pure functions.

```js
// pure functions
const head = list => list[0]
const tail = list => list.slice(1)

const reduce = (f, result, list) => {
  if (list.length === 0) return result
  return reduce(f, f(result, head(list)), tail(list))
}
```
The functions above are all pure and will always give the same result for the same input.

Pure functions are the bread and butter of functional programming. They are predictable, easily testable and highly reusable. The more of your code that is implemented with pure functions the better.

## DRY this time we are serious
Don't Repeat yourself is one of the fundamental mottos in every branch of programming. The problem is that writing reusable code is not always easy and it can often take longer to reuse code than to copy it.
Take a look at the following example:

```js
class Pacman {
  constructor() {
    this.score = 0
    this.x = 0
    this.y = 0
  }

  move (x, y) {
    this.x = x
    this.y = y
  }
}
```
lets say I want to add a class ghost:

```js
class Ghost {
  constructor(color) {
    this.color = color
    this.x = 0
    this.y = 0
  }

  move (x, y) {
    this.x = x
    this.y = y
  }
}
```
now we refactor the code, and extract a super class:


```js
class Actor {
  constructor() {
    this.x = 0
    this.y = 0
  }

  move (x, y) {
    this.x = x
    this.y = y
  }
}

class Pacman extends Actor {
  constructor() {
    this.score = 0
    super()
  }
}

class Ghost extends Actor {
  constructor(color) {
    this.color = color
    super()
  }
}

// usage
const pacman = new Pacman()
const inky = new Ghost('blue')

pacman.move(1, 2)
inky.move(1, 2)
```
As projects grow we repeat this pattern of refactoring again and again. These refactorings can become quite complex, since a class can have several classes depending on it.
Lets try the functional version:

```js
const ghostFactory = (color) => ({color, x:0, y:0})
const pacmanFactory = () => ({score:0 , x:0, y:0})
const move = (actor, x, y) => Object.assign({}, actor, {x, y})

// usage
let pacman = pacmanFactory()
let inky = ghostFactory('blue')

pacman = move(pacman, 1, 2)
inky = move(inky, 1, 2)

```

One of the great things about our new move function is that it is not limited to actors. We can use it on anything that has x and y coordinates. By separating function from the data it operates on, reusing code is much simpler. We also don't have to refactor our code when we add the ghosts, because the move function works the same way for every object passed to it.
Because our actors are simple hashmaps we get a lot of functionality for free. Lets say we want to save our game:

```js
const game = {
  pacman,
  ghosts: [inky, blinky, pinky, clyde]
}

localStorage.setItem('myGame', game)

```
If you want to do the same in the OO example you need a way to recreate the objects from the serialized data.


## Polymorphism
Yes functional programming has polymorphism too and its much more powerful than its OO cousin.
A great example of this is the map function.

probably the greatest example of a polymorphic function is `reduce`. The reduce function can be used in in endless ways and almost all list operations can be implemented with reduce.

```js
const max = list => reduce(Math.max, 0, list)
const min = list => reduce(Math.min, infinity, list)

const sum = list => reduce((a, b) => a + b, 0, list)
const avg = list => sum(list) / list.length

const filter = (f, list) => reduce((l, e) => f(e) ? [...l, e] : l, [], list)
const odd = (list) => filter((a) => a % 3 === 0, list)
const even = (list) => filter((a) => a % 3 !== 0, list)

const map = (f, list) => reduce((l, e) => [...l, f(e)], [], list)
const pluck = (prop, list) => map(e => e[prop], list)

const every = (f, list) => reduce((x, y) => x && f(e), true, list)
const any = (f, list) => reduce((x, y) => x || f(e), false, list)
```

## Testing
One of the first eureka moments when writing functional javascript is how simple testing becomes. Because Pure functions have no side effects, and no dependencies there is nothing to stub. Say goodbye to `before` and `after` your unit tests have no setup or teardown step. All your tests are independant, because the functions you are testing are independant.
Because we are reusing our code much more efficiently we don't need as many tests.
All this means that you spend a lot less time writing tests.

```js

it('should return the first element', () => {
  assert.equal(head([1, 2, 3]), 1)
})

it('should return `undefined` if list is empty', () => {
  assert.equal(head([]), undefined)
})
```


## Putting and end to the tyrany of `this`
`this` is arguably one of the worst things about javascript. It

## OO Javascript is broken


## This is javascript of cause we are gonna cheat
