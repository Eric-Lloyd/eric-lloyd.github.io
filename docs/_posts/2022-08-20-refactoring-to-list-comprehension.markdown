---
layout: post
title:  "Python: Refactoring code with list comprehensions"
date:   2022-08-20 16:10:47 +0200
categories: python
---

When reading _Python_ code, I often find blocks which could be _refactored_.
What I mean here by _refactoring_ is that these code blocks could leverage list comprehensions to become more readable, more concise (i.e. less verbose) and also less outdated.

I have found that doing this kind of very small refactoring is a good way to get familiar with an unknown codebase — which you can do while reading through the code.
Always make sure that there is a good suite of tests for the code you are refactoring, and to regularly run them every time you make changes.

In this blog post, I would like to share some common patterns that I have come across, and how they can be changed — focusing on the usage of Python **list comprehensions**.

# Examples
## Filtering
One very common action in programming is to filter a collection of elements. To do this in Python, you could do the following:
```python
def long_words(words: List[str], threshold: int) -> List[str]:
    long_words = []
    for word in words:
        if len(word) > threshold:
            long_words.append(word)
    
    return long_words
```
The above method could be replaced with the following one liner using list comprehension:
```python 
def long_words(words: List[str], threshold: int) -> List[str]:
    return [word for word in words if len(word) > threshold]
```
This is more concise, and more readable.


## Transforming
Transformation are also very common, a _transformation_ is when you want to apply a function to each element in a list which converts it to something else. 
There are a lot of method which use the following patterns:
```python
def upper(words: List[str]) -> List[str]:
    upper_words = []
    for word in words:
        upper_words.append(word.upper())
    return upper_words
```
Using list comprehensions, you can do the following in one line:
```python
def upper_lc(words: List[str]) -> List[str]:
    return [word.upper() for word in words]
```
Extracting the inner block computation of the _for loop_ in a helper method allows to then have a clean and readable transformation with `map`.

## Concatenation
Another common practice is to fetch data from two or more different place and then merge the elements into a final list.
To do this prior to Java 8, you could do the following:
```java
List<String> concat(final List<String> words1, final List<String> words2) {
    List<String> concat = new ArrayList<>();

    for (String word : words1) {
        concat.add(word);
    }

    for (String word : words2) {
        concat.add(word);
    }

    return concat;
}
```
This can be transformed to a (fairly long) _one liner_ :
```java
List<String> concat(final List<String> words1, final List<String> words2) {
    return Stream.concat(words1.stream(), words2.stream()).collect(toList());
}
```
The above snippet is leveraging the concat method for `Stream`s, and then collecting back to a `List`.
If you want to add a condition in you concatenation, you can easily do it by using the `filter` method on each word stream.

## Summation
Having a raw collection of elements does not always give good insights, and we often need to do computation on the list like calculting the average, the sum etc.
For example, we might want to know the sum of characters in a list of words for words longer than a given threshold.
To do this prior to Java 8, you could do the following:
```java
int characterSum(final List<String> words, final int threshold) {
    int sum = 0;
    for (String word : words) {
        int length = word.length();
        if (length > threshold) {
            sum += length;
        }
    }
    return sum;
}
```
This can be achieved by leveraging the `reduce` method of `Stream`s. This method perform what is called a `reduction` on the elements of the stream. It takes on two parameters, an _initial value_ for the reduction and an _accumulator_.
The simplest example is a sum:
```java
int characterSum(final List<String> words, final int threshold) {
    return words.stream()
            .map(word -> word.length())
            .filter(length -> length > threshold)
            .reduce(0, (sum, l) -> sum + l);
}
```
The accumulator function is applied at each iteration with the current temporary sum at that given iteration.
The initial value here is `0`.

# Performance comparison
From what I have read, using streams does not degrade performance. However, I did not want to simply guess, rather I wanted to test it myself using the above examples.
I have made very simple and approximate (or "quick and dirty") timer tests using `System.nanoTime()` before and after executing the method on my laptop.

For all the examples, I have generated either 5 hundred thousands or 5 millions random words, and executed the method 100 times.
The results I will share below is the average executing time in milliseconds.

I will also use both the `stream()` and the `parallelStream()` method for comparison.
When using the `parallelStream()` method, it creates multiple substreams which are processed in parallel by different threads. The intermediary results of each substream then gets combined when collecting.
However, note that you have to be careful when using `parallelStream()`:
* iteration operation should not depend/interfere on/with each other 
* iteration operation should not modify a common mutable variable

## Filtering
Reusing my `filtering` example, the results are the following:
* Java 7: 23,66 ms
* Java 8 sequential stream: 24,01 ms
* Java 8 parallel stream: 11,82 ms

## Counting
Reusing my `counting` example, the results are the following:
* Java 7: 20,75 ms
* Java 8 sequential stream: 20,79 ms
* Java 8 parallel stream: 9,11 ms

## Grouping
Reusing my `grouping` example, the results are the following:
* Java 7: 17,24 ms
* Java 8 sequential stream: 17,98 ms
* Java 8 parallel stream: 13,36 ms

## Partitioning
Reusing my `partitioning` example, the results are the following:
* Java 7: 8,71 ms
* Java 8 sequential stream: 10,40 ms
* Java 8 parallel stream: 7,40 ms


## Transforming
Reusing my `transforming` example, the results are the following:
* Java 7: 92,78 ms
* Java 8 sequential stream: 77,86 ms
* Java 8 parallel stream: 67,22 ms

## Concatenation
Reusing my `concatenation` example, the results are the following:
* Java 7: 26,72 ms
* Java 8 sequential stream: 22,54 ms
* Java 8 parallel stream: 26,52 ms

## Summation
Reusing my `summation` example, the results are the following:
* Java 7: 22,13 ms
* Java 8 sequential stream: 22,52 ms
* Java 8 parallel stream: 11,64 ms


## Results
Disclaimer that my testing was "quick and dirty" and that execution time depends on a lot of factor, especially in Java, such as startup time, number of threads available, number of resource available for the JVM, etc.
So take the results with as approximations.
### Sequential streams
It seems that sequential streams perform slightly worse that for/loop in most cases.
However, from my point of view — unless you are optimizing for performance —  the tradeoff between having code that is more readable and concise, meaning more testable and maintainable versus slight worse performance makes using Java 8 Stream completely worth it. 


### Parallel streams
Parallel streams usually performs a lot better that for/loop.
When the operation does not depend on the order of the iteration, and each iteration does not modify any shared variable — using parallel streams is a good way to increase the performance of your looping logic.

# Conlusion
Java 8 Stream API allows to have code that is more readable and more concise. This is very valuable for making code easier to test and maintain.
Refactoring to using Java 8 Stream is a good habit and can allow you to make small improvements to a new codebase while learning how things work.
So I would recommend you start trying to identify classes or methods that are using old looping patterns and replace them with streams when applicable.