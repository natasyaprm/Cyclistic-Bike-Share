# Introduction

This is a capstone project for Google Data Analytics Professional Certificate. In this case study, I work in the marketing analyst team at a fictional company, Cyclistic, a bike-share company in Chicago. The director of marketing, Lily Moreno, believes the company’s future success depends on maximizing the number of annual memberships. Our team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, our team will design a new marketing strategy to convert casual riders into annual members.

# Problem Statements
The business-related problem statements that could be asked to improve the company's growth rate and success is shared below:
1. How do casual riders and annual members use Cyclistic differently?
2. How can we design new marketing strategies to help convert casual riders into annual members?

# Preparing Data

I've used Cyclistic’s historical trip data to analyze and identify trends. Click [here](https://divvy-tripdata.s3.amazonaws.com/index.html) to download the data (Note: The data has been made available by Motivate International Inc. under this [license](https://ride.divvybikes.com/data-license-agreement)). This is public data that you can use to explore how different customer types are using Cyclistic bikes. 

In this project, i work with Python in JupyterLab for process, analyze, and visualize the data. 



# Processing, Analyzing and Visualizing Data
### Processing and Analyzing
```
--Import all packages we need to the JupyterLab

import pandas as pd
import numpy as np
import datetime as dt
import seaborn as sns
import matplotlib.pyplot as plt
import plotly.express as px
from pandas.api.types import CategoricalDtype
```

The dataset from January 2022 - December 2022

```
--Import the dataset as a .csv file

jan_2022 = pd.read_csv('202201-divvy-tripdata.csv')
feb_2022 = pd.read_csv('202202-divvy-tripdata.csv')
mar_2022 = pd.read_csv('202203-divvy-tripdata.csv')
apr_2022 = pd.read_csv('202204-divvy-tripdata.csv')
may_2022 = pd.read_csv('202205-divvy-tripdata.csv')
june_2022 = pd.read_csv('202206-divvy-tripdata.csv')
july_2022 = pd.read_csv('202207-divvy-tripdata.csv')
aug_2022 = pd.read_csv('202208-divvy-tripdata.csv')
sept_2022 = pd.read_csv('202209-divvy-publictripdata.csv')
oct_2022 = pd.read_csv('202210-divvy-tripdata.csv')
nov_2022 = pd.read_csv('202211-divvy-tripdata.csv')
dec_2022 = pd.read_csv('202212-divvy-tripdata.csv')
```

```
--Merge all of the dataset

data = [jan_2022, feb_2022, mar_2022, apr_2022, may_2022, june_2022, july_2022, 
        aug_2022, sept_2022, oct_2022, nov_2022, dec_2022]
df = pd.concat(data)
df.sample(5)
```

```
-- delete all unecessary columns for the analysis process
df = df.drop(columns=['start_station_name', 'start_station_id', 'end_station_name', 'end_station_id', 
                      'start_lat', 'start_lng', 'end_lat', 'end_lng'])
```

```
-- check null values
df.isnull().sum()
```

```
--check duplicated data
df.duplicated().any()
```
We also check the data type in our dataset to make sure there are no wrong data type

```
df.info()
```
From the dataset info, we know that there are inappropriate data type in started_at and ended_at columns
```
--change data type for started_at & ended_at from object to datetime

df['started_at'] = pd.to_datetime(df['started_at'])
df['ended_at'] = pd.to_datetime(df['ended_at'])
```

Here, we added some columns to help the analysis process
```
--add ride_duration column in minutes

df['ride_duration'] = (df['ended_at'] - df['started_at'])/pd.Timedelta(minutes=1)
df['ride_duration'] = df['ride_duration'].astype(int)
df.head()
```
```
df.sort_values(by='ride_duration')
```
When we sort the values (ascending) by ride_duration column, we can see there are many negative values. It can happened when the time at ended_at column is earlier than the time at started_at
```
--delete columns that contains ride_duration less than one minute (include the negative values)
--ride_duration less than one minute are potentially false starts
df[df['ride_duration'] < 1].count()
df = df[df['ride_duration'] >= 1]
df = df.reset_index()
df = df.drop(columns=['index'])
```

After adding the ride_duration column, we continue to make another column that may support our analysis
```
--add day_of_week column

cats1 = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
df['day_of_week'] = df['started_at'].dt.day_name()
df['day_of_week'] = df['day_of_week'].astype(CategoricalDtype(categories=cats1, ordered=False))
```
```
--add month column
cats2 = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 
         'October', 'November', 'December']
df['month'] = df['started_at'].dt.month_name()
df['month'] = df['month'].astype(CategoricalDtype(categories=cats2, ordered=False))
```

### Visualizing
![Screen Shot 2023-01-18 at 19 09 54](https://user-images.githubusercontent.com/122350261/213168196-59c9643d-6f74-4d35-9c8d-d90a348a8e9b.png)

From the pie chart above, we know that the annual members have 59% from total users and it's shows that the company has more annual members than casual riders

![newplot](https://user-images.githubusercontent.com/122350261/213168564-99d3eed7-f76a-4b24-ba57-c576fd242c2b.png)

From the visualization, we can see:
- More annual members use bike share on weekdays (Mon-Thu)
- On Friday, the day leading to the weekends, the total number of annual members using bike share are decrease. Meanwhile, the casual riders are the opposite.
- More casual riders use bike share on weekends (Sat-Sun)

![Screen Shot 2023-01-18 at 19 21 54](https://user-images.githubusercontent.com/122350261/213170329-f4e00aea-ef4b-42b6-be10-e9804c442dea.png)

The average ride duration from casual riders are higher than the annual members

![Screen Shot 2023-01-18 at 19 23 48](https://user-images.githubusercontent.com/122350261/213170672-af85f238-5e74-4f63-b32f-3123d8810d3a.png)

The data suggests that classic bike is the most type of bike often used by the annual members, followed by the electric bike. Meanwhile, the docked bike has the fewest users that's only used by the casual riders

![Screen Shot 2023-01-18 at 19 32 42](https://user-images.githubusercontent.com/122350261/213172443-c2e2f43e-ab49-402e-94aa-8423425bef38.png)

- There are an increasing number of bike users for annual members from January - August, and January-July for casual riders
- Most users use the bike share in the middle of the year
- In the winter season (November - January) the users that using bike share drops for both category, and increase significantly in the summer

# Recommendations
- We can give special discounts to new annual members who use bike share on weekdays
- Since we know that casual riders use the bike share longer than members, we can give some free ride minutes for annual members who spent more than 20 minutes using the bike on weekdays
- Frequently advertisements on social media during peak months
