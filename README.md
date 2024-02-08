# Lyft Driver Life time value analysis
This detailed analysis covers different aspects of a driver's life at Lyft. It covers drivers projected life, driver's projected lifetime value, driver churn and segmentation to assess behavior associated with drivers most likely to churn followed by effect of demand on drivers. 

I have first done some exploratory data analysis to get a better understanding of the data. 
We have three datasets:
1. Rides datasets: One that includes information on rides such as driver id, ride id, and the other that includes timestamps associated with the ride specific events. 
2. Drivers dataset: This dataset contains information on drivers including the onboarding dates for each of the drivers.

## Overview of Data
For the purpose of this analysis, we have only utilized information pertaining to drivers who have actually taken rides as most of the attributes are ride-centric. 
I found < 5% of the records to have negative rider wait time, which would imply that the ride left before the driver could get to the pick-up location. For correction, 
I set the value of wait time to NULL where wait time was negative. 
![image](https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/396121ac-c354-45c1-9e74-67acf82511a3)

## Assumptions made for this Analysis
- This dataset is not a representative sample of drivers around the world and as such, the decisions made based on the results may not apply to all drivers.
- Since the data only spans three months, assessing the lifetime of a driver requires the assumption that drivers rarely return after their period of inactivity.
- Prime time rate and the Service Fee have been applied to calculate the total fare which is in line with the actual pricing model of Lyft as described [here](https://www.ridester.com/lyft-cost/).
- Since Lyft keeps a part of the total fare as commission, it is assumed that the percentage is 20% as stated on various sources such as [this one](https://techboomers.com/t/lyft-cost-lyft-pricing).  

## Exploratory Data Analysis
Some of the observations include:
- Exponential increase in the number of rides, from  < 500 rides a day in March to ~ 3000 rides a day in June ( ~ 500% turn over) in this duration.
- Increase in number of drivers from March to June by 607% (From 96 drivers in March to 679 drivers in June).
![image](https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/98cf470e-2f56-4d47-938d-5f415e79b702)
- What's worth noting here is the slight decay in ride count as well as drivers from May some time all the way to end of June. The decline is specially stark in drivers than rides - reason being, apart from the usual decay, there has been no onboarding of drivers after 15th of May.
  
![image](https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/7ef0500e-221a-4bcb-9ed1-37c57cf26c72)
- Only 23% of the total rides taken over the course of 3 months, are taken at nighttime (i.e. between 12 AM and 6 AM).

![image](https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/734eea49-f6e1-4367-8bb1-4d3cd23fe1e2)
- Friday is busiest day of the week for drivers with highest number of rides as compared to others.
- Rides seem to be lowest on Monday, but the number of drivers is healthy which would indicate that it will take rider less time to find a ride.
- Lowest driver count on Sundays, which means higher wait times for rides to get matched.
- Since less number of drivers operate over Sundays, the ride allocation time stays high despite high prime time percentage. 

## Average Projected Lifetime of a Lyft Driver

<img align = "right" width = "400" src = "https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/e4fde0fd-1b97-45f1-8016-03ff18a28ac8"> </img>
- Lifetime of a Lyft driver is the time a driver spends working with the company after getting onboarded. 
- Given the slight skewness, my conclusion would be that the median here will most likely be a better representation of the average projected lifetime.
- Therefore we can conclude that once a driver is onboarded, they will typically continue driving for Lyft for 57 days.
<br> </br>

## Lifetime Value of a driver at Lyft

Lifetime Value of a Lyft driver is the monetary value that a driver generates while riding, it can be seen as the sum of ride fares for the rides a Lyft driver takes in their tenure. In order to assess what factors affect it, I first took a look at correlation which could then maybe used to predict the lifetime value of a driver at Lyft. 
<img width = "500" align= "right" src = "https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/3fe8fccf-b0ef-4d80-84e6-3edc512b8492" > </img>
Based on the correlation results, we can conclude that the lifetime value of a driver is highly correlated with:
- Total Duration – time a driver has spent on rides. 
- Number of rides taken
- Whether or not a ride is an evening ride – since rides and prime time % peaks in evenings creating more opportunity for drivers to earn.
- Active Day proportion – The number of days a driver has actively taken rides during his time at Lyft. 
- Driver Tenure - The total duration a driver has spend riding with Lyft.

Some low impact factors include:
- Arrival Time – Negatively correlated, inversely proportional effect on lifetime value
- Driver Accept Response time – negatively correlated, the more time a driver takes to accept rides, the more chance there is for them to lose opportunity to gain incentive. 

### Estimating Lifetime Value of a Lyft Driver
<img width = "500" align= "right" src = "https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/d4f8f5c1-7954-47bb-ad46-60a576ff0bc3" > </img>
Based on correlation, I have used the following three features to be a part of Regression model for estimating the lifetime value of a driver
- Number of rides taken
- Total ride duration of the rides taken by a driver
- Active day proportion

Given high accuracy (r-squared: 0.98), this model is good to use for LTV prediction. The lifetime value can then be calculated as:
LTV = -18.47 + 3.18 * total_ride_count + 0.70 * total_duration + 118.27 * active_day_proportion

- Considering a mean ride count of 220 rides, a mean duration of 3154 minutes, and  a mean active day proportion of 0.569 (56.9%), it can be estimated that a driver will generate a value of $ 2,956.23 during their lifetime. This is probably an over estimation as there are probably other factors that will affect the Lifetime value here which we do not have access to with this dataset.

## Exploration of Driver Churn
<img src = "https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/ebf37cb0-ed79-4fec-a1c5-b5f9cf5a0622" align = "right" width = "500"> </img>
Based on data, last driver onboarding date is 15th of May, and the last ride date in the dataset is 26th of June.
With the assumptions made regarding the dataset, I have defined a churn indicator based on the following conditions:
- Driver has not taken any ride in the last data month (June 2016) - 26 days of inactivity.
- Driver has taken rides in June but have only been active < 5% of their tenure

I have then used correlation to assess attributes that positively or negatively affect Churn.
- Churn is highly correlated with days_to_last_ride which is the difference between last ride a driver has taken and last ride date of the dataset. 
- Negatively correlated with driver_tenure, the smaller the tenure, the bigger there would be a possibility that a driver will churn.
- Negative correlation with lifetime_value, the negative correlation indicates an inversely proportional relationship meaning the less the value a driver generates, the more chance there exists for a driver to churn.
<br height = "100"> </br>

### Predictive Indicators of Driver Churn
<img src = "https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/be75f3af-284a-40e6-a4f9-5da88a01df60" align = "right" width = "350"> </img>
Based on the correlation, I decided to use the following features to assess their predictive capability for predicting churn:
1. Number of days to last ride - In this analysis, I have used max ride date to calculate the number of days, in a real time environment, this would be current date. 
2. Driver Tenure
3. Lifetime value of a driver 

I have used logistic regression as the outcome variable in this case is Boolean. 
The model was able to predict cases where a driver will churn with 98% accuracy.
The model was  able to predict cases where a driver will not churn with 100% accuracy. 

The high accuracy indicates that the model in conjunction with the specified features, is good to be used for prediction of driver churn.

### Driver Segmentation in order to assess driver behavior associated with Churn
To understand the behavior associated with drivers who are more likely going to leave, I used K-means clustering algorithm to create clusters of drivers based on that associated attributes resulting in 4 clusters described below:
![image](https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/db9b377f-9de0-4788-91ed-ae3c858d63d4)

On the basis of this, we can conclude that the behavior associated with "Bad Drivers" is the best representative of behavior associated with drivers who are more likely to churn than others. 

## Impact of demand for rides, on Lyft Drivers
<img align = "right" width = "500" src = "https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/c9968e94-bef6-4d6c-9d7c-bf8ec651b2bf"> </img>
Based on demand for rides through different times of the day, we can conclude that:
- lowest number of rides as well as drivers between 4-5 AM, highest between 5-6 PM.
- Number of drivers low between 12 – 6 AM, steady increase after 8 AM and in a good number through the day.
- Waiting time to get a ride match as well as prime time % is higher around 8 AM in the morning.
- The prime time % is also higher for closing hours like 5-6 PM because of higher demand as compared to remaining hours of the day.
- The average ride allocation time is higher in the early AM hours because of less drivers available. 

### Correlation between high demand times and other factors
<img align = "right" width = "150" src = "https://github.com/yumnazia/Lyft-Driver-Lifetime-Value-Analysis/assets/12965968/4be50582-3f24-43c1-8c58-b83263dbc43e"> </img>
Since high demand is indicated by prime time %, we can just see how prime time % ties to different driver related factors to assess the effect. 
- The prime time % is negatively correlated with driver response time, the higher the demand, the smaller the response time from drivers to accept ride requests.
- It’s positively correlated with total ride count as drivers, in a high demand time and area do take more frequent rides.
- The prime time % is positively correlated with driver lifetime value because of higher fares as high demand drives high incentives for the drivers, and there are drivers that take advantage of the prime time.

## Actionable Recommendations
- Retaining good drivers: Focusing on driver LTV and the value good drivers bring to Lyft, the business should focus on retaining as well as encouraging middle tier drivers to take more rides, stay longer with the business with an aim to convert them into good/excellent drivers along the way.
- Identify and address issues faced by drivers and riders in marketplace: The business should identify and address the issues faced by the bad drivers, like low earnings, low ride frequency, and low ride duration, to improve their performance. Simultaneously, the business should also identify and address customer related issues from the ratings and feedback given at the end of the ride to the drivers.
- Use real-time models to guide drivers: The business could use a heatmap that drivers can use to be present in the in-demand areas nearby for building higher incentives.
- Use of data driven strategies: Assess performance by submarket (region), ride type, platform to come up with submarket specific data-driven approaches for improvement and expansion.
- Use competitor data (if accessible) to compare performance and practices to understand areas of improvement and opportunity within the business model.
- Assess the KPIs of interest like driver response times, wait-time for a potential rider before ride is matched, ETA for driver to arrive at pickup, ratings per sub-market, analyze variance and come up with better estimates.










