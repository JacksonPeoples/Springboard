# Keeping Austin Roads Safe
Traffic accidents are the leading cause of death in the US for people aged 1-54 [[1]](#1). In Austin specifically, 88 people died in traffic accidents in 2019 [[2]](#2). While there are some promising reasons to think the rate of traffic accidents will decline in Austin (self-driving cars, improved public transit, etc), what could be done tomorrow to make an impact?

## Promising Strategies
A recent study by the National Institute of Justice has shown consistent decrease in accidents not only during, but also after periods of targeted 'High-Visibility Enforcement' by peace officers [[3]](#3). In other words, people are less likely to get in accidents when they believe there is a high likelihood of receiving a citation in a certain area at a certain time. Anecdotally, even the sight of a *cardboard cop* has been effective in changing drivers' behaviors[[4]](#4).
![cardboard cop](https://github.com/JacksonPeoples/Springboard/blob/master/CapstoneTwo/Images/cardboard%20cop.jpg)

## Challenges
While this is a fairly simple solution, there aren't *unlimited* resources to execute the strategy. So, using crash data from TXDOT I've determined where/when most crashes occur and built a model to predict the amount of crashes in a given hour in Austin. These insights and the model should help to inform an effective strategy for placement of both living and cardboard cops to help reduce traffic fatalities.

## 1. Data
TXDOT is responsible for the collection and analysis of crash records submitted by Texas peace officers. They make this data publicly available for up to ten years resulting in a large dataset of incidents useful for insights/modeling. I paired this data with historical weather data from OpenWeather to understand the effects of weather on road safety.
> * https://www.txdot.gov/government/enforcement/crash-statistics.html
> * https://openweathermap.org/api/statistics-api
## 2. Data Cleaning
[Data Wrangling](https://github.com/JacksonPeoples/Springboard/blob/master/CapstoneTwo/DataWrangling.ipynb)

[Data Wrangling 2](https://github.com/JacksonPeoples/Springboard/blob/master/CapstoneTwo/DataWranglingFinal.ipynb)

After some initial observation of the data, it became pretty clear that the location of crashes yielded mostly pretty expected insights. Most crashes in Austin happen on either I-35, US-183 or other highways. I decided therefore to pair down the data to what was useful in predicting the amount of crashes in Austin in a given hour. This meant losing a lot of data that was provided regarding street number, motorcycle involvement, pedestrian involvement, etc. I also needed to groupby hour in order to match the granularity of my weather data.
## 3. EDA
Although the locations of accidents seemed fairly intuitive, I decided to see whether there where any obvious patterns over the course of day when visualized on a map:
![crash_map](https://github.com/JacksonPeoples/Springboard/blob/master/CapstoneTwo/Images/crash_map.png)

From the map it appears that, no matter the time of day, most accidents occur in Central Austin and most commonly on highways. Nothing too surprising.
For the sake of the model, the useful patterns pertained to the regularity of crash numbers over the course of a day of the week:

![weekday](https://github.com/JacksonPeoples/Springboard/blob/master/CapstoneTwo/Images/weekdaycrashes.png)

![weekend](https://github.com/JacksonPeoples/Springboard/blob/master/CapstoneTwo/Images/weekendcrashes.png)

The observed weekday vs weekend patterns were extremely rigid. Even when accounting for the pandemic and rain, the same basic pattern occured just on a smaller and larger scale respectively.

![pandemic](https://github.com/JacksonPeoples/Springboard/blob/master/CapstoneTwo/Images/pancrash.png)

![rain](https://github.com/JacksonPeoples/Springboard/blob/master/CapstoneTwo/Images/raincrash.png)

## 4. Feature Engineering

While there weren't many strong linear correlations observed in the data, many factors had strong explanatory power as binary categorical variables. Therefore I added features such as 'raining' and 'pandemic' as binary 'yes/no' variables as well as many holidays. I also added a features to help capture the weekly cyclicality of the data. These were a rolling average of 3 crash counts lagged by one week as well as the overall average count of a given hour on a given weekday.

## 5. Modeling

For the sake of having a useful train/test split, I did not include 2020 data in my training or test data. This is due to the pandemic's effect on trends and the fact that my model wouldn't be able to train on any data during the pandemic and then also test on it. However, I did include the 'pandemic' feature in the data so I am confident that the model could effectively function during the pandemic once trained on the entirety of available data.

After trying multiple types of regression (Lasso, Ridge, OLS), I ultimately decided to deploy a Random Forest Regressor. It was able to handle the high-dimensionality of the data very effectively. 

![model_performance](https://github.com/JacksonPeoples/Springboard/blob/master/CapstoneTwo/Images/model_perf.png)

## 6. Conclusions/reccomendations

Ultimately, I believe the model performs well enough to be used. My reccomendation to Austin-area law enforcement would be to, as resources allow, choose a critical value of predicted crashes to signal deployment of a mix of real and fake law enforcement. I would randomly assign real/fake cops to said locations to keep drivers cautious.

### References

<a id="1">[1]</a>
'Global Road Safety | CDC' https://www.cdc.gov/injury/features/global-road-safety/index.html

<a id="2">[2]</a>
Austin-American Statesman: 'Austin Traffic Deaths hit 4-year high..' https://www.statesman.com/news/20200215/austin-traffic-deaths-hit-4-year-high-41-are-pedestrians-data-show#:~:text=The%20answer%20has%20become%20increasingly,a%20record%20102%20people%20died.

<a id="3">[3]</a>
'Effect of High-Visibility Enforcement on Motor Vehicle Crashes' https://nij.ojp.gov/topics/articles/effect-high-visibility-enforcement-motor-vehicle-crashes

<a id="4">[4]</a>
https://www.foxnews.com/us/texas-police-roll-out-cardboard-cutouts-in-attempt-to-crack-down-on-speeding-drivers
