---
layout: post
published: true
title: Exploratory Data Analysis of Titanic Dataset
date: '2017-03-26'
---
As part of DAND P2 Project, I will be doing Exploratory Data analysis on Titanic Dataset.

Let's start with importing required libraries.


```python
%matplotlib inline
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

Now I will read titanic dataset using Pandas read_csv method and explore first 5 rows of the data set.


```python
titanic_df = pd.read_csv('titanic-data.csv')
titanic_df.head()
```




<div>
<table class="dataframe" style="height: 170px;" border="1" width="650">
<thead>
<tr style="text-align: right;"><th>&nbsp;</th><th>PassengerId</th><th>Survived</th><th>Pclass</th><th>Name</th><th>Sex</th><th>Age</th><th>SibSp</th><th>Parch</th><th>Ticket</th><th>Fare</th><th>Cabin</th><th>Embarked</th></tr>
</thead>
<tbody>
<tr><th>0</th>
<td>1</td>
<td>0</td>
<td>3</td>
<td>Braund, Mr. Owen Harris</td>
<td>male</td>
<td>22.0</td>
<td>1</td>
<td>0</td>
<td>A/5 21171</td>
<td>7.2500</td>
<td>NaN</td>
<td>S</td>
</tr>
<tr><th>1</th>
<td>2</td>
<td>1</td>
<td>1</td>
<td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
<td>female</td>
<td>38.0</td>
<td>1</td>
<td>0</td>
<td>PC 17599</td>
<td>71.2833</td>
<td>C85</td>
<td>C</td>
</tr>
<tr><th>2</th>
<td>3</td>
<td>1</td>
<td>3</td>
<td>Heikkinen, Miss. Laina</td>
<td>female</td>
<td>26.0</td>
<td>0</td>
<td>0</td>
<td>STON/O2. 3101282</td>
<td>7.9250</td>
<td>NaN</td>
<td>S</td>
</tr>
<tr><th>3</th>
<td>4</td>
<td>1</td>
<td>1</td>
<td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
<td>female</td>
<td>35.0</td>
<td>1</td>
<td>0</td>
<td>113803</td>
<td>53.1000</td>
<td>C123</td>
<td>S</td>
</tr>
<tr><th>4</th>
<td>5</td>
<td>0</td>
<td>3</td>
<td>Allen, Mr. William Henry</td>
<td>male</td>
<td>35.0</td>
<td>0</td>
<td>0</td>
<td>373450</td>
<td>8.0500</td>
<td>NaN</td>
<td>S</td>
</tr>
</tbody>
</table>
</div>



### Data Description

(from [https://www.kaggle.com/c/titanic](https://www.kaggle.com/c/titanic))

1. survival: Survival (0 = No; 1 = Yes)
2. pclass: Passenger Class (1 = 1st; 2 = 2nd; 3 = 3rd)
3. name: Name
4. sex: Sex
5. age: Age
6. sibsp: Number of Siblings/Spouses Aboard
7. parch: Number of Parents/Children Aboard
8. ticket: Ticket Number
9. fare: Passenger Fare
10. cabin: Cabin
11. embarked: Port of Embarkation (C = Cherbourg; Q = Queenstown; S = Southampton)


### Variable Notes

**pclass**: A proxy for socio-economic status (SES)
1st = Upper
2nd = Middle
3rd = Lower

**age**: Age is fractional if less than 1. If the age is estimated, is it in the form of xx.5

**sibsp**: The dataset defines family relations in this way...
Sibling = brother, sister, stepbrother, stepsister
Spouse = husband, wife (mistresses and fiancés were ignored)

**parch**: The dataset defines family relations in this way...
Parent = mother, father
Child = daughter, son, stepdaughter, stepson
Some children travelled only with a nanny, therefore parch=0 for them.

Now let's see some statistical summary of the imported dataset using pandas.describe() method.


```python
titanic_df.describe()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>714.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>446.000000</td>
      <td>0.383838</td>
      <td>2.308642</td>
      <td>29.699118</td>
      <td>0.523008</td>
      <td>0.381594</td>
      <td>32.204208</td>
    </tr>
    <tr>
      <th>std</th>
      <td>257.353842</td>
      <td>0.486592</td>
      <td>0.836071</td>
      <td>14.526497</td>
      <td>1.102743</td>
      <td>0.806057</td>
      <td>49.693429</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.420000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>223.500000</td>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>20.125000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>7.910400</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>446.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>28.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>14.454200</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>668.500000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>38.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>31.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>891.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>80.000000</td>
      <td>8.000000</td>
      <td>6.000000</td>
      <td>512.329200</td>
    </tr>
  </tbody>
