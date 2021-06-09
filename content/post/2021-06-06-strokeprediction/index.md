---
title: 'Stroke Prediction: Battle of the Learning Methods [Currently Filling Post]'
banner: images/stroke_banner.jpg
author: Derek Helms
date: '2021-03-21'
slug: strokeprediction
tags: [Projects]
description: In this project I compare the performance of Statistical and Machine Learning models for predicting whether or not a patient will have a stroke. The data set has a large class imbalance, so comparing the performance of sampling methods with a Generalized Linear Model vs. an Ensemble Model that performs sampling while training is the focus of this project.
---

![](img/header.jpg)

# Alright, I'll admit it...
It sounded a lot cooler in my head. "Battle of the Learning Methods: Statistical vs Machine Learning". Man vs. Machine (well not really, but you get the point). A test of speed, strength, learning, and maybe a little bit of overfitting. Maybe it's just me that's excited about the project, but I finally get to implement what I learn in school against what I do in my free time. This term I took both Sampling Methods and Applied Statistics II, and this brought me to think if I could create a project that implemented the material from those classes and my knowledge of Machine Learning? This project was the answer. 

Stroke is the third leading cause of death in the United States, with over 140,000 people dying annually. Each year approximately 795,000 people suffer from a stroke with nearly 75% of these occurring in people over the age of 65. Using medical record information (age, gender, BMI, smoking, diseases, etc.) we will create models that can accurately predict whether or not a patient has had a stroke. The goal for this project will be to explore the data and find any correlations between features and the response variable stroke that will allow us to engineer new features for the data. After doing this, we will make a comparison between Statistical Modeling and Ensemble Modeling to see which we are able to achieve better results with. *Note: models will be evaluated by an F-Beta and Recall score since avoiding a missed diagnosis is the main focus.*


