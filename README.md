# Programming with Bacon

Here be opening words.

## Introduction

A bit on the ideas behind Bacon.

* reactive programming (TODO)
* functional programming (TODO)

## Central concepts

It's fun to program with Bacon. Infering the essential ideas behind Bacon can be
a challenge, though.

In this chapter we take a look at the world from the point of view of Bacon.

### Everything is an event

Bacon starts from the assumption that interesting phenomena in the world
manifest to us as events.

The event abstraction is useful, because it implies that the things that we are
interested in occur in parallel.

Since Bacon considers everything to be an event and it has inherent support for
concurrent programming, it is a powerful tool for modelling reality. The real
world runs in parallel and so should our code.

### Use simple components to construct complex programs

In the heart of Bacon is the concept of building complex expressions out of
simple ones.

This concept occurs in many different contexts. In the Unix world shell programs
suchs as `ls` or `grep` are built with the idea that a single program should do
one thing only. Once you have many small programs that do one thing well, you
can blend them into a complex one.

Bacon contains excellent tools for modelling complex programs. For example, it
let's you combine the results of two asynchronous HTTP requests into one.

You can also merge events. With Bacon, you can describe an event which occurs
whenever the user of your program clicks the search button or presses the enter
key on the keyboard.

### Evaluate code only when the result is needed

Bacon is lazy. This means that a Bacon event emits only when someone is
interested in it. Here, this someone is the piece of code that changes the state
of the program.

More formally, Bacon events occur only when you apply a side effect on your
program as a result of the event.

Let's dive deeper into lazyness by looking at an example. In the example we call a shopping
cart HTTP API and show the amount of products to the user.

Below, the functions `callHttp` and `updateAmountOfProductsInCart` are made-up.
You can assume that the `callHttp` function returns a Bacon event which signifies
the completion of the HTTP response. The `updateAmountOfProductsInCart` updates
a number on the screen of the user.

```javascript
var shoppingCartStream = callHttp('https://boutique.com/api/shopping-cart')
shoppingCartStream
  .onValue(function(shoppingCart) {
    updateAmountOfProductsInCart(shoppingCart.amountOfProducts)
  })
```

Above, the function `callHttp` does not have any side effects. In other words,
the `callHttp` function does not change the state of our application.

Now, code would be useless unless it modified the state of our program. Bacon
knows this an will not run the parts of our code that do not have side effects.
Cool stuff. But hey, you might ask, how do I run the code? You run Bacon code by
invoking the `onValue` function.

The `onValue` function tells the underlying Bacon event (or events) that you are
interested in applying the value of the event into the state of your program.
With that information, Bacon knows that the event is useful to someone, and
it will evaluate the code around the event.

In our shopping cart example, lazyness means calling the HTTP API only when the
`onValue` function is called. Should we omit the `onValue` invocation, the HTTP
request would not happen.

Without lazyness we could not be sure that our side-effect applying `onValue`
function would get called right after the event has occurrent. This has to do
with the asynchronous nature of events. Remember that the world runs in
parallel, and our programming tools should support the real world. Hence the
need for lazyness.