</table>
</div>



The output DataFrame index depends on the requested dtypes:

For numeric dtypes, it will include: count, mean, std, min, max, and lower, 50, and upper percentiles.

From above table I see that mean of survived column is 0.38, but since this is not complete dataset we cannot conclude on that.

Count for 'Age' column is 714, it means dataset has some missing values. I will have to cleanup the data before I start exploring.


#### Data cleanup

Now let's get some info on datatypes in the dataset using pandas.info() method. It will give us concise summary of a DataFrame.



```python
titanic_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 891 entries, 0 to 890
    Data columns (total 12 columns):
    PassengerId    891 non-null int64
    Survived       891 non-null int64
    Pclass         891 non-null int64
    Name           891 non-null object
    Sex            891 non-null object
    Age            714 non-null float64
    SibSp          891 non-null int64
    Parch          891 non-null int64
    Ticket         891 non-null object
    Fare           891 non-null float64
    Cabin          204 non-null object
    Embarked       889 non-null object
    dtypes: float64(2), int64(5), object(5)
    memory usage: 83.6+ KB
    

I see that there are some missing values in 'Age', 'Cabin' and 'Embarked' columns. I'll not use 'Cabin' which is the most missing and will ignore it. There are some columns which are not required in my analysis so I will drop them. For the missing 'Ages' and 'Embarked' I will omit those rows when I use the data.



```python
titanic_cleaned = titanic_df.drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
titanic_cleaned.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Fare</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>7.2500</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>71.2833</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>7.9250</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>53.1000</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>8.0500</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>




```python
titanic_cleaned.describe()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>714.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>0.383838</td>
      <td>2.308642</td>
      <td>29.699118</td>
      <td>0.523008</td>
      <td>0.381594</td>
      <td>32.204208</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.486592</td>
      <td>0.836071</td>
      <td>14.526497</td>
      <td>1.102743</td>
      <td>0.806057</td>
      <td>49.693429</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.420000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>20.125000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>7.910400</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>28.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>14.454200</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>38.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>31.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>80.000000</td>
      <td>8.000000</td>
      <td>6.000000</td>
      <td>512.329200</td>
    </tr>
  </tbody>
</table>
</div>




```python
titanic_cleaned.isnull().sum()
```




    Survived      0
    Pclass        0
    Sex           0
    Age         177
    SibSp         0
    Parch         0
    Fare          0
    Embarked      2
    dtype: int64



#### Data exploration:

As part this project I want to explore answers to following questions.

1. How Survival is correlated to other attributes of the dataset ? Findout Pearson's r.
2. Did Sex play a role in Survival ?
3. Did class played role in survival ?
4. How fare is related to Age, Class and Port of Embarkation ?
5. How Embarkation varied across different ports ?

Lets start with Q1.

#### Q1. How Survival is correlated to other attributes of the dataset ? Findout Pearson's r.

I will compute pairwise correlation of columns(excluding NA/null values) using pandas.DataFrame.corr method. I will use 'pearson' standard correlation coefficient for the calculation.


```python
titanic_cleaned.corr(method='pearson')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Survived</th>
      <td>1.000000</td>
      <td>-0.338481</td>
      <td>-0.077221</td>
      <td>-0.035322</td>
      <td>0.081629</td>
      <td>0.257307</td>
    </tr>
    <tr>
      <th>Pclass</th>
      <td>-0.338481</td>
      <td>1.000000</td>
      <td>-0.369226</td>
      <td>0.083081</td>
      <td>0.018443</td>
      <td>-0.549500</td>
    </tr>
    <tr>
      <th>Age</th>
      <td>-0.077221</td>
      <td>-0.369226</td>
      <td>1.000000</td>
      <td>-0.308247</td>
      <td>-0.189119</td>
      <td>0.096067</td>
    </tr>
    <tr>
      <th>SibSp</th>
      <td>-0.035322</td>
      <td>0.083081</td>
      <td>-0.308247</td>
      <td>1.000000</td>
      <td>0.414838</td>
      <td>0.159651</td>
    </tr>
    <tr>
      <th>Parch</th>
      <td>0.081629</td>
      <td>0.018443</td>
      <td>-0.189119</td>
      <td>0.414838</td>
      <td>1.000000</td>
      <td>0.216225</td>
    </tr>
    <tr>
      <th>Fare</th>
      <td>0.257307</td>
      <td>-0.549500</td>
      <td>0.096067</td>
      <td>0.159651</td>
      <td>0.216225</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



