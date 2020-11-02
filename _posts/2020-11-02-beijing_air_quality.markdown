---
layout: post
title:      "Beijing Air Quality"
date:       2020-11-02 20:06:39 +0000
permalink:  beijing_air_quality
---


https://andypeng93.medium.com/beijing-air-quality-d078ce66de0c

How is the air quality in Beijing? We hear stories about the air quality, but is the air quality in Beijing really that bad? Through my capstone project, we investigated the air quality and created a predictive model to predict the air quality in Beijing. Before we talk about the results of my project, let’s discuss about our data set.
The data set contains hourly air pollutants data from 12 air quality monitoring sites starting from March 01, 2013 to February 28, 2017. Below is an image of part of our data set of one of the 12 air quality monitoring sites.
Image for post
As you can see we have a total of 18 features and 12 air quality monitoring sites. This is a lot of data to investigate. Therefore for the extent of this project, we will be focusing on the hourly data from Gucheng monitoring site and the feature PM2.5. The index PM2.5 represents particles that have diameter less than 2.5 micrometers, which is more than 10 times thinner than a human hair. These particles are formed as a result of burning fuel and chemical reactions that take place in the atmosphere. It can be coal in a power plant or gasoline in your car. And at this small size, it can get deep into the lungs and bloodstream, and over time, it can destroy your body.
Prior to investigating any features, we need to clean up the data. First we turned the column values of year, month, day and hour into datetime. After that we created a data frame containing the datetime values and the corresponding PM2.5 index values. Since we will be considering the data as time series, we set the time as our index. Finally we performed forward filling on the missing values in our data set.
Image for post
Although our data set is ready for data exploration and modeling, we would first need to understand the PM2.5 index. What do the values mean? What is considered bad or good? Below is a image taken from this link that defines the cutoffs for air quality standards.
Image for post
By using these cutoffs, we graphed the daily average PM2.5 values along with the cutoffs shown above to display where Beijing’s PM2.5 values tend to normally stay around.
Image for post
Based on the above picture, we notice that majority of the time our PM2.5 index is located in the unhealthy for sensitive group to very unhealthy region. Now that we understand the cutoffs and how to identify whether the PM2.5 index is good or not, we will explore the data.
You may wonder, what causes the PM2.5 index to increase? PM2.5 tends to be associated with the effects of burning things. Especially on holidays such as Chinese New Year, China is known for lighting fireworks causing an increase in burning things. Would holidays have an affect on PM2.5 values?
Image for post
Image for post
Although there might be some increases after certain holidays, we cannot say for sure that holidays have an affect on our PM2.5 values. Continuing our data exploration, we grouped our data and calculated the weekly means. Using the weekly mean data we are able to graph a time series decomposition to find out if our data had any trend or seasonality in it.
Image for post
Based on the time series decomposition shown above, we can see that there is no trend and that there is some seasonality in our weekly mean data set. Now let’s move on to modeling. For modeling, I used naive forecasting, linear regression and SARIMA Model.
As a base model, naive forecasting achieves a root mean square error value of 51.64. In other words, our base model can predict the PM2.5 value within a 51.64 interval. Our linear regression model had an root mean square error value of 113.71. This makes sense that it would do worse because there is no way that a straight line would do good in predicting when our data fluctuates a lot.
For our last model, we used SARIMA Model along with grid search to find the optimal parameters for our model to do well. We ended up getting a root mean square error of 56.66 which is slightly worse than the naive forecasting model. The following three pictures show our predictions from each model and how it compares with the original data set. As you can see the best performance would be the Naive Forecasting Model.
Image for post
Image for post
Image for post
To sum it up, is the air pollution in Beijing really that bad? It’s hard to conclude because there’s a chunk of missing data from March 01, 2017 to present day. Using this data set, we weren’t able to predict the air pollution in Beijing. However, we can do a better job at predicting if we gather more updated data or try modeling with neural networks.

