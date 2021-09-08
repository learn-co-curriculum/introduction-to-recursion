# Introduction to Recursion

## Learning Goals

- Write recursive methods
- Define the "base case" of a recursive method
- Explain the role of the call stack in a recursive method
- Identify when to use recursion to solve algorithm problems

## Introduction

A recursive method is defined as any method that calls itself.

Typically, recursion is used in a manner similar to a loop: a procedure is run
over and over again until it reaches a stopping point. Recursive methods must
call themselves to be considered recursive.

We can use recursion anywhere we use a loop, and vice versa. Sometimes, it's a
lot easier to solve a problem using recursion, as opposed to a loop, and
sometimes it's easier to use a loop. It takes time and practice to figure out
when to choose one over the other.

Here is an example of a recursive method:

```rb
def talk_to_myself(n)
  talk_to_myself(n)
end
```

In the above code, the method `talk_to_myself` is recursive because it calls
itself. It, however, has a gigantic problem: there is no stopping point!

## Base Case/s (aka the stopping point/s)

The base case (or cases) tells the recursive method when to stop running. Base
cases are often written using conditional statements (such as the `if` statement
below), though they don't have to be (it depends on the method and what it needs
to do).

```rb
def talk_to_myself(n)
  return if n <= 0.5

  talk_to_myself(n / 2)
end
```

Notice how the base case comes **before** the recursive call to the method. If
the base case came after, it would be unreachable and we'd have the same exact
problem as before: there would be no stopping point, and we'd hit a stack
overflow.

## Stack Overflow

When we run a `while` loop where the terminating condition is never reached, we
get an infinite loop. A stack overflow is similar. However, code that would
eventually terminate can also cause a stack overflow if it adds too many
**frames** to the **call stack**.

- A **frame** is like a snapshot of all of the variables and other necessary
  information required to finish running the process.
- The **call stack** is a data structure that stores frames.

Frames are removed from the stack in last-in-first-out (LIFO) order, similar to
how we eat a stack of pancakes (the last pancake is put on the stack last, and
we eat that one first).

Here's a gif demonstrating how function calls are added and removed from the stack:

![call stack demo](https://curriculum-content.s3.amazonaws.com/data-structures-and-algorithms/intro-to-recursion/call-stack.gif)

Calling a function adds it to the top of the call stack. When the function
finishes running (either because of an explicit or implicit return), it is
removed from the top of the stack. All the other functions below the top of the
stack can't continue running until they make their way up to the top.

This is why it's important that all recursive functions have a base case defined
with some condition that causes the function to return. Otherwise, we'll
continually add more function calls to the stack with no way to stop!

## Depth-First Completion (LIFO)

With recursive methods, the last recursive call will complete its execution
first. Once that completes, the second to last recursive call will complete, and
so on until only the first call to the method remains. Let's go back to our
`talk_to_myself` method and illustrate each frame:

```rb
def talk_to_myself(n)
  return if n <= 0.5

  talk_to_myself(n / 2)
end
```

- Initial Call (execution incomplete, paused on recursive call):
  - `talk_to_myself(4)`
- Recursive Call 1 (execution incomplete, paused on recursive call):
  - `talk_to_myself(2)`
- Recursive Call 2 (execution incomplete, paused on recursive call):
  - `talk_to_myself(1)`
- Recursive Call 3 (execution incomplete, paused on recursive call):
  - `talk_to_myself(0.5)`
- => Base case is hit because n <= 0.5, no more recursion!
- Recursive Call 3 completes
- Recursive Call 2 completes
- Recursive Call 1 completes
- Initial Call completes

You can walk through and visualize this process [here][talk to myself viz]. The
frames and their data are visualized on the right side of the screen and the
arrows on the left inside the IDE show you which line is being executed. Notice
that the arrow pauses on the recursive call if the base case is not hit. When a
recursive call finally completes execution and returns up the stack, the
previous call will then continue to run from that line onward (the line where
the recursion was triggered).

[talk to myself viz]: https://pythontutor.com/visualize.html#code=def%20talk_to_myself%28n%29%0A%20%20return%20if%20n%20%3C%3D%200.5%0A%0A%20%20talk_to_myself%28n%20/%202%29%0Aend%0A%0Atalk_to_myself%284%29&cumulative=false&curInstr=0&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=ruby&rawInputLstJSON=%5B%5D&textReferences=false

## Dealing With Return Values

Let's go back to our code example and modify it to return the string `'done'`:

```rb
def talk_to_myself(n)
  return 'done' if n <= 0.5

  # this is where our method pauses
  # it's also where our return values return to
  talk_to_myself(n / 2)
end
```

That one small change will cause the method to return the string `'done'` from
every recursive call and the initial call. But how?

Let's illustrate how using stack frames again:

- Initial Call (execution incomplete, paused on recursive call):
  - `talk_to_myself(1)` # pauses on the line of the recursive call
- Recursive Call 1 (execution incomplete, paused on recursive call):
  - `talk_to_myself(0.5)` # pauses on the line of the recursive call
- => Base case is hit because `n <= 0.5`, no more recursion! `'done'` is
  returned to the previous frame
- Recursive Call 1 receives `'done'`, and then returns `'done'` up the stack
- Initial Call receives `'done'`, and then returns `'done'`

But what if we added a line of code after the recursive call? What would happen
then?

```rb
def talk_to_myself(n)
  return 'done' if n <= 0.5

  # this is where our method pauses
  # it's also where our return values return to
  talk_to_myself(n / 2)
  `'The sheep goes baaaaaahhhh'`
end
```

- Initial Call (execution incomplete, paused on recursive call):
  - `talk_to_myself(1)` # pauses on the line of the recursive call
- Recursive Call 1 (execution incomplete, paused on recursive call):
  - `talk_to_myself(0.5)` # pauses on the line of the recursive call
- => Base case is hit because n <= 0.5, no more recursion! `'done'` is returned to
  the previous frame
- Recursive Call 1 receives `'done'` and returns `'The sheep goes baaaaaahhhh'` up
  the stack
- Initial Call receives `'The sheep goes baaaaaahhhh'`, and then returns
  `'The sheep goes baaaaaahhhh'`

You can see a visualization of the code [here][talk to myself viz 2].

[talk to myself viz 2]: https://pythontutor.com/visualize.html#code=def%20talk_to_myself%28n%29%0A%20%20return%20'done'%20if%20n%20%3C%3D%200.5%0A%0A%20%20%23%20this%20is%20where%20our%20method%20pauses%0A%20%20%23%20it's%20also%20where%20our%20return%20values%20return%20to%0A%20%20talk_to_myself%28n%20/%202%29%0A%20%20%60'The%20sheep%20goes%20baaaaaahhhh'%60%0Aend%0A%0Atalk_to_myself%281%29&cumulative=false&curInstr=0&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=ruby&rawInputLstJSON=%5B%5D&textReferences=false

## Conclusion

Don't worry if this hasn't all sunk in yet. We'll get you started slowly. If you
find yourself having trouble with recursion, ask yourself these questions:

- What is/are the base case/s? Many people add those first.
- If you're getting a stack overflow: Why isn't my base case being triggered?
- What should the recursive call return? And how should I use that value?
  - Remember the return value goes up the stack to the line where the recursive
    call was made.
- What should the method return once it has completed execution?

You can also try drawing out the frames to trace what's happening or use this
[tool](http://pythontutor.com/visualize.html#mode=edit). Start small when
mapping out what's happening, e.g. in the code examples above we used the values
2 or 4, but never 20!
