Automatic Currying in Javascript
===

[Currying](http://en.wikipedia.org/wiki/Currying) is a very useful feature in functional languages such as Haskell - it allows us to treat a function with multiple arguments as a chain of functions each accepting one argument. This allows us to [partially apply](http://en.wikipedia.org/wiki/Partial_application) arguments and then use the function returned later. Take a look at this example [Haskell](http://en.wikipedia.org/wiki/Haskell_\(programming_language\)) code:

```haskell
add a b = a + b
add3 = add 3

r = add3 4     -- r is 7
```

We can make a javascript/coffeescript function that automatically enables this behaviour by simple recording the arguments that are applied to the function and then calling the function once the correct number of args have been applied. First, we need a function to partially apply a number of args to a function - we'll call this `curry`:

```coffeescript
curry = (f, curryArgs...) ->
	(args...)-> 
		f.apply(this, curryArgs.concat args)
```

This takes a function and some curried-args then produces another function that will apply these curried-args before any other args. Using this, we can build up a function `autocurry`:

```coffeescript
autocurry = (f, n) ->
	(args...) ->
		if n - args.length <= 0
			f.apply this, args
		else
			pun.autocurry(pun.curry.apply(this, [f].concat args), (n - args.length))
```

This takes a function and the number of args required<sup>1</sup> then repeatedly _curries_ the function until all the args have been applied. It then runs the function with all the args. The equivilent `add` example that we discussed to begin with can be written like so:

```coffeescript
add = (a, b) -> a + b
addC = autocurry add, 2

add3 = addC 3
r = add3 4     # r is 7
```

This is part my functional programming library for js, [pun](https://github.com/CRogers/pun).

---
<small><sup>1</sup>: Javascript unfortunately does not allow you to find out the number of args a function has. Even if it were possible, you could access arguments that are passed that are not declared in the function signature by using the `arguments` object, so we always need to define the number of args required.</small>

---

**Tags:** javascript, coffeescript, functional-programming, currying, pun

**Date:** 13/05/2012