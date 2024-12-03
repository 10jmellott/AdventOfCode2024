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
```

We now have the lists parsed, let's visualize the randomness of the lists first.


```python
import matplotlib.pyplot as plt

def plot_scatter():
    _, ax = plt.subplots()
    ax.scatter(first_matches, second_matches, s = 1, marker = ',')
    plt.show()

plot_scatter()
```


    
![png](README_files/README_6_0.png)
    


Alright, the data's pretty random, next let's just sort the data and see if we can look at how much the data actually deviates.


```python
import matplotlib.pyplot as plt

first_matches.sort()
second_matches.sort()

plot_scatter()
```


    
![png](README_files/README_8_0.png)
    


The data's not too curved looking at it like this, but ultimately we're interested in the deviations here so let's calculate the difference and sum it up.


```python
sum([abs(second_matches[i] - first_matches[i]) for i in range(len(first_matches))])
```




    2430334



### Part 2

Now instead of estimating the differences between the two lists, we want to estimate the similarities of the two lists. This is computed by looping through the first list, and then multiplying it by the number of times the number appears in the second list, and finally adding them all together.


```python
from collections import Counter

second_matches_count = dict(Counter(second_matches))
filtered_matches = [match for match in first_matches if match in second_matches_count]
```

Now that we have the count of second matches and the value of each valid first match, all that's left is to sum the values. We've plotted the similarities


```python
import matplotlib.pyplot as plt

plt.hist(filtered_matches, bins=10, edgecolor='black')
plt.title('Histogram of Matched Values')
plt.show()
```


    
![png](README_files/README_14_0.png)
    


You can see the rough count of matches, between 2 and 8 for every 10000 values, so it's not a large number. Last all we need to do is cross-calculate the values and sum them up to get our answer.


```python
sum([match * second_matches_count[match] for match in filtered_matches])
```




    28786472



## Day 2: Red-Nosed Reports

So, we've gathered a list of locations for where the Chief Historian might be at. We've wandered over to check the [Red-Nosed Reindeer nuclear fusion/fission plant](https://adventofcode.com/2015/day/19) (this is amazing world building, seriously) and while the elves are checking every corner of the building, we've been asked to help with determining, from a list of reports, which reports are 'safe' for the nuclear fusion/fission plant. I think we need to shut this facility down if I'm being honest.



### Part 1

We want to start with the number of reports that are 'safe'.

For each report, it is considered safe iff

* The levels are either all increasing or all decreasing.
* Any two adjacent levels differ by at least one and at most three.


```python
lines = read_file_to_array('./data/2.txt')
reports_raw = [line.split(' ') for line in lines]
reports = [[int(value) for value in report_raw] for report_raw in reports_raw]
```

Next we need to define a basic function that will help us to parse through a report and tell us if the report is safe or not.


```python
def is_report_safe(report):
    increasing = report[0] < report[1]
    for i in range(len(report) - 1):
        diff = report[i + 1] - report[i]
        if diff == 0:
            return False
        elif increasing and diff < 0:
            return False
        elif not increasing and diff > 0:
            return False
        if abs(diff) > 3:
            return False
    return True

safe_reports = [report for report in reports if is_report_safe(report)]
len(safe_reports)
```




    524



### Part 2

Apparently we can tolerate up to one unsafe level shift per-report by removing it, so instead of returning on the first instance of unsafe instability, we return unsafe when no value can be removed to make the report safe. Since the reports are fairly short, we can heavy-hand this by removing each value one by one to confirm there is no possible level we can remove to make a report safe. Technically we'd only need to check faulty levels, but it's simpler to just loop through each possible level instead.


```python
def tolerant_safe_check(report):
    if is_report_safe(report):
        return True
    for i in range(len(report)):
        if is_report_safe(report[:i] + report[i + 1:]):
            return True
    return False

tolerant_safe_reports = [report for report in reports if tolerant_safe_check(report)]
len(tolerant_safe_reports)
```




    569


