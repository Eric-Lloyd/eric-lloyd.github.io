---
layout: post
title:  "Advent of Code in Kotlin: Day 2"
date:   2021-12-02 16:10:47 +0200
categories: kotlin
---

## Day 2
Alright, today is day 2 of [advent of code](https://adventofcode.com/2021/), we have a challenge slightly more difficult than Day 1 but still very much accessible and fun (not overwhelming yet :grimacing:). 

### Looking back on Day 1
Before we jump into Day 2's puzzle and my proposed solution in Kotlin, I would like to share something I learned about yesterday:
In Kotlin `List` API there is a [windowed](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/windowed.html) method which allows iterating over a list with "sliding windows". 
This method takes a `size` parameter which is the size of the "sliding window". 

To give a concrete example, consider the following code:
```kotlin
listOf(1, 2, 3, 4).windowed(2) // returns [[1, 2], [2, 3], [3, 4]]
```

Also, it can be called with a "transform" function parameter which will be applied to each "sliding window" or sublist. 
For example:
```kotlin
listOf(1, 2, 3, 4).windowed(2) { it.sum() }) // returns [3, 5, 7]
```
I imagine you could remember how this could have been useful for yesterday's problem  :sweat_smile:

Big thanks to Sebastian Aigner for sharing [his solution](https://github.com/SebastianAigner/advent-of-code-2021/blob/master/src/main/kotlin/Day01.kt) for Day 1 and teaching me about this!
You can find an interesting [blog post](https://dev.to/kotlin/advanced-kotlin-collection-functionality-5e90) he wrote about advanced Kotlin Collection functionality.

### Day 2 Problem
You can find the description for this puzzle [here](https://adventofcode.com/2021/day/2), I would summarize the problem as the following:
> Given a list of commands, compute some aggregates based on the command's value.

### Modeling the input
I have decided to use a [data class](https://kotlinlang.org/docs/data-classes.html) to model the input, as well as an [enum](https://kotlinlang.org/docs/enum-classes.html) for the command type:
```kotlin
enum class Type {
    FORWARD, UP, DOWN
}

data class Command(val type: Type, val amount: Int)
```
The input to my functions will be a `List<Command>`.
### Solution
The distinction between _Part 1_ and _Part 2_ is how each command affects the aggregate to be calculated.
I have taken two different approach in each part on how to compute these aggregates, which I will share with you below:
#### Part 1
For this part, there are two aggregates `depth` and `horizontalPosition`. To calculate each we need to iterate over the input list. We could either calculate both in the same function and loop, or create a function for each aggregate. 
In _Part 2_ I will be doing the former, in _Part 1_ I am doing the latter.
Below are the two functions I have used:
```kotlin
fun horizontalPosition(commands: List<Command>) =
    commands
        .filter { it.type == Type.FORWARD }
        .map { it.amount }
        .sum()

fun depth(commands: List<Command>) =
    commands
        .filter { it.type == Type.DOWN || it.type == Type.UP }
        .map { if (it.type == Type.DOWN) it.amount else -it.amount }
        .sum()
```
These follow the pattern _filter-map-reduce_. It is the clean functional way of transforming list into an aggregate. 
* For the `horizontalPosition` we simply need to sum the commands of type `FORWARD` (while ignoring other types of commands).
* For the `depth`, we need to add the amount for `DOWN` commands and subtract it for `UP` commands (while ignoring `FORWARD` commands).

#### Part 2
For this part, there is an additional aggregate called `aim` which we need to compute `depth`.
In other words, the logic for calculating `depth` change, so we need to ditch our previous function. 
This time, we have to compute the aggregates together as they depend on each other (`depth` depends on `aim`). 
To do this, we can leverage the `fold` [method](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold.html) on the `List` API which is a generalized way of doing a `reduce` as instead of using the first element of the list as a starting point for the calculation, we can specify our own initial element (and its type).

I have again leveraged a data class here for my aggregates: 
```kotlin
data class Attributes(val aim: Int, val horizontalPosition: Int, val depth: Int)
```
I will use this class for my accumulator when using `fold`:
```kotlin
fun attributes(commands: List<Command>): Attributes {
    return commands
        .fold(
            Attributes(0, 0, 0),
            { acc, command -> operation(acc, command) }
        )
}

private fun operation(attributes: Attributes, command: Command): Attributes {
    val (aim, horizontalPosition, depth) = attributes
    return when (command.type) {
        Type.FORWARD -> Attributes(aim, horizontalPosition + command.amount, depth + (aim * command.amount))
        Type.UP -> Attributes(aim - command.amount, horizontalPosition, depth)
        Type.DOWN -> Attributes(aim + command.amount, horizontalPosition, depth)
    }
}
```
Here my initial value for the `acc` variable is `Attributes(0, 0, 0)`.
Then, I loop over the list and for each command, I transform my `acc` variable by calling the `operation` method. 
The `fold` can be considered as doing the following:
```kotlin
fun fold(commands: List<Command>) {
    var acc = Attributes(0, 0, 0)
    for (command in commands) {
        acc = operation(acc, command)
    }
    return acc
}
```
In other words, the `operation` method is where the computation happens, and if you look more closely you can see I am using the [when expression](https://kotlinlang.org/docs/control-flow.html#when-expression).
This can be considered an improved "switch" and allows partial "pattern matching". 

Based on the command type, I create a new `Attributes` instance using the previous one, and the command amount.
Note here than I do not need an `else` clause in my `when` expression. This is because I am using an enum and not a string, hence the Kotlin compilers knows that I have all the cases covered (as my enum has only 3 fields). 
This is very nice as it avoids having something like:
```kotlin
when (foo) { 
  // [...] 
  else -> throw Exception("this should never be reached")
}
```

Also note how data classes allow destructuring of properties:
```kotlin
val (aim, horizontalPosition, depth) = attributes
```
is equivalent to:
```kotlin
val aim = attributes.aim
val horizontalPosition = attributes.horizontalPosition
val depth = attributes.depth
```

### Kotlin Concepts 
Alright, I hope you liked my proposed solution and learned a few things along the way. 
The key Kotlin concepts that we have covered are:
* `filter-map-reduce` for computing an aggregate from a list
* `fold` for computing multiple aggregates at once from a list
* `when` expression and enums work nicely hand in hand
* data class can easily help to build named tuples of data

### Source code
You can find the source code for my solution [here](https://github.com/Eric-Lloyd/advent-of-code-kotlin/tree/main/src/day2).

Thank you very much for reading! 
