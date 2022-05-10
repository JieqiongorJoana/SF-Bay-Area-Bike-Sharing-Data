# SF-Bay-Area-Bike-Sharing-Data
### By Chenlu Zhu, Jieqiong Yang
![CP255 Final Project presentation  pptx](https://user-images.githubusercontent.com/75310566/167366826-f1b6ded2-64aa-44ab-8418-66b390101fd5.jpg)

## 1. Project Description
Bike sharing programs usually have a problem of unbalanced stations where the number of trips from these stations is higher than the number of trips to these stations (or vice versa). By anaylyzing the bike trips, based on the influencial factors such as trip time, weather, location of the stations, we will to find out the net change of each bike station. Then, we will figure out the unbalanced stations and recommand how to better allocate bicycles in different bike stations.

### Research Question
What is the net change in the bike stock (bikes returned - bikes taken) at a specific station at a specific hour in San Francisco?

### Variables:
Bike Stations: Location, number;

Trip: trip time, duration of trip;

Weather: hourly main weather feature, categorical characters of weather, eg: Humidity, Temperature, Wind; 

### Datasets:
station_data_sf, trip_data_sf, weather data from kaggle

### Methodology
Interactive Plotting in Python(Plotly, folium);APIs

## 2. Data exploration
The first step is to explore the data in a spreadsheet program and load data into Jupyter notebooks, and then explore each column and rows, join it with other data sources, generate charts and interactive maps to learn about the data and basic information. 

### Get Bike Station Data:
Station_data includes location coordinates of bike stations and their id number. The total number of bike stations that will be the targets in the project is 34. 
![station_df](https://user-images.githubusercontent.com/75310566/167487910-bf69cd70-b3e2-4b35-b933-26f3603ba586.png)


### Get Initial Trip Data(Sample):
![trip_df_10](https://user-images.githubusercontent.com/75310566/167490631-35732ca3-2db7-49b3-8650-a3488ccf9d0d.png)

### Weather Data:
We made our analysis with samples that are in an hour range. So, we used the weather data taken from Kaggle Datasets (Historical Hourly Weather Data 2012-2017). San Francisco's hourly weather measurements data has various weather attributes, such as temperature, humidity, air pressure, etc. are provided.

Since datasets are given by a common datetime column, we read all the datasets to create a weather dataset related to our are of interest.
![weather](https://user-images.githubusercontent.com/75310566/167543483-8e3d9691-e776-4608-86f1-8af529a1e008.png)

Weather Description column is categorical. We need to convert the categories into dummy variables. 
![weather2](https://user-images.githubusercontent.com/75310566/167543490-8004ba4f-5492-42cc-bef7-6e756bc421ea.png)


### Generate Bike Station Location Map
We use folium tools to create an interactive map in order to see the location of the bike station clearly. The marker clusters group points that overlap and then it labels the resulting circle with the number of points in that area. If you click on the circle, the map zooms to the area to show you the individual points. 
<iframe src="station_location.html" height="500" width="800"></iframe>

### Stations departures count
Count the departures from each stations
![Stations departures count](https://user-images.githubusercontent.com/75310566/167501603-ffce12c0-d10d-449e-ae60-f5c329f081ea.png)
In the stations departures count figure, we see that some stations are very popular with many rents, while some have only few rents. Because of that in general bicycles at popular stations tend to be used significantly more often than bicycles at not popular stations.
The map below shows the station that has the highest number of departures:
<iframe src="highest_stations_map.html" height="500" width="800"></iframe>

### Add time features
### Trip Duration
Firstly, we calculate the difference between start date time and end data time to obtain the duration of each trip and add a "duration" column into the trip dataframe. We use violin Box Plot, a figure and two axes for boxplot and distplot to show a process of analysis of the distribution of duration. The Duration aixes use seconds as units and from the plot, we found that most of the trips are less than half hour. Potential reason is that users can make an unlimited number of trips, with trips under thirty minutes in length having no additional charge; longer trips will incur overtime fees.

Outliers in Duration
We will take out the trips longer than two hours.
![Outliers_in_Duration](https://user-images.githubusercontent.com/75310566/167503746-ff9d0912-087b-4fbc-89ef-3003b1f8f204.png)


Distribution of Trip Duration:
![Density_Duration](https://user-images.githubusercontent.com/75310566/167503972-81455695-115c-47bc-9270-088070f785df.png)


### Trip Time
  Since the bike usage is very related with the breakdowns of the time we will add them as seperate features.
  Here we need to be aware of the cyclic nature of our time data and the non-linearity dependence between the bike rentals and the hours of the day.
  Month(0-11) / Day (day of the week) / Hour(0-23) / Holiday (1 or 0)
 
Daily trips charts

![Daily_weekdays_trips_subscribers](https://user-images.githubusercontent.com/75310566/167508794-52dc3f05-f0cb-42e6-a16e-c88d490a8479.png)

--Bike usage decrease on Fridays compared to other weekdays.
  
Hourly trips charts

<img src="https://user-images.githubusercontent.com/75310566/167508872-1b3ea0bf-7dad-47b0-9895-5aec20ee6034.png" width="400"> <img src="https://user-images.githubusercontent.com/75310566/167563253-6f954003-0511-4631-9dcb-588526f53277.png" width="410">

- Firstly, bike usage between 0h-4h is close to zero.
- Subscriber usage is increasing after 5h and making first peak around 8h which is high probably due to commuting to work or schools.
- The second peak is around 17h which is high probably due to commuting back from work or schools
- It is intiutive that people who have a regular schedule prefer to subscribe to the system
- However we will not use the subscriber type information in our model because when we want to predict future data of bike stock net change in the next hour at that moment we will not have the subscriber type data for the coming hour(s).

Hourly_trips_across_the_days
![Hourly_trips_across_the_days](https://user-images.githubusercontent.com/75310566/167509287-fc72584f-47b2-47b1-beeb-7f2781462141.png)
We can see the difference in the bike usage hours patterns of the weekdays and weekends

## 3. Modeling approach

### Goal
Predict the hourly net change in the bike stock in each station.

### Targets dataset with 68 columns 
We start modeling by with the targets in which the arrivals and departures are separated.
The targets datasets will have the shape [n_hours, 2*n_stations] e.g, [,]
For each hour row, there will be columns with the count of departures from each station and the count of arrivals to each station. 

### Arrivals and departures hourly count
  -Create departure columns names plus “d” stand for departures 
  -Create arrival columns’ names plus”a” stand for arrivals.
  -Then create arrival and departure dictionary 
  -Add arrival list and departure list to station dataframe ( station matrix) 
  -Combine trip data and station matrix into trips_extends 
  -Assign 1 to the arrival column of the corresponding the start station on the same the row and same procedure is applied for the departure column also 
  Now we have trip_extended dataframe whose rows contain every single trip information with a departure column and an arrival column encoded with
   1. Because every single trip starts in a station and ends in a station
![Modeling_trips_extended](https://user-images.githubusercontent.com/75310566/167579838-bbb0942f-5577-4b0a-a6b5-3050fca84bcb.png)

Resample trip_extended dataframe hourly and count the arrivals and departures (minimize the row number and keep every hour into index) 
<img src="https://user-images.githubusercontent.com/75310566/167580132-ca85b490-faa2-40e0-b6dd-53a8dbe60d02.png" width="400">
Create stations_hourly df by just taking the station column 

![Modeling_trips_extended2](https://user-images.githubusercontent.com/75310566/167580378-275d178a-f085-4138-9de1-1350ebd0cbf7.png)

This table clearly show, in every hour, how many bike arrival and departure happened in each station.

### Data split with timeseriesSplit 
We took last 10% of the sorted dataset as a hold-out set and use sklean TimeseriesSplit object for cross-validation 
Set train as 90% dataset and test as 10% dataset. 

## Model with 68 stations 
### Setting the first baseline for the model 
In this model a target corresponding to a features sample will be a point with the permutations of 68 stations. 
Before we make and evaluate the predictions, we need to establish a baseline, which is the measure we want our model to be compared to. If our model does not improve on the baseline, it will fail. The baseline prediction for our case could be hourly arrivals and departures. In other words, our baseline is the error we would get if we just predicted the average hourly arrivals and departures. 



## 4. Recommandation

## References:
https://hrngok.github.io/posts/bay_area%20bike%20data/
https://github.com/pavelk2/Bay-Area-Bike-Share#stations-in-san-francisco
https://www.kaggle.com/datasets/benhamner/sf-bay-area-bike-share
https://scikit-learn.org/stable/modules/generated/sklearn.multioutput.MultiOutputRegressor.html
