Unbounded Recursion in Javascript
===

For a while I've been trying to create a javascript based library that would allow you to write coffeescipt in a functional style (a la Haskell). One of the problems I had was finding a way to make unbounded recursion in javascript possible and also to make it performant.

The solution is to use [Continuation Passing Style](http://en.wikipedia.org/wiki/Continuation-passing_style) to allow for and unbounded stack. The way to make this performant is to run the code using "normal" recursion until we detect a stackoverflow - we then rollback the code one step and continue using CPS. This way recursive functions working for a small number of steps are fast (as they do not need any kind of explicit stack) but functions with a number of steps greater than that allowed by the native stack still work.

```coffeescript
``` 