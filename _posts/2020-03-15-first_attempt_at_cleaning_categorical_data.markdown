---
layout: post
title:      "First Attempt at Cleaning Categorical Data"
date:       2020-03-16 00:25:03 +0000
permalink:  first_attempt_at_cleaning_categorical_data
---


https://medium.com/@andypeng93/first-attempt-at-cleaning-categorical-data-e0ea8c33161f


How do you deal with categorical data? What should you do to be able to examine categorical data? While working on the Movie Project for Flatiron School, I came upon this problem when wanting to analyze the genres column. As shown below the genres column contain strings of different genres, but how can you analyze them?

IMDB Data Set imdb.title.basics.csv.gz (runtime_data)
Before we continue to investigate the genres column, we need to clean up the data set. The first thing we should do is to get rid of some of these columns because we are only interested in cleaning the genres categorical data. Therefore we will be running the code below to create a data frame containing only the primary title and genres column.
runtime_data = runtime_data[['primary_title', 'genres']]
Next we need to look at the data information by applying the function info() to our data frame. The function info() pulls up all the information in the data frame regarding the number of values and type of values in each column.

Data Information (runtime_data)
On the left we can see that our data frame contains two columns primary title and genres. Both columns are filled with object type data. However, our genres column seem to be missing values. Therefore, we need to clean up our data frame by either filling in those data with the correct genres or deleting the corresponding rows with missing data. Considering how large our data set is, we should remove these rows from our data by running the code below.
runtime_data = runtime_data.dropna()


After cleaning up the data set, we can see that the two columns contain the same number of non-null objects. Now we can focus on the genres column. As you can see above our genres column identify multiple genres that can describe the movie. But how do we use the genres column to do calculations? Let’s go through my thinking process and how I was able to change my data frame into something that can be analyzed.
When first coming upon this issue, I thought to “how do you identify categories in data frame?” To identify categories in data one would use True or False in other words using the number 1 for True or the number 0 for False. What do I want my code to do? I want my code to read each line and be able to assign the correct genre columns with the values 0 or 1.
My first attempt was to write a for loop that would read each row of the genres column and assign 1 or 0 if it is in the genres text or not. The code below first splits the text at each comma for each row in the genres column to create genres which stores a list of genres from the split up text. I was hoping that the next for loop in our code would run through the list and assign 1 to the corresponding genre column while in the same row.
for ind in runtime_data.index:
    genres = runtime_data['genres'][ind].split(',')
    for genre in genres:
        runtime_data[genre] = 1

However, I ended up getting 1's in all the individual genre columns even if the genre wasn’t in the list. After that I thought maybe I need to specify the individual row that I am working on. So for my second attempt I changed my code by adding the corresponding row index at the end of my code shown below. But before running any changed code, I always reset the kernel so that it doesn’t save the previous results.
for ind in runtime_data.index:
    genres = runtime_data['genres'][ind].split(',')
    for genre in genres:
        runtime_data[genre][ind] = 1
I ran the code and ended up getting the error KeyError: ‘Action’. This error basically tells me that they couldn’t assign the corresponding column and row the value of 1 because there is no such column. Therefore, we should create the genre columns with values of 0 before running the above code for this to work. But how can you create the individual genre columns without knowing all the possible genres? I decided to review my previous codes because it managed to create all the individual columns with values of 1. Along with a few changes to the for loop above, I turned it into a function that would return back to us a list of all possible genres in the genres column when given a data frame as an argument.
def genre_list(data):
    glist = []
    for ind in data.index:
        genres = data['genres'][ind].split(',')
        for genre in genres:
            if genre in glist:
                continue
            else:
                glist.append(genre)
    return glist
My next task was to somehow use the list of genres and turn them into individual genre columns in my data frame with values of 0. I decided to write another function that would basically run through my list of genres and create the columns.
def create_df(list_of_genres, data):
    for genre in list_of_genres:
        data[genre] = 0
    return data
After running these two functions back to back, I managed to get my data frame’s individual genre columns to contain only 0’s in each row. Now all we have to do now is to run the code in my second attempt above. This will generate what I want which assigns one if a certain genre appears in the corresponding genres column. As you can see below, in genres column’s first row we have the string ‘Action, Crime, Drama.’ We now have values of 1 in the Action, Crime and Drama column in the first row and 0’s in the other genre columns. Our final result ended up with 1’s in the corresponding genre columns and 0’s in the rest.

I later found out that this method of cleaning categorical data was called “One hot encoding.” One hot encoding is a process by which categorical data are converted into a form where we can quantify categorical data.


We can now count the correct amount of movies that are Dramas. For example, the genre column on the left shows there are 21486 movies with a Drama genre. However, if you look closely on the actual number of movies with the genre Drama is 49887. By cleaning categorical data we can now investigate and do calculations with the genres.
