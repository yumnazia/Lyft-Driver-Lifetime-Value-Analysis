# Lyft Driver Life time value analysis
This detailed analysis covers different aspects of a driver's life at Lyft. I have first done some exploratory data analysis to get a better understanding of the data. 
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









