---
layout: post
title:  "Python Basics: Looping over common Data Structures"
date:   2022-05-22 13:10:47 +0200
categories: python
---
### Python Basics: Looping over common Data Structures

Looping is an essential aspect to programming.
Given a data structure which length may vary, it is common to want to repeat an operation over each elements of it.
Instead of repeating the common operation in your code, you can use looping to iterate over all elements in a data structure.

I will give a few examples of how to loop over the common data structures in _Python_ (`string`, `list`, `tuple` & `dictionary`).

This will cover `for` loops and not `while` loops.
The name of the variable(s) inside the loop is for you to define, but keep it mind that you should try to use variable names which makes sense and make your code readable.
### Looping over a string

#### Simple looping over characters
```python
sentence = "How to loop over a string?"
for character in sentence:
    # here you can do any operation which will be repeated
    # for each character in the sentence.
    print(character)
```


#### Looping over characters and positions
We can access the characters of a `string` in _Python_ using their index or position. 

For example if `s = "Baz"`, then `s[0] == 'B'`, `s[1] == 'a'` and `s[2] == 'z'`.

You can notice that index starts from `0` for the first element in _Python_.
This is the same for `string`, `list` and `tuple`.
```python
sentence = "How to loop over a string with the position?"
for position, character in enumerate(sentence):
    # here you can do any operation which will be repeated
    # for each position and character in the sentence 
    # using the "enumerate" function.
    print(position, character)
```

### Looping over a list
#### Simple looping over elements
```python
grades = [11, 18, 16, 19, 14]
for grade in grades:
    # here you can do any operation which will be repeated
    # for each grade in the grades list.
    print(grade)
```

#### Looping over elements and positions
We can access the elements of a `list` in _Python_ using their index or position.

For example if `l = [2, 0, 1]`, then `l[0] == 2`, `l[1] == 0` and `l[2] == 1`.
```python
grades = [11, 18, 16, 19, 14]
for position, grade in enumerate(grades):
    # here you can do any operation which will be repeated
    # for each position and grade in the grades list 
    # using the "enumerate" function.
    print(position, grade)
```

### Looping over a tuple
A `tuple` is an [immutable](https://www.merriam-webster.com/dictionary/immutable) container of elements, which means we can not change/add/remove elements after it is defined.

Looping over a `tuple` is the same as looping over a `list`.
#### Simple looping over elements
```python
names = ("Jane", "John", "Janet")
for name in names:
    # here you can do any operation which will be repeated
    # for each name in the names tuple.
    print(name)
```

#### Looping over elements and positions
We can access the elements of a `tuple` in _Python_ using their index or position.

For example if `t = (1, 2)`, then `t[0] == 1` and `t[1] == 2`.
```python
names = ("Jane", "John", "Janet")
for position, name in enumerate(names):
    # here you can do any operation which will be repeated
    # for each position and name in the names list 
    # using the "enumerate" function.
    print(position, name)
```

### Looping over a dictionary
A `dictionary` is a container of "key/value" pairs, in other words a mapping of elements.

We can access the elements of a `dictionary` in _Python_ using the keys.

For example if `d = {"foo": 1, "bar": 2}`, then `d["foo"] == 1` and `d["bar"] == 2`.
#### Simple looping over keys
A simple loop over a `dictionary` iterates over the **keys** of the `dictionary`.
```python
ages = {"Jane": 25, "John": 26, "Janet": 24}
for name in ages:
    # here you can do any operation which will be repeated
    # for each name (key) in the ages dictionary.
    # you can access the value with the key:
    age = ages[name]
    print(name, age)
```

#### Looping over key/value pairs
The `items` method of the dictionary will return all the key/value pairs.
This way, you can iterate over key/value pairs directly.
```python
ages = {"Jane": 25, "John": 26, "Janet": 24}
for name, age in ages.items():
    # here you can do any operation which will be repeated
    # for each name (key) and age (value) in the ages dictionary.
    print(name, age)
```

#### Looping over values
The `values` method of the dictionary will return all values in a `list`.
This way, you can iterate over key/value pairs directly.
```python
ages = {"Jane": 25, "John": 26, "Janet": 24}
for age in ages.values():
    # here you can do any operation which will be repeated
    # for each age (value) in the ages dictionary.
    print(age)
```

### Looping in practice

#### Use case 1: sum of a list
Given a `list` of grades, of unknown/varying size, we can compute the sum using looping:
```python
grades = [11, 18, 16, 19, 14]
# initialize sum of grades to 0
sum_of_grades = 0
for grade in grades:
    # add current grade to the total sum
    sum_of_grades = sum_of_grades + grade
    
# check result is correct:
assert 78 == sum_of_grades
```

#### Use case 2: average of a list
This is the same as above, we just need to divide the sum by the length of the `list` and round to `2` decimal point.
```python
grades = [11, 18, 16, 19, 14]
# initialize sum of grades to 0
sum_of_grades = 0
for grade in grades:
    # add current grade to the total sum
    sum_of_grades = sum_of_grades + grade

avg_of_grades = round(sum_of_grades / len(grades), 2)
# check result is correct:
assert 15.6 == avg_of_grades
```

#### Use case 3: dictionary of grades
Given a `dictionary` with names as key and a `list` of grades as values of unknown/varying size, we can compute the average for each student using looping:
```python
grades_dict = {"Jane": [14, 16], "John": [11, 12], "Janet": [18, 16]}
# initialize result to an empty dictionary:
avg_of_grades_dict = {}
for name, grades in grades_dict.items():
    # do the same as above inside the loop for each student:
    sum_of_grades = 0
    for grade in grades:
        # add current grade to the total sum
        sum_of_grades = sum_of_grades + grade
    # compute the average for the student:
    avg_of_grades = round(sum_of_grades / len(grades), 2)
    # save the avg in a result dictionary for the student:
    avg_of_grades_dict[name] = avg_of_grades


# check result is correct:
assert {"Jane": 15.0, "John": 11.5, "Janet": 17.0} == avg_of_grades_dict
```
#### Use case 4: common letters (in place) in a string
Given two words (of type `string`), we can find the common letters in the same position using looping:
```python
name = "Jean"
surname = "John"
# initialize result to an empty list:
common_letters_in_place = []
# loop over position and letters in a string with enumerate:
for position, letter in enumerate(name):
    # check if letters in both string are the same at the same position:
    if letter == surname[position]:
        common_letters_in_place.append(letter)

        
# check result is correct:
assert ['J', 'n'] == common_letters_in_place
```

### Code
All the code from this blog post can be found in this [replit](https://replit.com/@19Eric/Python-Basics-Looping-over-common-Data-Structures?v=1#main.py).

