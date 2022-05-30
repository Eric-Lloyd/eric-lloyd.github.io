---
layout: post
title:  "Python Basics: Functions"
date:   2022-05-29 18:57:47 +0200
categories: python
---
## Python Basics: Functions

Functions are the building blocks of programming.

A function is block of code which is used to execute a task/action or data transformation.
It can be called multiple times, with different inputs.

We will decipher this definition in the following blog post.
I will explain how to **define** a function in _Python_ and how it can be **called** multiple times with different inputs. 
Using a function can make your code clearer, more organised and more readable. I will give a few use cases to illustrate this.

### Print function
Usually the first thing you learn in _Python_ (or any programming language) is to display the sentence "Hello World!" to the terminal output.
This is how the code would look like:
```python
print("Hello World!")
```
Here what we are actually doing is calling the _built-in_ `print` function with a string as argument with the value `"Hello World!"`.

You could simplify and imagine the `print` function to be defined the following way:
```python
def print(value):
    # do something with the value to 
    # display it on the terminal output.
    # ...
    return None
```
This imaginary `print` function has a **parameter** called `value`. It then displays the `value` to the terminal and returns `None`.
We will explain in more details what is a **parameter** a and what is a `return` statement later.

The `print` function is very valuable because you do not need to know how the value you give it is printed to the terminal, you only care about the result.
It is used a lot of times throughout programs.
The name `print` clearly states the intent of the function, which is to display or print to the terminal output.

Similarly, when we build our own functions, we will try to:
* hide implementation details
* give them a meaningful name
* make them as reusable as possible



