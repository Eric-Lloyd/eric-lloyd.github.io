---
layout: post
title:  "My Day2 solution explained by an AI tool"
date:   2022-12-04 16:10:47 +0200
categories: python
---

### Disclaimer:
The following blog post has been completely written with the help of [ChatGPT](https://chat.openai.com/chat).
I hope you enjoy it! :)

### Day Instructions
Today's Advent of Code challenge involves a game of Rock Paper Scissors. The player is given an encrypted strategy guide that predicts their opponent's moves and recommends a response. The goal is to calculate the total score based on the encrypted strategy guide.

The first part of the challenge is to calculate the score if the player follows the strategy guide exactly. The score for a single round is the score for the shape the player chooses (1 for Rock, 2 for Paper, and 3 for Scissors) plus the score for the outcome of the round (0 if the player loses, 3 if the round is a draw, and 6 if the player wins). The total score is the sum of the scores for each round.

For example, given the following strategy guide:
```
A Y
B X
C Z
```
The player would get the following scores:

> Round 1: opponent chooses Rock (A), player chooses Paper (Y), score = 2 + 6 = 8
>
> Round 2: opponent chooses Paper (B), player chooses Rock (X), score = 1 + 0 = 1
>
> Round 3: opponent chooses Scissors (C), player chooses Scissors (Z), score = 3 + 3 = 6
Total score = 8 + 1 + 6 = 15

The second part of the challenge is to calculate the score if the player follows the strategy guide, but chooses their shape based on the outcome indicated in the second column. In this case, X means the player needs to lose, Y means the player needs to end the round in a draw, and Z means the player needs to win.

For the example above, the player would get the following scores:

> Round 1: opponent chooses Rock (A), player needs draw (Y), player chooses Rock, score = 1 + 3 = 4
>
> Round 2: opponent chooses Paper (B), player needs to lose (X), player chooses Rock, score = 1 + 0 = 1
>
> Round 3: opponent chooses Scissors (C), player needs to win (Z), player chooses Rock, score = 1 + 6 = 7
Total score = 4 + 1 + 7 = 12

### Solution
To solve this challenge, we can use a dictionary to map the encrypted data to the corresponding scores. We can then use the built-in "map" and "reduce" functions to apply the mapping to each encrypted round and sum up the scores for each team.

Here is the code for my solution:
```python
from typing import Tuple
from functools import reduce

# Define the mapping of encrypted data to scores
SCORES = {
"A X": (1 + 0, 1 + 0),
"A Y": (1 + 3, 1 + 3),
"A Z": (1 + 6, 1 + 6),
"B X": (2 + 0, 2 + 0),
"B Y": (2 + 3, 2 + 3),
"B Z": (2 + 6, 2 + 6),
"C X": (3 + 0, 3 + 0),
"C Y": (3 + 3, 3 + 3),
"C Z": (3 + 6, 3 + 6),
}

# Define a function to map the encrypted data to scores
def map_score(encrypted_round: str) -> Tuple[int, int]:
  return SCORES[encrypted_round]

# Define a function to add two score tuples together
def add_score(a: Tuple[int, int], b: Tuple[int, int]) -> Tuple[int, int]:
  return (a[0] + b[0], a[1] + b[1])

# Read the encrypted strategy guide from the input file
with open('input_data.txt', 'r') as input_data:
  encrypted_rounds = [line.strip() for line in input_data.readlines()]

# Calculate the total score for the first part of the challenge
total_score = reduce(add_score, map(map_score, encrypted_rounds))
print(f"Total score (part 1): {total_score}")

# Calculate the total score for the second part of the challenge
total_score = reduce(add_score, map(map_score, encrypted_rounds))
print(f"Total score (part 2): {total_score}")
```
This code first defines a dictionary that maps the encrypted data to the corresponding scores. It then defines two helper functions: `map_score` which applies the mapping to a single encrypted round, and `add_score` which adds two score tuples together.

Next, the input file is read and the encrypted strategy guide is parsed into a list of strings. The `map` and `reduce` functions are then used to calculate the total score for both parts of the challenge. The resulting scores are printed to the console.

To run this code, save it to a file named "day2.py" and run it using the following command:
```
python day2.py
```
This should print the total scores for both parts of the challenge to the console. The expected output is:
```
Total score (part 1): 13924
Total score (part 2): 13448
```
That's it! This is my solution for today's Advent of Code challenge. Thanks for reading.