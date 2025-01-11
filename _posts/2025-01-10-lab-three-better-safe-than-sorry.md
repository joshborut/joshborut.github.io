---
layout: post
title: Better Safe than Sorry (Lab 3)
subtitle: Exploring the Statistics of Health Insurance 
cover-img: /assets/img/insurance_cover.png
thumbnail-img: /assets/img/insurance_thumbnail.png
share-img: /assets/img/insurance_cover.png
tags: [labs]
author: Joshua Borut
---

> "15 minutes on Kaggle can save you 15% or more on data cleaning"
>
> \- A certain green gecko (I think)

Unfortunately for me, 15 minutes turned into more like a hour and a half of looking for the perfect dataset. I initially wanted to analyze a dataset covering **Nvidia stock** until I realized how interesting __insurance__ could be to evaluate. Additionally, I believe that analyzing health insurance data is pertinent to current events as the recent assassination of the __United Health Care__ CEO has prompted discussions about insurance. However, the public dataset I found did not seem particularly credible as it lacked citations and featured many typos. For instance, the following is an unaltered sentence from the documentation:

> "my method the collect is conversation with peoples in hospital and too see datas online the hospistal about insegurance for world with my dataset creation visualization with peoples development method."

After searching for a better dataset to no avail, I decided to use it. I figured that a dataset with a usability rate of __10__ on __Kaggle__ could not be all that bad...right? We'll see if that's really the case.

## Overview

The data includes 7 columns: **age** (age of the insurance holder ), **sex** (sex of the insurance holder), **bmi** (body mass index of the insurance holder), **children** (number of children of the insurance holder), **smoker** (whether or not the insurance holder smokes), **region** (region of residence of the insurance holder), and **charges** (total money paid to insurer from premiums, deductibles, etc). Sadly, much of the documentation for this dataset is lackluster. For instance, it is never said if the charges are annual or over some other time period. Additionally, it is not specified what regions the data refers to. This oversight was not exactly confidence inspiring, but I continued onwards with my analysis.

Another limitation comes from the surveyed ages being between 18 and 64. Since the data ends at age 64, there are very few seniors included in the dataset. So, it does not give a complete picture of health insurance.

The following is an example of rows from the dataset.

| age | sex | bmi | children | smoker | region | charges |
| :-: | :-: | :-: | :------: | :----: | :----: | :-----: |
| 19  | female | 27.9 | 0 | yes | southwest | 16884.924 |
| 18 | male | 33.77 | 1 | no | southeast | 1725.5523 |
| 28 | male | 33 | 3 | no | southeast | 4449.462 |

To begin analyzing the dataset, I created a python notebook where I imported the necessary modules and processed the dataset from a **csv** into a usable dataframe. 

```python
import pandas as pd
import seaborn as sns

DF_PATH = "../datasets/insurance.csv"

df = pd.read_csv(DF_PATH)
```

I also ran some summary statistics to get a sense of the dataset that I reference later in this post.

```python
df.corr(numeric_only=True)
df.describe()
```

Then, it was time to explore some variables!

## Smoker