From above correlation table we can see that Survival is inversly correlated to Pclass value. In our case since Class 1 has lower numerical value, it had better survival rate compared to other classes.

We also see that Age and Survival are slighltly correlated.

We will try to visualize these corelation below.

This brings us to Q2

#### Q2. Did Sex play a role in Survival ?

Lets pull a histogram of 'Survived' column.


```python
#titanic_cleaned.groupby(['Survived']).hist()

sns.factorplot('Survived', data=titanic_df, kind='count')
sns.plt.title('Count of Passengers who survived')
```


![output_18_1.png]({{site.baseurl}}/img/output_18_1.png)



Let's see agewise distribution of the passenger aboard the Titanic.


```python
#Histogram of Age of the given data set(sample)
#plt.hist(titanic_cleaned['Age'].dropna())
sns.distplot(titanic_cleaned['Age'].dropna(), bins=15, kde=False)
sns.plt.ylabel('Count')
sns.plt.title('Agewise distribution of the passenger aboard the Titanic')
```

![output_20_1.png]({{site.baseurl}}/img/output_20_1.png)



Many passensgers are of age 15-40 yrs. But again this is not complete dataset.

Now I would like to see agewise distribution of passsengers for both Genders. I will do this by plotting the rows where 'Sex' is Male and Female respectively.


```python
#Age wise Distribution of Male and Female passengers
sns.plt.hist(titanic_cleaned['Age'][(titanic_cleaned['Sex'] == 'female')].dropna(), bins=7, label='Female', histtype='stepfilled')
sns.plt.hist(titanic_cleaned['Age'][(titanic_cleaned['Sex'] == 'male')].dropna(), bins=7, label='Male', alpha=.7, histtype='stepfilled')
sns.plt.xlabel('Age')
sns.plt.ylabel('Count')
sns.plt.title('Age wise Distribution of Male and Female passengers')
sns.plt.legend()
```


![output_22_1.png]({{site.baseurl}}/img/output_22_1.png)



There were many male passengers aboared compared to female passengers.

I will do a agewise distribution plot for passenges who Survived across both Genders by filtering out rows where 'Survived' = 1.


```python
#Age wise Distribution of Male and Female survivors
sns.plt.hist(titanic_cleaned['Age'][(titanic_cleaned['Sex'] == 'female') & (titanic_cleaned['Survived'] == 1)].dropna(), bins=7, label='Female', histtype='stepfilled')
sns.plt.hist(titanic_cleaned['Age'][(titanic_cleaned['Sex'] == 'male') & (titanic_cleaned['Survived'] == 1)].dropna(), bins=7, label='Male', alpha=.7, histtype='stepfilled')
sns.plt.xlabel('Age')
sns.plt.ylabel('Count')
sns.plt.title('Age wise Distribution of Male and Female survivors')
sns.plt.legend()
```


![output_24_1.png]({{site.baseurl}}/img/output_24_1.png)



From above visualization, it is evident that Women had better survival chance. One can do an Hypothesis test to verify this.

Lets take a look for youngest and oldest passenger to survive.


```python
yougest_survive = titanic_cleaned['Age'][(titanic_cleaned['Survived'] == 1)].min()
youngest_die = titanic_cleaned['Age'][(titanic_cleaned['Survived'] == 0)].min()
oldest_survive = titanic_cleaned['Age'][(titanic_cleaned['Survived'] == 1)].max()
oldest_die = titanic_cleaned['Age'][(titanic_cleaned['Survived'] == 0)].max()

print "Yougest to survive: {} \nYoungest to die: {} \nOldest to survive: {} \nOldest to die: {}".format(yougest_survive, youngest_die, oldest_survive, oldest_die)
```

    Yougest to survive: 0.42 
    Youngest to die: 1.0 
    Oldest to survive: 80.0 
    Oldest to die: 74.0
    

#### Q3. Did class played role in survival ?

Next, let's look at survival based on passenger's class for both genders.

We can do this by grouping the dataframe with respect to Pclass, Survived and Sex.


```python
#sns.plt.hist(titanic_cleaned.groupby(['Pclass', 'Survived', 'Sex']).size())
grouped_by_pclass = titanic_cleaned.groupby(['Pclass', 'Survived', 'Sex'])
grouped_by_pclass.size()
```




    Pclass  Survived  Sex   
    1       0         female      3
                      male       77
            1         female     91
                      male       45
    2       0         female      6
                      male       91
            1         female     70
                      male       17
    3       0         female     72
                      male      300
            1         female     72
                      male       47
    dtype: int64




