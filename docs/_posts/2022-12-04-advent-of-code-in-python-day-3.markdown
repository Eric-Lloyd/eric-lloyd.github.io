---
layout: post
title:  "AOC: My Day 3 solution explained by an AI tool"
date:   2022-12-04 22:10:47 +0200
categories: python
---

### Disclaimer:
The following blog post has been completely written with the help of [ChatGPT](https://chat.openai.com/chat).
I hope you enjoy it! :)

### Day Instructions
This solution solves the challenge for Advent of Code 2022, Day 3: Rucksack Reorganization. The challenge consists of two parts:
1. For each rucksack, find the common letter between the two compartments. Calculate the sum of the priorities for these letters. 
2. Group the rucksacks into sets of three. For each set, find the common letter between the three rucksacks. Calculate the sum of the priorities for these letters.

### Solution
Here is my solution:
```python
from functools import reduce
from typing import List, Set


def common_letter_priority(sets: List[Set[str]]) -> int:
  """Finds the priority of the common letter in a list of sets"""
  intersection = reduce(lambda s1, s2: s1.intersection(s2), sets)
  assert len(intersection) == 1
  letter = intersection.pop()
  return ord(letter) - 96 if letter.islower() else ord(letter) - 38

def map_rucksack(row: str) -> int:
  mid = len(row) // 2
  sets = [set(row[0:mid]), set(row[mid:])]
  return common_letter_priority(sets)

def map_group(group: List[str]) -> int:
  sets = [set(s) for s in group]
  return common_letter_priority(sets)


with open('input_data.txt', 'r') as input_data:
    rows = [line.strip() for line in input_data.readlines()]

total_part1 = sum([map_rucksack(row) for row in rows])
print(total_part1)

n = 3
groups = (rows[i:i + n] for i in range(0, len(rows), n))
total_part2 = sum([map_group(group) for group in groups])
print(total_part2)
```
To solve this challenge, the solution first defines a `common_letter_priority` function that takes a list of sets and finds the common letter between them. This function calculates the priority of the common letter by checking if it is a lowercase or uppercase letter and converting it to a number using the ASCII code.

Next, the solution defines two mapping functions: `map_rucksack` and `map_group`. The map_rucksack function takes a rucksack and splits it into two compartments, then uses the `common_letter_priority` function to find the common letter and calculate its priority. The map_group function takes a group of rucksacks and uses the `common_letter_priority` function to find the common letter and calculate its priority.

Finally, the solution reads the input data from a file and uses the `map_rucksack` function to solve the first part of the challenge. It then groups the rucksacks into sets of three and uses the `map_group` function to solve the second part of the challenge. The solution prints the total priorities for each part of the challenge.