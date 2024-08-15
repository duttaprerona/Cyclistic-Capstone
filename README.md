 # **Case Study: How Does a Bike-Share Navigate Speedy Success?**
 By Prerona Dutta

 **Table of Contents**
* [Introduction](#Introduction)
* [Ask](#Ask)
* [Prepare](#Prepare)
* [Process](#Process)
* [Analyse](#Analyse)
* [Share](#Share)
* [Act](#Act)

  <a id="Introduction"></a>
# **Introduction**

This project is part of the [Google Data Analytics Capstone](https://www.coursera.org/learn/google-data-analytics-capstone) course specifically Case Study 1. It follows six steps of the data analysis process:
* Ask
* Prepare
* Process
* Analyze
* Share
* Act

<a id="Ask"></a> 
# **Step 1: Ask**

This step looks at the problem and objectives of our case study and its desired outcome.

### 1.1 Background
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members.

### 1.2 Business Objective and Expected Results
The main business objective in this case is to discover how casual riders and annual members use Cyclistic bikes differently. Both the Director of Marketing as well as finance analysts have concluded that annual members are more profitable. 

Therefore, results from this analysis will be used to provide reccomendations that can help the marketing team come up with strategies to increase annual members.

<a id="Prepare"></a>
# **Step 2: Prepare**
In this step, the data being used and its sources are identified.

### 2.1 Data Information
The datasets used for this analysis has been made available by Motivate International Inc. [here](https://divvy-tripdata.s3.amazonaws.com/index.html) which is separated by months and quaters, each having its own csv. It is a public dataset and is also reliable.12 months of trip data was downloaded and stored for further analysis. 

<a id="Process"></a>
# **Step 3: Process**
The 'Process' step has been conducted to :
* Explore and observe the data
* Merge data sets
* Check for null values & missing values
* Check for duplicate values and remove them
* Transform data - format data type

This step helps ensure the data is clean and free from outliers before moving ahead with further analysis. Python (Jupyter Notebook) has been used as the main tool for processing and analysing the data.
### **3.1 Code**
**Collecting Data**
# Importing all necessary libraries
import numpy as np
import pandas as pd

# Importing all the 12 csv files into Dataframes
```
df1 = pd.read_csv(r"C:\Users\dutta\OneDrive\Desktop\Case Study 1\Q12020_tripdata.csv")
df4 = pd.read_csv(r"C:\Users\dutta\OneDrive\Desktop\Case Study 1\202004_tripdata.csv")
df5 = pd.read_csv(r"C:\Users\dutta\OneDrive\Desktop\Case Study 1\202005_tripdata.csv")
df6 = pd.read_csv(r"C:\Users\dutta\OneDrive\Desktop\Case Study 1\202006_tripdata.csv")
df7 = pd.read_csv(r"C:\Users\dutta\OneDrive\Desktop\Case Study 1\202007_tripdata.csv")
df8 = pd.read_csv(r"C:\Users\dutta\OneDrive\Desktop\Case Study 1\202008_tripdata.csv")
df9 = pd.read_csv(r"C:\Users\dutta\OneDrive\Desktop\Case Study 1\202009_tripdata.csv")
df10 = pd.read_csv(r"C:\Users\dutta\OneDrive\Desktop\Case Study 1\202010_tripdata.csv")
df11 = pd.read_csv(r"C:\Users\dutta\OneDrive\Desktop\Case Study 1\202011_tripdata.csv")
df12 = pd.read_csv(r"C:\Users\dutta\OneDrive\Desktop\Case Study 1\202012_tripdata.csv")
```
**Data Wrangling and Cleaning**
# Cocatenating all the files 
```
df_comb = [df1,df4,df5,df6,df7,df8,df9,df10,df11,df12]
df = pd.concat(df_comb)
``` 
# Checking all the columns to get a better idea
```
df.info()
```
![image](https://github.com/user-attachments/assets/c89fed42-5558-4779-86e0-3780dca9eb57)
# Dropping the columns (index wise) not required i.e 'start_lat', 'start_lng', 'end_lat', 'end_lng'.
```
df.drop(df.columns[[8,9,10,11]], axis=1, inplace=True )
```
# Dropping Rows with Null Values
```
df.dropna(inplace= True)
```
# Counting the no. of duplicates on 'ride_id column'
```
df.ride_id.duplicated().sum()
```
# Dropping Duplicate on the basis of 'ride_id column'
```
df.drop_duplicates(subset='ride_id', keep= 'first', inplace= True)
```
**Data Transformation**
# Transforming 'started_at' and 'ended_at' column to datetime format and creating a new column for date
```
df['date'] = pd.to_datetime(df['started_at']).dt.date
df['start_time'] = pd.to_datetime(df['started_at']).dt.time
df['end_time'] = pd.to_datetime(df['ended_at']).dt.time
```
# Dropping the columns 'started_at' and 'ended_at'
```
df.drop(df.columns[[2,3]], axis=1, inplace=True )
```
# Checking the first five rows to see the updates
```
df.head()
```
**Finding day of the week, ride length and month for future analysis**
# Importing libraries for further transformation
```
from datetime import date
from datetime import datetime
from datetime import time
import calendar
```
# Creating a column for day of the week
```
df['date'] = pd.to_datetime(df['date'])
df['day_of_week'] = df['date'].dt.day_name()
```

# Finding the time difference between start and end time to get the ride length.
# Converting ride length in seconds for future calculations
```
CURRENT_DAY = date.today()

def convert_to_time(seconds: int) -> time:
    diff_hours = seconds // 3600
    diff_minutes = (seconds // 60) % 60
    diff_seconds = seconds % 60
    return time(hour=diff_hours, minute=diff_minutes, second=diff_seconds)

def get_time_delta_in_s(st: time, et: time) -> int:
    start_time = datetime.combine(CURRENT_DAY, st)
    end_time = datetime.combine(CURRENT_DAY, et)
    return (end_time - start_time).seconds 

diff_list = []
diff_in_s_list = []

for (st, et) in zip(df['start_time'], df['end_time']):
    total_seconds = get_time_delta_in_s(st, et) 
    diff_in_s_list.append(total_seconds)
    diff_list.append(convert_to_time(total_seconds))
df['ride_length'] = pd.Series(diff_list)
df['ride_length_in_s'] = pd.Series(diff_in_s_list)
```
# Creating a column for month
```
df['month'] = df['date'].dt.month_name()
```
# Removing the columns for start and end station id
```
df.drop(df.columns[[3,5]], axis=1, inplace=True )
```
**Final Reveal**

Checking the data once more before further analysis.

```
df.head()
```
<a id="Analyse"></a>
# **Step 4: Analyze**

In this step, data has been analyzed  and summarised using descriptive statistics. 
Firstly,the number of annual members and casual riders in the dataset were compared.
# Percentage of casual users and annual members
```
df['member_casual'].value_counts(normalize= True)
```
![image](https://github.com/user-attachments/assets/76aa58d7-91a7-4cb5-ab8a-936ce2c09656)

As we can see, members take up more than 61% of the dataset as compared to the casual riders who take around 38%. Next,the ride length data for both bike users have been summarised.
# Summarising ride length for both the bike users
```
df.groupby(["member_casual"]).describe()
```
![image](https://github.com/user-attachments/assets/c874b96b-b1f9-43c6-9436-d32ac518c5f1)

Although the number of annual members are higher, casual users on average have a higher ride length. 

<a id="Share"></a>
# **Step 5: Share**

For the share step, further data analysis is conducted along with visualizations to present the findings. The data visualizations are created using Matplotlib and Seaborn libraries.
### **5.1 Data Visualizations and Further Analysis**
```
import matplotlib.pyplot as plt
import seaborn as sns
```
**Differences between the casual users and annual members**
# Count of casual riders and annual members
```
sns.countplot(x= 'member_casual', data= df)
plt.xlabel('User Type')
plt.ylabel('Count')
```
# Average trip duration
```
t = pd.pivot_table(data=df,index='member_casual',values='ride_length_in_s',aggfunc=np.average)
sns.barplot(t.index, t['ride_length_in_s'])
plt.xlabel('Rider Type')
plt.ylabel('Average Trip Duration(in seconds)')
```
**No. of bike users over the week by user type**
# Weekly Trend
```
pd.crosstab(df['member_casual'], df['day_of_week'])
sns.countplot(x= 'day_of_week',hue= 'member_casual',data= df)
```
It can be observed that both casual users and annual members commonly rent the bikes on Saturday. However,annual members tend to be using the bikes mostly on weekdays with Wednesday being the most common day.
**Monthly trend in rides by user type**
# Monthly Trend
```
pd.crosstab(df['member_casual'], df['month'])
trip = pd.crosstab(df['month'], df['member_casual'])
trip
trip.plot(kind= 'barh', stacked= True)
plt.xlabel('No.of Users')
plt.title('Trips by Month')
plt.xticks(rotation=0, ha='center')
```
July, August and September seemed to have the highest number of users among both user types. This could be influenced by Chicago's cyclable weather in those months which remains around 25-30 celsius degrees as presented by [Current Results](https://www.currentresults.com/Weather/Illinois/Places/chicago-temperatures-by-month-average.php).

**Bike preferences for users**
# Types of bike preffered
```
pd.crosstab(df['member_casual'], df['rideable_type'])
sns.countplot(x= 'rideable_type', hue= 'member_casual', data=df,)
plt.xlabel('Bike Type')
plt.ylabel('Count')
```
It is clear that docked bike is the most preffered bike type among both users.
<a id="Act"></a>
# **Step 6: Act**

Finally, in the Act step we will be delivering our insights and providing recommendations based on our analysis.

### **Conclusion**

Based on the above findings, here are some takeaways:
1. There are more Member bike users in comparison to Casual bike users.
2. Casual users were found to take longer trips or rides on average than Annual Members.This could mean that casual users rented Cyclistic bikes for leisure.
3. Casual users often ride on weekends whereas Members use the bikes more over the week than on weekends. This could indicate that annual Members are using the bikes to commute to work. Additionally, both casual and annual members commonly rent the bike on Saturdays.
4. Rides may be influenced by weather with July, August, September having the highest number of riders.
5. Docked bike is the most preferred type of ride among both users followed by electric bike.

### **Recommendations**
The following recommendations might encourage Casual riders to upgrade to annual membership thus increasing revenue and profits at Cyclistic bike-share:
* Offer a discount for users who take long rides or discounts based on certain distance.
* Introduce a weekend only membership that costs less than the current weekly membership.
* Create more campaigns around summer months and offer coupons/discounts on winter months to encourage more riders.
