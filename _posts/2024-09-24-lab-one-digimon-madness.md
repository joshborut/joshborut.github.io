---
layout: post
title: "Lab 1: Digimon Madness"
subtitle: Battle with the Digimon Dataset
cover-img: /assets/img/digimon_cover.png
thumbnail-img: /assets/img/digimon_thumbnail.png
share-img: /assets/img/digimon_cover.png
tags: [labs]
author: Joshua Borut
---

For our first lab, we were asked to explore the Digimon dataset from __Kaggle__. This dataset includes 249 "Digimon," which I learned means "digital monster." In the dataset, each column describes a different statistic pertaining to a unique digital monster.

Here is a sample of the first three Digimon from the dataset:

| Number | Digimon | Stage | Type | Attribute | Memory | Equip Slots | HP  | SP | Atk | Def | Int | Spd |
| :----- | :------ | :---- | :--- | :-------- | :----- | :---------- | :-- | :- | :-- | :-- | :-- | :-- |
| 1      | Kuramon | Baby  | Free | Neutral   | 2      | 0           | 590 | 77 | 79  | 69  | 68  | 95  |
| 2      | Pabumon | Baby  | Free | Neutral   | 2      | 0           | 950 | 62 | 76  | 76  | 69  | 68  |
| 3      | Punimon | Baby  | Free | Neutral   | 2      | 0           | 870 | 50 | 97  | 87  | 50  | 75  |


With this in mind, I downloaded the  file `digimon.csv` to my folder `datasets/` and got to work. 

# 1. *What is the average HP of all Digimon?*

I began *question #1* by creating the getter `get_average_hp()` in my file `digimon_reader.py`. With the dataset open, I used the method `DictReader(<CSV File>)` from the CSV module to convert the file into an iterable.

```python
def get_average_hp():
    with open('datasets/digimon.csv', 'r') as f:
        data = csv.DictReader(f)
```

 I then looped through the elements of the resultant iterable, each of which corrosponds to a Digimon, and added their health points to `hp_list`. 

 ```python
hp_list = []
for digimon in data:
            hp_list.append(float(digimon['HP']))
 ```

 I  found the __sum__ of everything in `hp_list` with `sum(<Iterable>)` and divided that value by the __length__ of the list using `len(<Iterable>)` to find the average health points.

 ```python
 average_hp = (sum(hp_list) / len(hp_list))
return average_hp
```


All together, my function looks like this: 

```python
def get_average_hp():
    # Open the dataset
    with open('datasets/digimon.csv', 'r') as f:
        # Parse the dataset to an iterable
        data = csv.DictReader(f)
        # Initialize the HP list
        hp_list = []
        # Loop through each row/digimon 
        for digimon in data:
            # Add the HP of the Digimon to the HP list
            hp_list.append(float(digimon['HP']))
        # Average the values from the HP list
        average_hp = (sum(hp_list) / len(hp_list))
        # Return the average value
        return average_hp

# Printing the return
print(get_average_hp())
```
The function returns  __1210.8835341365461__ as the average HP. So...

### Answer: 1210.8835341365461

# 2. *Write a function that can count the number of Digimon with a specific attribute. For example, `count_digimon("Type", "Vaccine")` would return 70. The function should be able to take __any__ attribute and value as arguments.*

I started by defining the function `count_digimon(category, value)` in the same file as the previous getter. Although the question refers to the category as "attribute," I chose to represent it as "category" because __Attribute__ is technically a category itself (*see table at top*). I opened the file and applied `DictReader(<CSV File>)` once again. 

```python
def count_digimon(category, value):
    with open('datasets/digimon.csv', 'r') as f:
        data = csv.DictReader(f)
```

Next, I initialized a variable `count` that I could increment each time I found a match from the dataset. Then, I looped through each Digimon, checking if `digimon[category] == value` to see when desired conditions were met. Each time this was true, I added one to `count`. Lastly, I returned the `count`. 

```python
 for digimon in data:
            if digimon[category] == value:
                count += 1
```

This left me with the following:

```python
def count_digimon(category, value):
    # Open the dataset
    with open('datasets/digimon.csv', 'r') as f:
        # Parse the dataset to an iterable
        data = csv.DictReader(f)
        #Initialize the counter
        count = 0
        # Loop through each row/Digimon
        for digimon in data:
            # Check if the desired condition is true
            if digimon[category] == value:
                # Increment the counter
                count += 1
                # Return the total count
        return count

# Printing the return
print(count_digimon("Type", "Vaccine"))
```

The function returns 70 like the example from the question says. This means that our function works!

# 3. *The Digimon on your team are restricted by the total amount of Memory that they need. If your team only has 15 Memory, name a team of up to 3 Digimon that has at least 300 attack (Atk) in total.*

## Brute-Force Approach

*Wowza* that is a long question. The first thing that came to mind was a __brute-force method__. While not ideal, it would at least serve as a starting point. Like before, the first step was to define a getter function, this time named `get_team()`. Then, I opened the CSV file and parsed the data to an iterable with `DictReader(<CSV File>)`.

