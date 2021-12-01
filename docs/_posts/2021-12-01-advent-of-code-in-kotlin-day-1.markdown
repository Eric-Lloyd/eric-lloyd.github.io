---
layout: post
title:  "Advent of Code in Kotlin: Day 1"
date:   2021-12-01 16:10:47 +0200
categories: kotlin
---

# Introduction 
It's this time of the year when it gets dark and cold outside (at least in Berlin), people start getting into the Christmas spirit by going to Christmas Markets and drinking Gluhwein (while it's still allowed). It's also the time when you spend more time indoors "cosying" up with a hot beverage. 

More specifically, if you are interested in coding, today marks the first day of [advent of code](https://adventofcode.com/). The idea is fairly straightforward: every day a programming puzzle gets released, in order to unlock the puzzle for each day you need to solve the previous day. 

This is a great opportunity to learn new things while having fun for people who enjoy coding. 
I personally am in the process of learning Kotlin so this is an opportunity for me to practice my skills in this programming language. 

Disclaimer: this event can be competitive and as a result stressful for some. I am not planning to complete each and every puzzle every day, because "life"! Nor am I trying to solve puzzles as fast as possible or as concisely as possible.

Instead, my main goal in participating in this event is three-fold:
* Practice coding in Kotlin
* Have fun
* Share my learnings with the community    

Luckily for you, in you are also interested in learning Kotlin, JetBrains have shared a [blog post](https://blog.jetbrains.com/kotlin/2021/11/advent-of-code-2021-in-kotlin/) on this event, explaining in more details how you can participate. Moreover, they have created a [GitHub template](https://github.com/kotlin-hands-on/advent-of-code-kotlin-template) which you can use to easily get started.

My plan is to write short blog posts for the days I will have time to solve in which I will share and explain my solution to the daily puzzle. 

Let's get started with Day 1!

# Day 1
FYI: in the last years, the puzzle increase in difficulty throughout the days — the first few days can be considered as a "ramp up".
## Puzzle — Part 1
The puzzle description can be found [here](https://adventofcode.com/2021/day/1) — I would summarize it to the following: 
> Given a list of integers, return the number of times an element in the list is bigger than the previous element. 
### Solution
Here is my proposed solution, I will walk through it afterwards: 
```kotlin
fun countIncrease(input: List<Int>): Int {
    if (input.isEmpty()) return 0

    var count = 0
    var previous = input[0]
    val tail = input.drop(1)
    for (current in tail) {
        if (current > previous) {
            count += 1
        }
        previous = current
    }
    return count
}
```
This method takes a list of integers as a parameter, and returns an integer. 
Before looping over the input, I check whether it is empty (we can return early if it is). Then I extract the first element of the list in a temporary variable called `previous`, this variable is mutable and will be updated to always contain the element before the `current` one.
I then loop over the `tail` of the list .i.e the input list with the first element removed. In the loop body, I simply compare the `current` element with the `previous` and if the former is greater than the latter, I increment a `count` variable. Then I update the `previous` variable to `current`. 
Finally, once the loop is completed, I return the `count` variable. 

Here the interesting part is the `.drop` method on the `List` API, which concisely returns a new 'read only' list instance without the first element.

## Puzzle — Part 2
The second part of the puzzle is an extension of the part 1, it can actually be considered the exact same problem, with an additional data transformation before the calculation. 
Indeed, we need to group each triple elements and then apply our algorithm on the new transformed list. 
For example, if the input list is `[4, 1, 4, 1, 7]` the transformed list is `[9, 6, 12]` (computed by `4 + 1 + 4 = 9`, `1 + 4 + 1 = 6` and `4 + 1 + 7 = 12` respectively). Once we have the transformed list, we only need to pass it to our function from part 1. 
### Solution
Here is my proposed solution for the data transformation:  
```kotlin
fun buildTriples(input: List<Int>): List<Triple> {
    val lastFirst = input.size - 3
    return input
        .filterIndexed { index, _ -> index <= lastFirst }
        .mapIndexed { index, _ ->
            val first = input[index]
            val second = input[index + 1]
            val third = input[index + 2]
            Triple(first, second, third)
        }
}

data class Triple(val first: Int, val second: Int, val third: Int)

fun Triple.sum(): Int = this.first + this.second + this.third
```
This method transform each element in the list to a `Triple` instance, `Triple` is a simple [data class](https://kotlinlang.org/docs/data-classes.html) with 3 integer attributes. 
The triple contains the current element and the two following elements in the list. 

Here the interesting parts used are the `.filterIndexed` and `.mapIndexed` method of the `List` API. As the names imply, they are the equivalent of `.filter` and `.map` but they provide a pair of `(index, element)` instead of simply the current element. 

We need to loop over all elements except the last two, however we still need access these elements, so instead of using `.dropLast` we simply filter elements in using their index value. After that, we want to transform each element in a triple of elements containing the current and the two subsequent ones. To do that, we use `.mapIndexed` as it will allow us accessing the following elements based on the current index, and create a `Triple` instance for each element. 

Once we have our transformation, we simply need to pass the result to our other function like so:
```kotlin
countIncrease(buildTriples(input).map { it.sum() })
```
We are also leveraging an [extension function](https://kotlinlang.org/docs/extensions.html) to compute the sum of the `Triple`.

## Source code
You can find the source code for my solution [here](https://github.com/Eric-Lloyd/advent-of-code-kotlin/tree/main/src/day1)

Thank you very much for reading! 