### Building your own functions
In _Python_, you can use many _built-in_ functions, meaning functions that come with the language such as `print`, `len` (you can find more details about _built-in_ functions [here](https://docs.python.org/3/library/functions.html)).
More importantly, you can **define** your own functions in your programs, this is what we will focus on from now on.
#### How to define a function
To **define** your own function, you need to:
* start with the keyword `def`
* after that you need **parenthesis** with, optionally, the parameters of your function
* then you need a **semicollon** to start your function block
* finally the block or content of the function (what it does)

> The first line when you define a function is called the **function signature**, it has the keyword (`def`) the name of the function and optionally some parameters.

Remember that all the code in your function should be **indented** inside the function **block**.
#### Defining a function with no parameters
Here is an example of what is described above for a function with no parameters:
```python
def my_first_function():
    print("Hello inside a function!")
```
Here the name of the function is `my_first_function`, it takes no parameters: there is nothing inside the parenthesis. 
It then calls the `print` _built-in_ function.

#### Defining a function with parameters
Here are two examples of functions with parameters:
```python
def greeting(name):
    print(f"Hello {name}, from inside a function!")

def polite_greeting(title, surname):
    print(f"Dear {title} {surname}, greetings from inside a function!")
```
Here the name of the first function is `greeting`, it takes one parameter called `name` (inside the parenthesis).

The name of the second function is `polite_greeting`, it takes two parameters called `title` and `surname` (inside the parenthesis).

Again, they both call the `print` _built-in_ function, this time with a [f-string](https://realpython.com/python-f-strings/).

Once you have **defined** your functions, if you try to run your program, you will see that nothing happens.
This is expected: for your functions to be executed you will need to `call` them.

### How to call a function
Calling a function is equivalent to executing the code inside your function.
It will be called with the **arguments** (or values) that you pass to it.

To call your first function, you simply do the following:
```python
my_first_function()
```
This has to be below your function definition in your program.

You can call it multiple times, it will execute the code inside your functions each time:
```python
my_first_function()
my_first_function()
my_first_function()
```

To call the greetings functions, which take parameters there are multiple ways.
I will illustrate below 3 different ways:
 
#### 1. With inline variables
In this method, we directly pass inline values to the function:
```python
greeting("Jane")
polite_greeting("Mrs", "Jackson")
```
* We pass the value `"Jane"` directly to the `greeting` function.
When the function is executed, we have `name="Jane"` inside the function block.

* We pass the value `"Mrs"` and `"Jackson"` directly to the `polite_greeting` function.
When the function is executed, we have `title="Mrs"` `surnname="Jackson"` inside the function block.

#### 2. With existing variable(s)
In this method, we pass existing variables to the function:
```python
first_name = "John"
greeting(first_name)
title = "Dr"
surname = "Jackson"
polite_greeting(title, surname)
```
* We pass the variable `first_name` to the `greeting` function.
When the function is executed, we have `name=first_name` inside the function block (which is equivalent to `name="John"`).

* We pass the variables `title` and `surname` to the `polite_greeting` function.
When the function is executed, we have `title=title` `surnname=surname` inside the function block (which is equivalent to `title="Dr"` `surnname="Jackson"`).

> Here it is important to note that the variable we pass the function (called argument) does not have to have the same name as the function parameter.

#### 3. With keyword arguments
In this method, we can pass either inline values or variable using the parameter name explicitly followed by an equal sign:
This method is more verbose, but it is in my opinion is the more intuitive one when learning:
```python
first_name = "John"
greeting(name=first_name)
title = "Dr"
surname = "Jackson"
polite_greeting(title=title, surname=surname)
``` 
We are explicitly saying that we want the variable inside our `greeting` function block name to be `first_name` when we call it.

Here is an example with inline values:
```python
polite_greeting(title="Mrs", surname="Jackson")
``` 

### Function return statement in _Python_
For your functions to be useful in your program, it is common for them to execute some kind of data transformation and return a result.
To do this in _Python_ (and most programming languages), you can use the `return` statement.
Here is a very simple example: 
```python
def add(a, b):
    result = a + b
    return result
``` 
The above function takes 2 parameters, `a` and `b`, it computes the sum of these variables and saves it into a `result` variable.
It then **returns** this `result` variable.
When you call it, you can assign its return value to a variable like so:
```python
c = add(5, 5)
# here c = 10

d = add(1, 5)
# here d = 6

one = 1
two = 2
f = add(one, two)

g = add(c, d)
# here g = 16
``` 
Here you see an example of how a function called `add` can be used and called multiple times. 
It can be called directly with values (example `c` and `d`) or with existing variables (example `f` and `g`)


> In _Python_, a function always returns a value. If you do not explicitly write a return statement in your function definition, _Python_ will add an implicit `return None`.
In other words if a function has not explicit return statement, it will return `None`.

You can check this by assigning the execution of a function with no implicit return to a variable and printing its value:
```python
def does_nothing():
    pass

nothing = does_nothing()
# here nothing = None
``` 

### Use case 1: average of a list
Imagine we have a list grades for which we want to compute the average.
Here is the code I would use without a function. 
```python
grades_1 = [12, 15, 10, 17]

sum  = 0
for grade in grades_1:
    sum += grade
avg = round(sum / len(grades_1), 2)
```
We are summing all the elements in the list, and then dividing the sum by the length of the list, rounding the result to maximum 2 decimal.

This works, but it is not reusable. If I want to do it again for a different list of grades, for example `grades_2 = [18, 15, 11, 13]`, 
I would have to copy and paste my code above and tweak it for it to work with the `grades_2` variable.
However, if I create a function called `average`, I can reuse it easily on the 2 lists. Let's do it:
```python
def average(grades):
    sum  = 0
    for grade in grades:
        sum += grade
    avg = round(sum / len(grades), 2)
    return avg
```
This is the same code as above, just extracted into a function.

Now that we have the function, we can **call** it or apply it multiple times:
```python
grades_1 = [12, 15, 10, 17]
grades_2 = [18, 15, 11, 13]
avg_1 = average(grades=grades_1)
# here avg_1 = 13.5
avg_2 = average(grades=grades_2)
# here avg_2 = 14.25
```

### Use case 2: formatting a string
Imagine we are given list of names with different formats, and we want all the names to have the same format.
For this, we will write a function with converts all the names to _title case_ using the `title()` string method.
```python
def format(names):
    result = []
    for name in names:
        formatted_name = name.title()
        result.append(formatted_name)
    return result


unformatted_names = ["JANE", "john", "Janet"]
formatted_names = format(names=unformatted_names)
# here formatted_names = ['Jane', 'John', 'Janet']
```
Here if we are ever given more list of unformatted names, we can reuse our function to format them.


### Conclusion
This blog post has just introduced functions, why they are useful in programming and how to **define** them and **call** them in _Python_.

### Code
All the code from this blog post can be found in this [replit](https://replit.com/@19Eric/Python-Basics-Functions?v=1#main.py).