# Data Exploration & Feature Engineering
The data originated from the [Kaggle](https://www.kaggle.com/fedesoriano/stroke-prediction-dataset) repository for stroke prediction. There are 11 features that were recorded for 5110 observations. Personally I like to perform feature engineering while doing my EDA so that I can create these new features while the ideas are fresh in my mind. From importing the data, our initial data set has the following features:

![](img/head.jpg)

However, the *id* feature is just a unique identifier, so it can be dropped from the data set.

## BMI (Body Mass Index)
From the initial exploration, I found that the only feature that had missing values was *bmi* (with 201 na's). Before filling the missing values, we have the following distribution for *bmi*:
![](img/bmi_init.jpg)

Two important notes here: the data seems to be somewhat normally distributed and we seem to have extreme values for bmi. To handle the missing values, I decided to fill them with the median value +/- random noise between [1,4]. Do I have a logical explanation for why I did this? Of course not, but filling with the median only increased a single value being present, where adding the random noise allowed for a more "even" distribution around the median. On top of this, I filter the data to only include values up to the 99th quantile (approximately bmi = 53) to filter out the outliers. Doing this we used the following code and resulted in the following graph:
``` Python
# Fill missing with values with median +/- random noises between [1,4]
error_term = np.round(np.sqrt(np.random.randint(1, 16, size=data.bmi.isna().sum())),2)
bmi_fill = data.bmi.median() + error_term
data.loc[data.bmi.isnull(), 'bmi'] = bmi_fill

# Remove any values above the 99th quantile (approx BMI = 53)
data = data[data.bmi < np.quantile(data.bmi, 0.99)]
```
![](img/bmi_final.jpg)

Okay so a bit more normal right? Maybe a little right skew in there too? I'm happy with where that's at. Now that we've handle the *bmi* feature it's time to create our first new feature: weight classes based on patient bmi.

### Weight Class (Feature Engineering)
I decided to create weight classes based on the [National Heart, Lung, and Blood Institute BMI Scale](https://www.nhlbi.nih.gov/health/educational/lose_wt/BMI/bmicalc.htm) which gives us the following categories:

- Underweight = Less than 18.5
- Normal weight = 18.5 - 24.9
- Overweight = 25 - 29.9
- Obesity = Greater than 30

``` Python
def weight(row): # see function.py file for function definition
    if row['bmi'] >= 30:
        val = 'obese'
    elif ((row['bmi'] >= 25) & (row['bmi'] < 30)):
        val = 'over weight'
    elif ((row['bmi'] >= 18.5) & (row['bmi'] < 25)):
        val = 'normal weight'
    else:
        val = 'under weight'
    return val
```
![](img/stroke_weight_class.jpg)

So it seems that the majority of the data falls into the *obese* category, followed by *over weight*, *normal weight*, and finally *under weight*. The majority of strokes seem to occur in for patients that fall under either obese or over weight, some for normal weight, and only a single patient that was under weight. If we look further into this (not shown in this article, refer to notebook) we get the following mean ages for each weight group: normal - 33.76, obese - 49.96, over weight - 49.39, and under weight - 10.91. It seems that older patients, who are also more susceptible to strokes, fall into the obese and over weight classes.

## Age

![](img/age_part.jpg)

Age does not seem to have a certain type of distribution for all observations (closest to a uniform distribution). When looking strictly at stroke observations, we can see the majority of the data is above 35 and has an extreme left skew. The distribution for no stroke also seems to not have a certain distribution (but could be classified at closely uniform).

### Age Class (Feature Engineering)
Using the Age Categories from the Canadian Statistics website, I created features for the life cycle groupings defined on their website:

- Children (0-14)
- Youth (15-24)
- Adults (25-64)
- Seniors (65+)

I also created a generic *age_class* feature to be used for graphing. Think of the above individual features as a one-hot-encoding of the age_class feature.

``` Python
data['child'] = np.where(data.age < 15, 1, 0)
data['youth'] = np.where(((data.age >= 15) & (data.age < 25)), 1, 0)
data['adult'] = np.where(((data.age >= 25) & (data.age < 65)), 1, 0)
data['senior'] = np.where(data.age > 65, 1, 0)

def age(row): # see function.py file for function definition
    if row['child'] == 1:
        val = 'child'
    elif row['youth'] == 1:
        val = 'youth'
    elif row['adult'] == 1:
        val = 'adult'
    else:
        val = 'senior'
    return val

data['age_class'] = data.apply(age, axis=1)
```
![](img/stroke_age_class.jpg)

It seems that the majority of the people observed in the data were young adults (25-64). However, people categorized as seniors had the highest amount of strokes. It also looks like both children and youth have very low stroke rates, and looking into this further we can see only two female children (age 1 and 14) had strokes. An important note is that the two children who had strokes were both categorized as obese with a BMI of approximately 30 for both.

## Remaining Features Exploration
The remaining features didn't seem to have too many correlated findings or values that allowed for any feature engineering, so they will be generalized into this section. This will just be a brief overview of a few features, so refer the *stroke_prediction.ipynb* section *Age* & *Other Numerical Features* for a more in-depth exploration.

![](img/glucose_part.jpg)

We can see that *avg_glucose_level* doesn't have a certain distribution (could be considered bimodel), and for both stroke/no stroke we have the same findings (with stroke having a slightly higher second peak).

![](img/stroke_hyper.jpg)

Looking at the above graph, we don't seem to have any clear indicator of *Hypertension* being a key feature for stroke detection. However, if we look at the ratio of stroke to no stroke within the Hypertension classes, we can find that people with Hypertension are about 4x more likely to have a stroke.

![](img/stroke_heart.jpg)

Again looking at the above graph, we don't seem to have any clear indicator of *Heart Disease* being a key feature for stroke detection. However, if we look at the ratio of stroke to no stroke within the Heart Disease classes, we can find that people with Heart Disease are about 5x more likely to have a stroke.

![](img/stroke_gender.jpg)

We seem to have around 800 more females than males in our dataset, but the amount of strokes for each gender seem to be about equivalent. Similarly, their ratio in respect to their group total are about equivalent. There is no clear evidence that one gender is more susceptible to stroke then the other.

## Final Data Processing
Now that we have finished exploring the data, we will want to create a final data set that can be used in the modeling section. We will drop all unused features and one-hot-encode the remaining categorical features.

``` Python
# Drop features that wont be used
data.drop(['work_type', 'Residence_type', 'age_class', 'gender'], axis=1, inplace=True)

# Encode and create any new necessary features
data['ever_married'] = data['ever_married'].replace(['No', 'Yes'], [0,1])
data['age_over_45'] = np.where((data.age >= 45), 1, 0)
data['over_weight'] = np.where((data.bmi >= 25), 1, 0)
data['smokes'] = np.where((data.smoking_status == 'smokes'), 1, 0)
data['never_smoked'] = np.where(((data.smoking_status == 'never smoked') | 
                                 (data.smoking_status == 'Unkown')), 1, 0)
data.drop(['smoking_status', 'child', 'youth', 'adult', 'senior', 'weight_class'], 
          axis=1, inplace=True)
```
![](img/feat_corr.jpg)

Looking at the above matrix it seems that stroke has the highest correlation with the following features: age (0.25), age_over_45 (0.21), heart_disease (0.14), hypertension (0.13), and avg_glucose_level (0.13). There are other features that are correlated with stroke that we engineered, but are lower than the ones listed previously. Now that our data is processed and a subset of the features are kept, we can begin the modeling section.





# Modeling
### Brief Overview of Data Formatting
For the **Statistical Modeling** section, the data was reformatted in two ways to accommodate the large class imbalance (around 20x more observations of "No Stroke" compared to "Stroke"):

- Training data was balanced by using SMOTE (Synthetic Minority Oversampling Technique) to increased of minority "stroke" class to a 3:4 ratio with the majority class "no stroke". This resulted in around 3400 majority observations (0) and 2500 minority observations (1).

- Testing data was balanced using the NearMiss algorithm, which undersampled the majority class to a 4:3 ratio with the minority class. This resulted in around 120 majority and 90 minority observations to be used for evaluation. *Note: when evaluating based on oversampled data, I did not feel the results were as accurate since repeated observations were increasing the scores. I want the model to be prepared for real world data rather than higher metrics on repeated data.*

For the **Ensemble Modeling** section, the data was reformatted in the following ways to accommodate the class imbalance:

- Training data was left untouched since the ensemble algorithms we used are able to handle the imbalance within the model itself.

- Testing data was resampled so that we would have a "Stroke" to "No Stroke" ratio of 2:3, resulting in around 50 minority and 75 majority observations (slightly smaller than the statistical modeling data).

- An important note is that the extra observations from the majority class (after being undersampled) in the testing data were added back into the training data so that we had more data to train on. This was due to the algorithms being able to handle class imbalance (so more majority observations would not have a negative effect).

## Statistical Modeling
*To be fileld in...*


