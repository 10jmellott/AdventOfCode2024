# Advent of Code 2024

Advent of Code is a yearly celebration of coding challenges done every day leading up to Christmas. Each day has two, usually closely related, challenges for you to perform. If you are interested, you can follow the fun for the 2024 season at [adventofcode.com](https://www.adventofcode.com/2024).

## Frontmatter

This helps establish some basic functions we can reuse later on in this document.


```python
def read_file_to_array(file_path):
    with open(file_path, 'r') as file:
        lines = file.readlines()
    return [line.strip() for line in lines]
```

## Day 1: Historian Hysteria

It appears that the Chief **Christmas** Historian is missing, and the chief is known for always attending the sleigh launch. As such, we've taken it upon ourselves to go out there and figure out where they've gone.

### Part 1

In our search for historically significant locations, the elves have come up with two lists of locations (by a unique ID). However, the lists the elves come up with seem to be a little different, so we want to estimate the difference between the lists. This estimate is calculated by reading in the smallest number from each list and getting the difference, then doing the same for the second, third smallest and so on.


```python
import re

lines = read_file_to_array('./data/1.txt')
# Use regex to capture the first and last numbers in each line
pattern = re.compile(r'(\d+)\s+(\d+)')
matches = [pattern.match(line).groups() for line in lines]

first_matches = [int(match[0]) for match in matches]
second_matches = [int(match[1]) for match in matches]

first_matches.sort()
second_matches.sort()

diff = [abs(second_matches[i] - first_matches[i]) for i in range(len(first_matches))]

sum(diff)
```




    2430334



### Part 2

Now instead of estimating the differences between the two lists, we want to estimate the similarities of the two lists. This is computed by looping through the first list, and then multiplying it by the number of times the number appears in the second list, and finally adding them all together.


```python
from collections import Counter

second_matches_count = dict(Counter(second_matches))

fist_matches_similarity = [match * (second_matches_count[match] if match in second_matches_count else 0) for match in first_matches]

sum(fist_matches_similarity)
```




    28786472

