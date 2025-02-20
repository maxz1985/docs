# Arbitrary Pattern Generation

Create a function that, given the Item number `i` generates the following pattern:
```python
Item 0: Output:5
Item 1: Output:12 
Item 2: Output:10 
Item 3: Output:-1 
Item 4: Output:-8
Item 5: Output:-1
Item 6: Output:-3
Item 7: Output:-14
Item 8: Output:-21
Item 9: Output:-14
Item 10: Output:-16
Item 11: Output:-27
Item 12: Output:-34
Item 13: Output:-27
Item 14: Output:-29
Item 15: Output:-40
Item 16: Output:-47
Item 17: Output:-40
Item 18: Output:-42
Item 19: Output:-53
Item 20: Output:-60
```
## Determine the pattern
There seems to be no relation between Item number `i` and the Output.
So potentially there is no _functional_ relation such as `Output = f(i)` between `i` and the Output. `i` may be a simple counter.

However, the Output numbers may relate to _each other_ in a pattern.

Let us start by determining the difference between adjacent Output numbers.

The sequence starts with `5`. Following that, let us list the difference between each pair of Output numbers.
```python
Item 0: Output:5
Item 1: Output:12 +7
Item 2: Output:10 -2
Item 3: Output:-1 -11
Item 4: Output:-8 -7
Item 5: Output:-1 +7
Item 6: Output:-3 -2
Item 7: Output:-14 -11
Item 8: Output:-21 -7
Item 9: Output:-14 ... and so on repeats
Item 10: Output:-16
Item 11: Output:-27
Item 12: Output:-34
Item 13: Output:-27
Item 14: Output:-29
Item 15: Output:-40
Item 16: Output:-47
Item 17: Output:-40
Item 18: Output:-42
Item 19: Output:-53
Item 20: Output:-60
```

**The pattern has emerged**. The sequence starts with `5` and, following that, 
each next number is obtained by adding the following `+7, -2, -11, -7` in the cyclical fashion.

## Think through the function
### Staring point

Let's call our function `pattern_generator`. It will take `i` as an argument and return `i-th` number in the sequence.
```Python
def pattern generator(i):
    return result
```
Now the first thing we can do right away is to return the staring number` 5` when the `i` is `0`.
```Python
def pattern generator(i):
    if i == 0:
        return 5
    return result
```
### Thinking through the sequence
Next, we have to tackle the sequence. 

What we have to do is to cycle through the following increments `+7, -2, -11, -7` for the remainder of the sequence.

Let us first create a simple loop that iterates the correct number of times, adds a value to the result and returns it.

The sequence starts at `0`, so the `Output(20)` is actually the `21st` ordinal in the sequence.

We're already returning `5` for `i==0`, so to return the rest of the numbers we need to iterate `i` number of times.
`range(i)` will do exactly that.
```Python
def pattern generator(i):
    if i == 0:
        return 5
    for j in range(i):
        result = result + value
    return result
```
Now we see that before the loop runs, the `result` needs to be initialized to the staring value of `5`, let us fix that.
```Python
def pattern generator(i):
    result = 5
    if i == 0:
        return result
    for j in range(i):
        result = result + value
    return result
```
Let us tackle the `value` next.

The `value` should apply the correct increment from this list `+7, -2, -11, -7` for each iteration. 
Let us introduce our list.

```Python
def pattern generator(i):
    result = 5
    if i == 0:
        return result
    increments = [7, -2, -11, -7]
    for j in range(0, i):
        result = result + value
    return result
```
**We need to cycle through the list with a _wraparound_.**

#### So we need a function that goes like this:
<table style="header-column">
    <tr>
        <td> <code>j</code></td>
        <td> <code>0</code></td>
        <td> <code>1</code></td>
        <td> <code>2</code></td>
        <td> <code>3</code></td>
        <td> <code>4</code></td>
        <td> <code>5</code></td>
        <td> <code>6</code></td>
        <td> <code>7</code></td>
    </tr>
    <tr>
        <td> <code>increments list index</code></td>
        <td> <code>0</code></td>
        <td> <code>1</code></td>
        <td> <code>2</code></td>
        <td> <code>3</code></td>
        <td> <code>0</code></td>
        <td> <code>1</code></td>
        <td> <code>2</code></td>
        <td> <code>3</code></td>
    </tr>
     <tr>
        <td> <code>increment[list index] value</code></td>
        <td> <code>+7</code></td>
        <td> <code>-2</code></td>
        <td> <code>-11</code></td>
        <td> <code>-7</code></td>
        <td> <code>+7</code></td>
        <td> <code>-2</code></td>
        <td> <code>-11</code></td>
        <td> <code>-7</code></td>
    </tr>
</table>

## Solution One, algorithmic – the `mod` operator
### Basic math first – what is `mod`?

`Modulo` operator `A mod B = R` returns the **remainder** $R$ of $A/B$ division.

For example, ` 7 mod 2 = 1`: $2$ fits into $7$ fully $3$ times (`2 * 3 = 6`) and $1$ remains.

Essentially `mod` performs the following:
* `7 / 2 = 3.5`: Divide $A$ by $B$
* `int (3.5) = 3`: Truncate the decimal part – this is the $Quotient$ $Q$
* `7 – (2*3) = 1`: Subtract $Quotient$ from $A$ to get the $Remainder$ $R$: 

So $A$ `=` $(B*Q) +R$ or, if we solve for $R$, then $R$ `=` $A$ ` – ` $(B*Q)$

