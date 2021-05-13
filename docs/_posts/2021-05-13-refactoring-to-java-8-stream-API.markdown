---
layout: post
title:  "Refactoring code to Java 8"
date:   2021-05-13 16:10:47 +0200
categories: java
---
When going through some Java codebase which are fairly old but that have migrated to Java 8 or later version, I often find code blocks which could be _refactored_. What I mean here by _refactoring_ is that these code blocks could leverage the Java 8 APIs to become more readable and also more up to date.

In this blog post, I would like to share some common patterns that I have come across, and how they can be changed — focusing on the usage of Java 8 Stream API.

## Filtering
One very common action in programming is to filter a collection of elements. To do this prior to Java 8, you would do the following:
```java
 List<String> longWords(final List<String> words, final int threshold) {
     List<String> longWords = new ArrayList<>();
     for (String word : words) {
        if (word.length() > threshold) {
            longWords.add(word);
        }
    }
    return longWords;
}
```
The above method could be replaced with the following:
```java 
List<String> longWords(final List<String> words, final int threshold) {
    return words.stream()
            .filter(word -> word.length() > threshold)
            .collect(toList());
}
```
This is more concise, and more readable.

## Grouping
Another common action is to group elements from a list, based on a specific condition or predicate. 
For example, a word counter would group words by length. To do this prior to Java 8, you would do the following:
```java
Map<Integer, List<String>> groupByWordLength(final List<String> words) {
    Map<Integer, List<String>> groups = new HashMap<>();
    for (String word : words) {
        int length = word.length();
        if (groups.containsKey(length)) {
            List<String> group = groups.get(length);
            group.add(word);
        } else {
            List<String> group = new ArrayList<>();
            group.add(word);
            groups.put(length, group);
        }
    }
    return groups;
}
```
This is starting to be already quite verbose for such a common task. Thankfully it becomes a lot more simpler leveraging Java 8 Stream:
```java
Map<Integer, List<String>> groupByWordLength(final List<String> words) {
    return words.stream()
            .collect(groupingBy(word -> word.length()));
}
```
This _one liner_ is also a lot more readable.

## Partitioning 
One slightly less common but still frequent action is to partition a list, based on a specific condition or predicate. Continuing with our "words" example, we can partition words based on their lengths. To do this prior to Java 8, you would do the following:
```java

List<List<String>> partitionByWordLength(final List<String> words, final int threshold) {
    List<String> shortWords = new ArrayList<>();
    List<String> longWords = new ArrayList<>();
    for (String word : words) {
        if (word.length() > threshold) {
            longWords.add(word);
        } else {
            shortWords.add(word);
        }
    }
    return Arrays.asList(shortWords, longWords);
}
```
With Java 8 Stream API, you can refactor the method to be:
```java

List<List<String>> partitionByWordLength(final List<String> words, final int threshold) {
    Collection<List<String>> partition = words.stream()
        .collect(partitioningBy(word -> word.length() > threshold))
        .values();
    return new ArrayList<>(partition);
}
```
Here we need to convert the `Collection` to an `ArrayList` because the type returned by `partitioningBy` collector is a `Map`.
We could have simply returned the `Map` of `List`s which contains two keys, `true` and `false`.