I first wanted to see the impact of smoking on insurance charges. Although it is quite clear that smoking is harmful, I did not know the exact extent of its effects. After a bit of research, I found that half of chronic smokers die from tobacco usage according to the __World Health Organization__ (__WHO__) ([source](https://www.who.int/news-room/fact-sheets/detail/tobacco)). Hence, it is reasonable to assume that insurance companies would charge larger premiums for customers more prone to illness.

 Before moving any further, I wanted to verify the legitimacy of this dataset by checking if the proportion of smokers from the presumably random sample aligned with the __WHO__'s estimate that 22.3% of people use tobacco globally. I chose to use seaborn's `countplot` with percentages to compare the dataset.

```python
sns.countplot(x=df['smoker'], stat='percent')
```

![Percent of smokers](/assets/img/percent_of_smokers.png)

This chart demonstrates that the number of smokers in the dataset seems reasonable. So, I could move forward with a bit more confidence in my data.


 I decided to use a `violinplot` to confirm my suspicions that smokers pay larger premiums as it displays distributions in an easily interpretable manner. I also printed the means and standard deviation of the two categories (smokers and non-smokers).

 ```python
sns.violinplot(x='charges', y='smoker', data=df)

# Smokers
print(f"Smoker mean: {df[df['smoker'] =='yes']['charges'].mean()}")
print(f"Smoker std: {df[df['smoker'] =='yes']['charges'].std()}")

# Non-smokers
print(f"Non-smoker mean: {df[df['smoker'] =='no']['charges'].mean()}")
print(f"Non-smoker std: {df[df['smoker'] =='no']['charges'].std()}")
```

![Charges vs smoker](/assets/img/charges_vs_smoker.png)

This plot quite clearly shows that smokers experience larger fees for insurance. The difference is so large in the two subsets that only the non-smoker distribution's fourth quartile range and outliers overlap with the smoker distribution. The average cumulative charge for a smoker is about __$32,050.23__ while the average cumulative charge for a non-smoker is approximately __$8,434.27__. As shown in the graph, the distribution for non-smokers is more centralized about the mean with a standard deviation of __$5,993.78__ while the distribution for smokers appears to spread out more with a standard deviation of __$11,541.55__. I would like to find a way to explain this discrepancy but could not find a way to given our current statistics knowledge.

## BMI

I assumed that the most influential factor on the price of insurance other than smoking status is __BMI__. I began by making a simple scatter plot to observe the relationship between BMI and insurance charges incurred. 

```python
sns.scatterplot(x="bmi", y="charges", data=df)
```

![Charges vs BMI](/assets/img/charges_vs_bmi.png)

Upon seeing this graph, it quickly occured to me that the relationship between BMI and charges is not that simple. With a correlation of __0.198341__, BMI seems to generally create a weak upward trend in insurance charges. I realized that, if BMI and smoking compound, it could be beneficial to split the scatter plot into groups of smokers and non-smokers. 

```python
sns.regplot(x="bmi", y="charges", data=df[df['smoker'] == 'yes'])

df[df['smoker'] =='yes'].corr(numeric_only=True)
```

![Charges vs bmi of smoker](/assets/img/charges_vs_bmi_smoker.png)

This graph is much better than the previous one. The linear regression model aligns well with the data, which has a correlation of __0.806481__, which is considerably larger than that of the original combined graph. I observed that the data points seem to be most dense in two groups: points within the range of 20 to 30 BMI and points within the range of 30 to 40 BMI. Although BMI is a disputed method of determining physical health, I found that the __Center for Disease Control__ (__CDC__) considers a BMI between 18.5 and 25 to be "healthy" ([source](https://www.cdc.gov/bmi/adult-calculator/bmi-categories.html)). Therefore, one possible explanation for this unique distribution is that insurance companies increase costs for smoking customers when their BMI falls into the overweight category due to elevated risks of illness.

```python
sns.regplot(x="bmi", y="charges", data=df[df['smoker'] == 'no'])

df[df['smoker'] =='no'].corr(numeric_only=True)
```
![Charges vs bmi of non-smoker](/assets/img/charges_vs_bmi_nonsmoker.png)

The relationship is far less obvious between BMI and insurance costs for non-smokers. Not only is the linear regression model a poor fit, but the correlation is only 0.084037. It could be the case that, because of the extent to which smoking affects health, insurance companies only consider the comparatively less detrimental effects of BMI if they are compounded with smoking. However, this is purely speculative.

## Children

I did not know what to expect looking at the effect of children on insurance prices. Instead of utilizing a `violinplot`, I used a `boxplot` to more easily identify outliers. Also, I wanted to give a different plotting technique an attempt.

```python
sns.boxplot(x='children', y='charges', data=df)
```
![Charges vs children](/assets/img/charges_vs_children.png)

All of the mean charges are around $10,000. So, it makes sense that the correlation between charges and children is only __0.067998__. Unfortunately, the relationship for this column of data turned out to be less interesting than I had hoped. 

## Age

My prediction for the relationship between insurance charges and age felt straightforward; older people are more likely to fall ill, therefore they are charged more for health insurance. I began by creating a scatter plot of age against insurance charges to confirm my hypothesis but found that the correlation was only __0.299008__. 

```python
sns.regplot(x='age', y='charges', data=df[(df['smoker']=='no') & (df['bmi'].between(18.5, 25))])

df[(df['smoker']=='no') & (df['bmi'].between(18.5, 25))].corr(numeric_only=True)
```
![Charges vs age](/assets/img/charges_vs_age.png)

After realizing that the graph seems to have "bands" with upward trends, I used a similar approach to when I analyzed BMI. By looking at insurance customers who had a BMI within the "healthy range" and did not smoke, I could reasonably isolate age as the reason for the difference in pricing. This yielded the following graph.

```python
sns.regplot(x='age', y='charges', data=df[(df['smoker']=='no') & (df['bmi'].between(18.5, 25))])
df[(df['smoker']=='no') & (df['bmi'].between(18.5, 25))].corr(numeric_only=True)
```

![Filtered charges vs age](/assets/img/filtered_charges_vs_age.png)

Now, the correlation was __0.589071__ with the bottommost band successfully separated. Still, the correlation was not particularly strong, but it was higher than that of the previous graph. This graph indicates that age has a direct relationship with the cost of insurance for mostly healthy individuals.

## Sex

I explored the effect of sex on insurance cost simply with a `violinplot`. I did not expect there to be too much difference for the two categories in the dataset, but had suspicions that insurance costs may be more expensive for men because of their lower life expectancy ([Source](https://www.cdc.gov/nchs/fastats/life-expectancy.htm))

```python 
sns.violinplot(x='charges', y='sex', data=df)

print(f"Female std: {df[df['sex'] =='female']['charges'].std()}")
print(f"Male std: {df[df['sex'] =='male']['charges'].std()}")
```

![Charges vs sex](/assets/img/charges_vs_sex.png)

This graph clearly shows that sex has little impact on the cost in insurance. For whatever reason, the second interquartile range for males is larger than that of females, but otherwise the distributions are quite similar (the female standard __$11128.70__ whereas the males' is __$12971.03__). 


## Region

Just as I did with the column for sex, I analyzed the relationship between region and insurance cost with a `violinplot`. I had very few predictions for this relationship because it is not specified in the documentation what regions the data refers to.

```python
sns.violinplot(x='charges', y='region', data=df)

# Southwest
print(f"Southwest mean: {df[df['region'] =='southwest']['charges'].mean()}")
print(f"Southwest std: {df[df['region'] =='southwest']['charges'].std()}")

#Southeast
print(f"Southeast mean: {df[df['region'] =='southeast']['charges'].mean()}")
print(f"Southeast std: {df[df['region'] =='southeast']['charges'].std()}")

# Northwest
print(f"Northwest mean: {df[df['region'] =='northwest']['charges'].mean()}")
print(f"Northwest std: {df[df['region'] =='northwest']['charges'].std()}")

# Northeast
print(f"Northeast mean: {df[df['region'] =='northeast']['charges'].mean()}")
print(f"Northeast std: {df[df['region'] =='northeast']['charges'].std()}")
```

![Charges vs region](/assets/img/charges_vs_region.png)

From this plot, it's quite clear that region has little impact on insurance cost. There are some variations in the interquartile and quartile ranges, but they are rather minimal. Additionally, the standard deviations and means are quite similar in value, as shown in the table below. This made me somewhat skeptical of the dataset as I assumed some premiums would depend on where you live. 

|  Region | Mean ($) | STD ($) |
| :-----: | :--: | :-: |
| Southwest | 12346.937377292308 | 11557.179100748781 |
| Southeast | 14735.411437609888 | 13971.098588991747 |
| Northwest | 12417.57537396923 | 11072.276927579976 |
| Northeast | 13406.384516385804 | 11255.803065578462 |

## Conclusion 

Overall, I found this dataset relatively interesting. Although some trends were predictable, I enjoyed trying to choose the best plots and offer potential explanations for relationships. I still am on the fence about the legitimacy of the data, but regardless it was a helpful exercise to analyze it independently. Some of the higher premiums made sense, but I was astounded by the price of insurance. The average charge across the dataset is __$13270.42__, which makes up around __%16.5__ of the median American income ([source](https://www.census.gov/library/publications/2024/demo/p60-282.html)). This means that health insurance is nearly unaffordable for many just looking to have treatment in case of falling ill or simply visiting the doctor.