Enough basic math. Let us see how this all helps us.

It is all fine and good when $A > B$ – we get the remainder between $[0 … B-1]$.

Let's consider what happens when $A < B$.

Let us take ($1$ `mod` $4$) and contemplate this equation $R$ `=` $A$ `–` $(B*Q)$.

We see that when $A < B$ (including $A$ `=` $0$), the $R$ `=` $A$, because $Q$ is always **$0$**
(the larger $B$ fits into smaller $A$ $0$ times).

So: ($1$ `mod` $4$) `=`$1 – ( 4 * 0 )$ `=` $1$

And:

| A | A mod 4 | A (continue) | A mod 4 (Continue) |
|---|---------|--------------|--------------------|
| 0 | 0       | 4            | 0                  |
| 1 | 1       | 5            | 1                  |
| 2 | 2       | 6            | 2                  |
| 3 | 3       | 7            | 3                  |

**So given the sequence of `A [0…∞]`, `A mod B` returns `[0..B-1]` every `B` elements.**

[Hm, it seems like we've seen this pattern before? ](#so-we-need-a-function-that-goes-like-this)

Why, yes, it is the function we've been looking for.

### Back to code – finish the function.

Let us get back to our code now. Let us express how we're going to use mod function to achieve the result.

Replace the `value` placeholder with `increments[j % 4]`. 

As the counter `j` increments through the loop,
the `j % 4` index will pick the correct element from the `increments` list wrapping around after `4` iterations.
```Python
def pattern generator(i):
    result = 5
    if i == 0:
        return result
    increments = [7, -2, -11, -7]
    for j in range(0, i):
        result = result + increments[j % 4]
    return result
```
A bit of a cleanup. 
* `range()` starts with `0` by default, so replace `range(0, i)` with `range(i)`
* use abbreviated addition – `result += increments[j % 4]`
* remove hardcoded list size dependency and use `len(increments)` for maintainability

Here is our final function:
```Python
def pattern generator(i):
    result = 5
    if i == 0:
        return result
    increments = [7, -2, -11, -7]
    for j in range(i):
        result += increments[j % len(increments)]
    return result
```

## Solution Two – Pythonic

There is actually a very _Pythonic_ way to solve this problem.

The solution relies on understanding of `generators`, `iterables` and `dunders` in Python.

I will show you the solution first and then talk a bit about how it works.
```Python
def cycle_array(arr):
    while True:
        yield from arr
```
Wow. So this is it? Well, yes, but how exactly would this work?

Let's start with this: `yield from`.
### `yield from`
`yield from` was introduced in Python 3.3 to simplify generator chaining. 

For example, when you need to call _another_ `generator` from your `generator`, you would use `yield from {other_generator}`.

`Generators` are `iterables`, which in Python means they're objects that implement `__iter__()` and `__next__()` `dunders`.

`yield from {some_generator}` creates an `iterator`by executing `some_generator.__iter__()` on the first call 
and `some_generator.__next__()` on the following calls until the `generator` is exhausted.

When the `generator` is exhausted, the `__next__()` call raises `StopIteration` **exception
which is caught and handled** by `yield from` and program **execution moves on to the next line** after `yield from`.

Now this is all exciting, but what is it to us? We're using a `list` (or array) here, NOT a `generator`.
### Use `yield from` with a `list` or any `iterable`

Ah but see, `yield from` **will work with ANY `iterable`** since it only relies on the presence
of `__iter__()` and `__next__()` `dunders` which by definition all `iterables` implement.

So? So when we call `yield from arr`, `yield from` will treat it the same way – create an `iterator`, 
then call `__next__()` on the `iterator` until it is exhausted by traversing the entire array.

Ok so far `yield from arr` traversed the array, generated `StopIteration` exception after delivering the last element
and handled the exception by exiting. What happens now? 

Well, the next line executes. And what is the next line? 
```Python
while True:
```
The line restarts the loop, which calls `__iter__()` on `arr` creating a fresh `iterator` pointing to the first element, 
and the whole process starts again.

This code:
```Python
def cycle_array(arr):
    while True:
        yield from arr
```
will cycle endlessly through an array while not using memory since it is a generator
and not requiring the array size or type.

All that is required is that `arr` is an `iterable`.

_Welcome to Python._

How do we use it?
Like this:
```Python
def pattern_generator2(i):
    result = 5
    if i == 0:
        return result
    cycler = cycle_array([7, -2, -11, -7])
    for _ in range(i):
        result += next(cycler)
    return result
```
Notice:
* list size irrelevant
* no math logic
* throwaway for loop index `_`
* in-place `generator` initialization – no need for variable to hold the `increments` list

## Aren't there any packages that can do this?
Why, yes, of course. There is a great library called `itertools` which, among other functions, contains a list cycler.
```Python
from itertools import cycle
def pattern_generator3(i):
    result = 5
    if i == 0:
        return result
    cycler = cycle([7, -2, -11, -7])
    return result + sum( [next(cycler) for _ in range(i)])
```
So here we are:
* creating `cycler` which will cycle around our list. `cycle` is a generator provided by `itertools` library.
* using list comprehension to create a list of the increments to add together to produce a sum equivalent to `ith` iteration
* this is the list comprehension `[next(cycler) for _ in range(i)]`
* using `sum()` to add the elements generated by the list comprehension together producing the `ith` number in sequence