```python
def get_team():
    with open('datasets/digimon.csv', 'r') as f:
        data = csv.DictReader(f)
```

Now, it was time to __brute-force__ the dataset. Since the question asks about a team of three, I knew this would entail three layers of iteration. I called the iterator `digimon_1` and assigned the Digimon's __Memory__ and __Atk__ to variables. I had to apply the `float(<Number or String>)` function to each value from the dictionary because they were techincially Strings. The last step of this layer of iteration was to check if the single `digimon_1` fulfilled our team requirements, in which case I returned a list with just `digimon_1`. If not, the code moved to the next layer of iteration.

```python
for digimon_1 in data:
    digimon_1_mem = float(digimon_1['Memory'])
    digimon_1_atk = float(digimon_1['Atk'])
    if digimon_1_mem <= 15 and digimon_1_atk >= 300:
        return [digimon_1['Digimon']]
    else:
        # Next layer of iteration
        pass
```

The subsequent layer followed the same logic, except it now checked if the __sums__ of the __Memory__ and __Atk__ fulfilled their respective conditions from *question #2*.

```python 
for digimon_2 in data:
    digimon_2_mem = float(digimon_2['Memory'])
    digimon_2_atk = float(digimon_2['Atk'])
    if (digimon_1_mem + digimon_2_mem) <= 15 and (digimon_1_atk + digimon_2_atk) >= 300:
        return [digimon_1['Digimon'], digimon_2['Digimon']]
    else:
        # Next layer of iteration
        pass
```

The third and final layer of iteration followed the same form.

```python
 for digimon_3 in data:
    digimon_3_mem = float(digimon_3['Memory'])
    digimon_3_atk = float(digimon_3['Atk'])
    if (digimon_1_mem + digimon_2_mem + digimon_3_mem) <= 15 \
            and (digimon_1_atk + digimon_2_atk + digimon_3_atk) >= 300:
        return [digimon_1['Digimon'], digimon_2['Digimon'], digimon_3['Digimon']]
```
Put together, these created the following function:

```python
def get_team():
    # Open the dataset
    with open('datasets/digimon.csv', 'r') as f:
        # Parse the dataset to an iterable
        data = csv.DictReader(f)
        # Loop through each row/Digimon
        for digimon_1 in data:
            # Store digimon_1 Memory
            digimon_1_mem = float(digimon_1['Memory'])
             # Store digimon_1 Atk
            digimon_1_atk = float(digimon_1['Atk'])
             # Check if digimon_1 fulfills the conditions
            if digimon_1_mem <= 15 and digimon_1_atk >= 300:
                # Return the team with just digimon_1
                return [digimon_1['Digimon']]
            # Move to next layer of iteration
            else:
                # Loop through each row/Digimon
                for digimon_2 in data:
                    # Store digimon_2 Memory
                    digimon_2_mem = float(digimon_2['Memory'])
                    # Store digimon_2 Atk
                    digimon_2_atk = float(digimon_2['Atk'])
                    # Check if the team with digimon_1 and digimon_2 fulfills the conditions
                    if (digimon_1_mem + digimon_2_mem) <= 15 and (digimon_1_atk + digimon_2_atk) >= 300:
                        # Return the team with digimon_1 and digimon_2
                        return [digimon_1['Digimon'], digimon_2['Digimon']]
                    # Move to next layer of iteration
                    else:
                        # Loop through each row/Digimon
                        for digimon_3 in data:
                            # Store digimon_3 Memory
                            digimon_3_mem = float(digimon_3['Memory'])
                            # Store digimon_3 Atk
                            digimon_3_atk = float(digimon_3['Atk'])
                            # Check if the team with digimon_1, digimon_2, and digimon_3 fulfills the conditions
                            if (digimon_1_mem + digimon_2_mem + digimon_3_mem) <= 15 \
                                    and (digimon_1_atk + digimon_2_atk + digimon_3_atk) >= 300:
                                # Return the team with digimon_1, digimon_2, and digimon_3
                                return [digimon_1['Digimon'], digimon_2['Digimon'], digimon_3['Digimon']]

# Print the return
print(get_team())
```

The first team that this function finds is `['Kuramon', 'Pabumon', 'Ogremon']`. So...

### Answer: Kuramon, Pabumon, and Ogremon

Even though this code finds the correct answer, it is not the best given how many levels of iteration we do. Additionally, there is a possibility that this solution does not find the optimal team of three given our constraints; instead, it finds the first team that at least meets the minimum requirements.

# Takeaways
The process of this lab improved my __ability to use and interpret CSV files__ in Python. Additionally, it improved my __comprehension of dictionaries__ and of strategies to iterate through them. Not many of my methods failed initially for this lab because I had a sense of what I had to do from the start, but I still think that there is room for improvement for my solution to *question #3*.

#### Disclaimer: I spoke with *Charlie Kerfoot* during this lab.