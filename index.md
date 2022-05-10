# SF-Bay-Area-Bike-Sharing-Data

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


Outliers in Duration
We will take out the trips longer than two hours.
![Outliers_in_Duration](https://user-images.githubusercontent.com/75310566/167503746-ff9d0912-087b-4fbc-89ef-3003b1f8f204.png)


Distribution of Trip Duration:
![Density_Duration](https://user-images.githubusercontent.com/75310566/167503972-81455695-115c-47bc-9270-088070f785df.png)


### Add time features
  Since the bike usage is very related with the breakdowns of the time we will add them as seperate features.
  Here we need to be aware of the cyclic nature of our time data and the non-linearity dependence between the bike rentals and the hours of the day.
  Month(0-11) / Day (day of the week) / Hour(0-23) / Holiday (1 or 0)
 
Daily trips charts

![Daily_weekdays_trips_subscribers](https://user-images.githubusercontent.com/75310566/167508794-52dc3f05-f0cb-42e6-a16e-c88d490a8479.png)
Bike usage decrease on fridays compare to other weekdays.
  
Hourly trips charts

<img src="https://user-images.githubusercontent.com/75310566/167508872-1b3ea0bf-7dad-47b0-9895-5aec20ee6034.png" width="300"> <img src="https://user-images.githubusercontent.com/75310566/167563253-6f954003-0511-4631-9dcb-588526f53277.png" width="300">

Hourly_trips_across_the_days
![Hourly_trips_across_the_days](https://user-images.githubusercontent.com/75310566/167509287-fc72584f-47b2-47b1-beeb-7f2781462141.png)

## 3. Modeling approach

### Goal
Predict the hourly net change in the bike stock in each station.

### Steps
1)Targets dataset with 68 columns 
We start modeling by with the targets in which the arrivals and departures are separated.
The targets datasets will have the shape [n_hours, 2*n_stations] e.g, [,]
For each hour row, there will be columns with the count of departures from each station and the count of arrivals to each station. 



## 4. Recommandation

## References:
https://hrngok.github.io/posts/bay_area%20bike%20data/
https://github.com/pavelk2/Bay-Area-Bike-Share#stations-in-san-francisco

