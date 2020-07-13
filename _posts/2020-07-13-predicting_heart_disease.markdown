---
layout: post
title:      "Predicting Heart Disease"
date:       2020-07-13 22:59:06 +0000
permalink:  predicting_heart_disease
---


https://medium.com/@andypeng93/predicting-heart-disease-d805f95b5c71

Can you predict if a patient has heart disease? Let’s give it a try. I will be using the data set from Kaggle for the extent of my Mod 3 Project from Flatiron School. Before we start cleaning the data set, we need to first understand how to interpret the features.
df = pd.read_csv('datasets_33180_43520_heart.csv')
df.head()
Image for post
Heart data set with 13 features columns and 1 target column
As you can see from the data set sample there are 13 feature columns that we need to figure out how to interpret the values. After researching the documentation of each feature column, the following are the results that we found out.
Column Descriptions
age — The age of the patient
sex — The gender of the patient
1: Male
0: Female
cp — Chest pain type (4 values)
Value 0: asymptomatic
Value 1: atypical angina
Value 2: non-anginal pain
Value 3: typical angina
trestbps — Resting blood pressure
chol — Serum cholestoral in mg/dl
fbs — Fasting blood sugar > 120 mg/dl
0: False
1: True
restecg — Resting electrocardiographic results (values 0,1,2)
Value 0: showing probable or definite left ventricular hypertrophy by Estes’ criteria
Value 1: normal
Value 2: having ST-T wave abnormality (T wave inversions and/or ST elevation or depression of > 0.05 mV)
thalach — Maximum heart rate achieved
exang — Exercise induced angina
1: Yes
0: No
oldpeak — ST depression induced by exercise relative to rest
slope — The slope of the peak exercise ST segment
0: downsloping
1: flat
2: upsloping
ca — Number of major vessels (0–3) colored by flourosopy
thal — Thalium stress result
1: fixed defect;
2: normal;
3: reversible defect
target — Whether the patient has heart disease or not
0: disease
1: No disease
For further documentation please refer to this link.
Given the column descriptions, there are two things we need to do.
Edit the target column so that 1 would represent having heart disease and 0 would represent not having heart disease.
df.target = df.target.replace({0:1, 1:0})
df.head()
Image for post
2. Clean up the data set because there are some values that don’t make sense given our column descriptions. For example the ca column have values of 4 when it is suppose to contain only values from 0–3 and the thal column have values of 0 when it is suppose to contain only values from 1–3. Therefore we will be dropping those rows from our data set.
df = df[df.ca != 4]
df = df[df.thal != 0]
Next we need to create dummy variables for categorical columns. However, we will need to convert certain columns from integer/float to category in order to create dummy variables.
# Convert Integer/Float columns to Category
cat = ['sex', 'cp', 'fbs', 'restecg', 'exang', 'slope', 'ca', 'thal', 'target']
for column in cat:
    df[column] = pd.Categorical(df[column])
# Create Dummy Variables
target = df.target
features = df.drop(columns = ['target'], axis = 1)
features = pd.get_dummies(features)
Since we have finish cleaning our data set, we can now move on to data visualizations and modeling.
Data Visualizations
The first feature that I want to investigate on is the gender of the patient.
Image for post
As you can see from the graph above, heart disease appear more in men then women. There are approximately 120 males with heart disease versus 20 females with heart disease. This then led me to ask is there another feature that created this outcome? So I started investigating and found out that out of majority of the patients with heart disease have asymptomatic chest pain.
Image for post
The next feature that I wanted to investigate is the Thalium Stress Result. We can see below that if it is a reversible defect, they tend to have a higher chance with heart disease.
Image for post
The last feature that I wanted to investigate is the age of the patient.
Image for post
In the above box plot, we see that patients with heart disease tend to be older than patients without heart disease. But, what other features are affected as a patient gets older? The first thing that comes to mind is the Maximum Heart Rate the patient’s heart can achieve.
Image for post
As you can see in the above scatter plot as you get older the maximum heart rate decreases for both categories patients with heart disease and patients without heart diseases.
Modeling
For modeling, we will be doing a 80% training set and 20% testing set by using train_test_split function. We will be using Logistic Regression, K Nearest Neighbors, Decision Tree, Random Forest, XGBoost and GridSearchCV to create the best model to predict heart diseases in patients. After going through different combinations of modeling, the following are the results we got from them.
AUC - The best model that we got was through the usage of GridSearchCV and RandomForest to find the best combination to maximize accuracy. By using GridSearchCV we found that the best parameter combination would be the following:
{'criterion': 'gini',
 'max_depth': 5,
 'min_samples_leaf': 4,
 'min_samples_split': 2}
Therefore we ran RandomForestClassifier with the parameters listed above. By running the code below using X_train and y_train which are the variables we got from using the train_test_split function we can calculate the AUC value.
forestBest = RandomForestClassifier(n_estimators = 100, max_depth = 2, criterion = 'gini', min_samples_leaf = 3, min_samples_split = 10)
forestBest.fit(X_train, y_train)
y_scoreforest = forestBest.fit(X_train, y_train).predict_proba(X_test)
fpr, tpr, thresholds = roc_curve(y_test, y_scoreforest[:, 1])
auc(fpr, tpr)
The AUC value we got from this is approximately 0.94216.
Accuracy/Precision/F1 Score - The best model we got for these metrics is by using XGBoost with GridSearchCV. Similar to above we use GridSearchCV to find the best combination to maximize accuracy. The optimal parameters to maximize accuracy are listed below.
learning_rate: 0.1
max_depth: 2
min_child_weight: 2
n_estimators: 100
subsample: 0.5
We then used the classification report function to display the values of precision, recall, F1-score and accuracy.
print(classification_report(y_test, val_predsbest))
Image for post
As you can see in the classification report above, we have a precision value of 0.96, F1-Score of 0.89 and accuracy score of 0.90.
Recall - The best model we got with the highest recall score was by using XGBoost by itself. By using the confusion matrix and classification report function on the predictions we can get the below results.
Image for post
This is the confusion matrix that we got from the XGBoost model. The highlighted model represents the amount of patients we classify as false negatives. In other words 4 out of the 60 patients we classified as not having the disease when they do have the disease.
Image for post
We can see above that the recall score from the XGBoost model is 0.86.
Conclusion
To summarize everything above, we can see from above that to correctly classified a patient as having heart disease we need to consider the following features.
1) Gender — Males have a higher chance at having heart disease than females.
2) Asymptomatic Chest Pain — Individuals with this type of chest pain have a high chance of having heart disease
3) Reversible Defect — If the thalium stress result turns out to be reversible defect, the individual would have a high chance of having heart disease.
4) Age & Maximum Heart Rate — As you get older, your maximum heart rate goes down. We can see that individuals that have heart disease tend to be older and have a lower maximum heart rate.
Our modeling shows that a regular XGBoost is the best model for our problem. This is because we want a model that generates a high recall value in order to minimize the chance of us classifying an individual as false negatives. Being that heart disease is really serious, it would be dangerous if we wrongly classify a person with no heart disease when they indeed do have heart disease.
Have we succeeded in predicting heart disease? Of course not, but our model can be further improved by gathering more patient’s information. We can also look at other features such as whether the patient has a genetic disorder or the body fat percentages of the patient.

