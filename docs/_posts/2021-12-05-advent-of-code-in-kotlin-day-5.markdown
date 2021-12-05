---
layout: post
title:  "Advent of Code in Kotlin: Day 5"
date:   2021-12-05 16:10:47 +0200
categories: kotlin
---

## Day 5
Alright, today is day 5 of [advent of code](https://adventofcode.com/2021/), we have an interesting challenge involving points and lines (or vectors) in a 2D plane.
Below I will share and explain my proposed solution in Kotlin.

### Looking back on Day 3 and Day 4
Before we jump into day 5's puzzle, I would to share links to my previous days' proposed solutions:
* [Day 3](https://github.com/Eric-Lloyd/advent-of-code-kotlin/blob/main/src/day3/Day3.kt) — a few key concepts used are:
  * [extensions](https://kotlinlang.org/docs/extensions.html) for example on `List<Char>` type
  * [higher-order functions](https://kotlinlang.org/docs/lambdas.html) aka a function which takes other functions as parameters
* [Day 4](https://github.com/Eric-Lloyd/advent-of-code-kotlin/blob/main/src/day4/Day4.kt) — a few key concepts used are:
  * [data classes](https://kotlinlang.org/docs/data-classes.html) for the board, rows and tiles
  * [chunked](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/chunked.html) method to partition a iterable in groups of a given size `n`

### Day 5 Problem
You can find the description for this puzzle [here](https://adventofcode.com/2021/day/5), I would summarize the problem as the following:
> Given a list of lines or vectors, count the number of points which overlap


### Solution
In my opinion, today's puzzle could be broken down into 3 challenges:
1. modeling the input data
2. converting lines to a list of points
3. counting the points to find overlapping ones

I will share how I approached each of these challenges by sharing parts of my solution.

#### Modeling the input
So here we need to convert each line from the input file to two points on a 2D plane.
The line between two points can be called a vector. I chose to simply call it `Line`. 
Here is how I modelled my data:
```kotlin
data class Point(val x: Int, val y: Int)

data class Line(val from: Point, val to: Point) {
    // [...]
}
```
Then you need to convert the `List<String>` to a `List<Line>`. Here is how I did it:
```kotlin
val lines = readInput("day5/Day5") // https://github.com/Eric-Lloyd/advent-of-code-kotlin/blob/main/src/utils/Utils.kt#L8
        .map { lineFrom(it) }

fun lineFrom(line: String) =
    line.split(" -> ")
        .map { subLine ->
            subLine.split(",")
                .map { it.toInt() }
                .let { (x, y) -> Point(x, y) }
        }
        .let { (from, to) -> Line(from, to) }
```
Once we have modelled the data, we can focus on solving the problem at hand.

#### Converting a line to a list of points
As stated before, we need to count occurrences of each point to find when two points overlap. 
Before we can do this, we need to get all given points on a `Line`.

In my opinion, this part was the most difficult — especially for diagonal lines.
For horizontal and vertical lines, we can leverage the fact that `y` or `x` stay consistent for all points.
For diagonal lines, there are 4 cases:
* `from.x < to.x` and `from.y < to.y` 
* `from.x < to.x` and `from.y > to.y` 
* `from.x > to.x` and `from.y < to.y` 
* `from.x > to.x` and `from.y > to.y`

In order to traverse the line, we need to either increment or decrement `x` and `y`. 
* if `from.x > to.x` we should decrement x
* if `from.x < to.x` we should increment x

The same thing can be said for `y`. Instead of handling these 4 cases, we can abstract out the increment/decrement action in a `direction` function. 
Then, we can update `x` and `y` using this `direction`.
I hope I was clear, if not reading the code may help you understand:
```kotlin
data class Line(val from: Point, val to: Point) {
    val isHorizontal: Boolean = from.y == to.y
    val isVertical: Boolean = from.x == to.x
    val isDiagonal: Boolean = abs(from.x - to.x) == abs(from.y - to.y)

    /*
     * returns all the points along the line.
     */
    fun points(): List<Point> {
        val points = mutableListOf<Point>()
        when {
            isHorizontal -> {
                val min = minOf(from.x, to.x)
                val max = maxOf(from.x, to.x)
                for (i in min until max + 1) {
                    points.add(Point(i, from.y))
                }
                return points
            }
            isVertical -> {
                val min = minOf(from.y, to.y)
                val max = maxOf(from.y, to.y)
                for (i in min until max + 1) {
                    points.add(Point(from.x, i))
                }
                return points
            }
            isDiagonal -> {
                points.add(from)
                val xDirection = direction(from.x, to.x) // whether to increment or decrement x when traversing the line
                val yDirection = direction(from.y, to.y) // whether to increment or decrement y when traversing the line
                var currentX = xDirection(from.x)
                var currentY = yDirection(from.y)
                while (currentX != to.x) {
                    points.add(Point(currentX, currentY))
                    currentX = xDirection(currentX)
                    currentY = yDirection(currentY)
                }
                points.add(to)
                return points
            }
            else -> {
                throw NotImplementedError("We only support horizontal, vertical or diagonal lines.")
            }
        }
    }
}

fun direction(a1: Int, a2: Int): (Int) -> Int {
    if (a1 > a2) return { a -> a - 1 } else return { a -> a + 1 }
}
```
Once we have points for a given `Line`, we simply need to count occurences of points for the lines (`List<Line>`).
#### Counting the points
Counting the points can be done in a very idiomatic way leveraging some built-in function of the Kotlin `List` class.
Here is the one function for it:
```kotlin
fun overlappingPoints(lines: List<Line>, threshold: Int) =
    lines.flatMap { it.points() }
        .groupBy { it }
        .count { it.value.size >= threshold }
```
Let me break it down for you:
* `flatMap` allows getting all the points for all the lines, if we would have simply `map`ped, we would have had `List<List<Point>>`, using `flatMap` allows to flatten the result to a `List<Point>`.
* `groupBy` allows transforming the `List<Point>` to a `Map<Point, List<Point>>` with the list being a list of duplicates of the same point (one for each time when this point is on a given line).
* `count` allows counting how many times a given predicate is `true` for the key/value pair. Here the predicate is `{ it.value.size >= threshold }` which checks whether the number of duplicates for this point is bigger than the given `threshold`.

##### Filtering which points to count
You may have noticed how I did not distinguish my solution between _Part 1_ and _Part 2_ so far, this is because the only difference is which points to count. 
For both parts you need to add a filter based on the type of line (whether it is `horizontal`, `vertical` or `diagonal`).
Here is how I did it:
```kotlin
fun count(lines: List<Line>, filter: (Line) -> Boolean) =
    overlappingPoints(lines.filter { filter(it) }, 2)

val count1 = count(lines) { line -> line.isVertical || line.isHorizontal }
val count2 = count(lines) { line -> line.isVertical || line.isHorizontal || line.isDiagonal }
```
Caveat: for today it seems all lines in the input are one of `horizontal`, `vertical` or `diagonal` (there are no lines at 60 degrees for example).
This means if a line is not `horizontal` nor `vertical` you could consider it `diagonal` without explicit check.
### Kotlin Concepts 
Alright, I hope you liked my proposed solution and learned a few things along the way.
The key Kotlin concepts that we have covered are:
* [data classes](https://kotlinlang.org/docs/data-classes.html)
* [`flatMap` ](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flat-map.html) 
* [`count`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) 
* [higher-order functions](https://kotlinlang.org/docs/lambdas.html#higher-order-functions) to avoid code duplicates

Feel free to share with me any feedback / concerns / questions you may have.
### Source code
You can find the source code for my solution [here](https://github.com/Eric-Lloyd/advent-of-code-kotlin/tree/main/src/day5).

Thank you very much for reading! 