```python
titanic_cleaned.groupby(['Pclass', 'Sex']).describe()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>Age</th>
      <th>Fare</th>
      <th>Parch</th>
      <th>SibSp</th>
      <th>Survived</th>
    </tr>
    <tr>
      <th>Pclass</th>
      <th>Sex</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="16" valign="top">1</th>
      <th rowspan="8" valign="top">female</th>
      <th>count</th>
      <td>85.000000</td>
      <td>94.000000</td>
      <td>94.000000</td>
      <td>94.000000</td>
      <td>94.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>34.611765</td>
      <td>106.125798</td>
      <td>0.457447</td>
      <td>0.553191</td>
      <td>0.968085</td>
    </tr>
    <tr>
      <th>std</th>
      <td>13.612052</td>
      <td>74.259988</td>
      <td>0.728305</td>
      <td>0.665865</td>
      <td>0.176716</td>
    </tr>
    <tr>
      <th>min</th>
      <td>2.000000</td>
      <td>25.929200</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>23.000000</td>
      <td>57.244800</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>35.000000</td>
      <td>82.664550</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>44.000000</td>
      <td>134.500000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>63.000000</td>
      <td>512.329200</td>
      <td>2.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th rowspan="8" valign="top">male</th>
      <th>count</th>
      <td>101.000000</td>
      <td>122.000000</td>
      <td>122.000000</td>
      <td>122.000000</td>
      <td>122.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>41.281386</td>
      <td>67.226127</td>
      <td>0.278689</td>
      <td>0.311475</td>
      <td>0.368852</td>
    </tr>
    <tr>
      <th>std</th>
      <td>15.139570</td>
      <td>77.548021</td>
      <td>0.658853</td>
      <td>0.546695</td>
      <td>0.484484</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.920000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>30.000000</td>
      <td>27.728100</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>40.000000</td>
      <td>41.262500</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>51.000000</td>
      <td>78.459375</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>80.000000</td>
      <td>512.329200</td>
      <td>4.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th rowspan="16" valign="top">2</th>
      <th rowspan="8" valign="top">female</th>
      <th>count</th>
      <td>74.000000</td>
      <td>76.000000</td>
      <td>76.000000</td>
      <td>76.000000</td>
      <td>76.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>28.722973</td>
      <td>21.970121</td>
      <td>0.605263</td>
      <td>0.486842</td>
      <td>0.921053</td>
    </tr>
    <tr>
      <th>std</th>
      <td>12.872702</td>
      <td>10.891796</td>
      <td>0.833930</td>
      <td>0.642774</td>
      <td>0.271448</td>
    </tr>
    <tr>
      <th>min</th>
      <td>2.000000</td>
      <td>10.500000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>22.250000</td>
      <td>13.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>28.000000</td>
      <td>22.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>36.000000</td>
      <td>26.062500</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>57.000000</td>
      <td>65.000000</td>
      <td>3.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th rowspan="8" valign="top">male</th>
      <th>count</th>
      <td>99.000000</td>
      <td>108.000000</td>
      <td>108.000000</td>
      <td>108.000000</td>
      <td>108.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>30.740707</td>
      <td>19.741782</td>
      <td>0.222222</td>
      <td>0.342593</td>
      <td>0.157407</td>
    </tr>
    <tr>
      <th>std</th>
      <td>14.793894</td>
      <td>14.922235</td>
      <td>0.517603</td>
      <td>0.566380</td>
      <td>0.365882</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.670000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>23.000000</td>
      <td>12.331250</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>30.000000</td>
      <td>13.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>36.750000</td>
      <td>26.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>70.000000</td>
      <td>73.500000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th rowspan="16" valign="top">3</th>
      <th rowspan="8" valign="top">female</th>
      <th>count</th>
      <td>102.000000</td>
      <td>144.000000</td>
      <td>144.000000</td>
      <td>144.000000</td>
      <td>144.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>21.750000</td>
      <td>16.118810</td>
      <td>0.798611</td>
      <td>0.895833</td>
      <td>0.500000</td>
    </tr>
    <tr>
      <th>std</th>
      <td>12.729964</td>
      <td>11.690314</td>
      <td>1.237976</td>
      <td>1.531573</td>
      <td>0.501745</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.750000</td>
      <td>6.750000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>14.125000</td>
      <td>7.854200</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>21.500000</td>
      <td>12.475000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.500000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>29.750000</td>
      <td>20.221875</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>63.000000</td>
      <td>69.550000</td>
      <td>6.000000</td>
      <td>8.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th rowspan="8" valign="top">male</th>
      <th>count</th>
      <td>253.000000</td>
      <td>347.000000</td>
      <td>347.000000</td>
      <td>347.000000</td>
      <td>347.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>26.507589</td>
      <td>12.661633</td>
      <td>0.224784</td>
      <td>0.498559</td>
      <td>0.135447</td>
    </tr>
    <tr>
      <th>std</th>
      <td>12.159514</td>
      <td>11.681696</td>
      <td>0.623404</td>
      <td>1.288846</td>
      <td>0.342694</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.420000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>20.000000</td>
      <td>7.750000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>25.000000</td>
      <td>7.925000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>33.000000</td>
      <td>10.008300</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>74.000000</td>
      <td>69.550000</td>
      <td>5.000000</td>
      <td>8.000000</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



I would also like to see the survival rate across all the class.
I can do this by taking sum of survived passengers for each class and divide it by totla number of passenger for that class and multiplying by 100. I will use pandas groupby function to segregate passengers according to their class.


```python
titanic_cleaned.groupby(['Pclass'])['Survived'].sum()/titanic_cleaned.groupby(['Pclass'])['Survived'].count()*100
```




    Pclass
    1    62.962963
    2    47.282609
    3    24.236253
    Name: Survived, dtype: float64



I see Class did play role in survival of the passengers.
Now let's visualize the same.


```python
sns.factorplot('Survived', col='Pclass', data=titanic_cleaned, kind='count', size=7, aspect=.8)
plt.subplots_adjust(top=0.9)
sns.plt.suptitle('Class wise segregation of passengers', fontsize=16)
```


![output_33_1.png]({{site.baseurl}}/img/output_33_1.png)



Above visualization compares passengers who survived the tragedy and who did not, across three classes.
We can also drill down further to visualize survival of passengers of both genders across 3 classes.


```python
sns.factorplot('Survived', col='Pclass', hue='Sex', data=titanic_cleaned, kind='count', size=7, aspect=.8)
plt.subplots_adjust(top=0.9)
sns.plt.suptitle('Class and gender wise segregation of passengers', fontsize=16)
```



![output_35_1.png]({{site.baseurl}}/img/output_35_1.png)



From above visualization we can see that class played important for Survival of Male and Female passengers.
This brings us to my next questions.

#### Q4. How fare is related to Age, Class and Port of Embarkation ?
#### Q5. How Embarkation varied across different ports ?

Let's see how Fare varies with respect to Age and Port of Embarkation. I will do a scatterplot of passengers from 3 classes for Age and Fare on X and Y axis.


```python
sns.lmplot('Age', 'Fare', data=titanic_cleaned, fit_reg=False, hue="Pclass", scatter_kws={"marker": ".", "s": 20})
sns.plt.title('Scatterplot of passengers w.r.t Fare and Age')
```


![output_37_1.png]({{site.baseurl}}/img/output_37_1.png)



I can segregate the passengers according to thier Port of Embarkation and then compare Fare v/s Age across 3 classes.


```python
sns.lmplot('Age', 'Fare', data=titanic_cleaned, fit_reg=False, hue="Pclass", col="Embarked", scatter_kws={"marker": ".", "s": 20})
plt.subplots_adjust(top=0.9)
sns.plt.suptitle('Scatterplot of passengers w.r.t Fare and Age for diff. ports', fontsize=16)
```



![output_39_1.png]({{site.baseurl}}/img/output_39_1.png)



From above visualization we can see that Fare is quite uniform for Class 2 and 3 across all ages. Fare varies for Class 1 across all ages, but we cannot conclude why it varies. We need more attributes to our data points to drill down to the reason for variation. We can also observe that lot of passengers embarked from port of Southampton.

#### Conclusions

From my exploratory analysis of Titanic dataset we conclude that, women had higher chances of survival. We can do a t test to come up with chances(probability) of survival. I also see that Class(Socio-Economic status) of the passengers had played a role in their survival. I also compared fare across different classes and found that it varied a lot for Class 1 passenger, although I could not conclude as to why it varried diffrently for Class 1 due to insufficient data.

There were some limitation for this dataset such as missing values for some attributes of passesngers. This is not in any form a exhaustive study. More can be done on this data set.

#### Reference:
Websites:
* [https://www.kaggle.com](https://www.kaggle.com)
* [https://chrisalbon.com](https://chrisalbon.com)
* [http://seaborn.pydata.org/tutorial.html](http://seaborn.pydata.org/tutorial.html)

Books:
Python Data Science Handbook, By - Jake VanderPlas
