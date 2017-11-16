
## Unit 6 | Assignment - What's the Weather Like?

## Background

Whether financial, political, or social -- data's true power lies in its ability to answer questions definitively. So let's take what you've learned about Python requests, APIs, and JSON traversals to answer a fundamental question: "What's the weather like as we approach the equator?"

Now, we know what you may be thinking: _"Duh. It gets hotter..."_

But, if pressed, how would you **prove** it?

![Equator](equatorsign.png)

## WeatherPy

In this example, you'll be creating a Python script to visualize the weather of 500+ cities across the world of varying distance from the equator. To accomplish this, you'll be utilizing a [simple Python library](https://pypi.python.org/pypi/citipy), the [OpenWeatherMap API](https://openweathermap.org/api), and a little common sense to create a representative model of weather across world cities.

Your objective is to build a series of scatter plots to showcase the following relationships:

* Temperature (F) vs. Latitude
* Humidity (%) vs. Latitude
* Cloudiness (%) vs. Latitude
* Wind Speed (mph) vs. Latitude

Your final notebook must:

* Randomly select **at least** 500 unique (non-repeat) cities based on latitude and longitude.
* Perform a weather check on each of the cities using a series of successive API calls.
* Include a print log of each city as it's being processed with the city number, city name, and requested URL.
* Save both a CSV of all data retrieved and png images for each scatter plot.

As final considerations:

* You must use Matplotlib to create your plots.
* You must include a written description of three observable trends based on the data.
* You must use proper labeling of your plots, including aspects like: Plot Titles (with date of analysis) and Axes Labels.
* You must include an exported markdown version of your Notebook called  `README.md` in your GitHub repository.
* See [Example Solution](WeatherPy_Example.pdf) for a reference on expected format.

## Hints and Considerations

* You may want to start this assignment by refreshing yourself on the [geographic coordinate system](http://desktop.arcgis.com/en/arcmap/10.3/guide-books/map-projections/about-geographic-coordinate-systems.htm).

* Next, spend the requisite time necessary to study the OpenWeatherMap API. Based on your initial study, you should be able to answer  basic questions about the API: Where do you request the API key? Which Weather API in particular will you need? What URL endpoints does it expect? What JSON structure does it respond with? Before you write a line of code, you should be aiming to have a crystal clear understanding of your intended outcome.

* Though we've never worked with the [citipy Python library](https://pypi.python.org/pypi/citipy), push yourself to decipher how it works, and why it might be relevant. Before you try to incorporate the library into your analysis, start by creating simple test cases outside your main script to confirm that you are using it correctly. Too often, when introduced to a new library, students get bogged down by the most minor of errors -- spending hours investigating their entire code -- when, in fact, a simple and focused test would have shown their basic utilization of the library was wrong from the start. Don't let this be you!

* Part of our expectation in this challenge is that you will use critical thinking skills to understand how and why we're recommending the tools we are. What is Citipy for? Why would you use it in conjunction with the OpenWeatherMap API? How would you do so?

* In building your script, pay attention to the cities you are using in your query pool. Are you getting coverage of the full gamut of latitudes and longitudes? Or are you simply choosing 500 cities concentrated in one region of the world? Even if you were a geographic genius, simply rattling 500 cities based on your human selection would create a biased dataset. Be thinking of how you should counter this. (Hint: Consider the full range of latitudes).

* Lastly, remember -- this is a challenging activity. Push yourself! If you complete this task, then you can safely say that you've gained a strong mastery of the core foundations of data analytics and it will only go better from here. Good luck!

## Copyright

Coding Boot Camp (C) 2017. All Rights Reserved.



```python
# Dependencies
from random import *
import urllib
import json
import requests
import pandas as pd
import matplotlib.pyplot as plt
import openweathermapy.core as ow
import pandas as pd
from config import *
from citipy import citipy
```


```python
#select random latitude (from -90 to +90)

city_list = pd.DataFrame()

for i in range(600):
    lat=(random()-.5)*randint(-180,180)
    lon=(random()-.5)*randint(-360,360)
    coords=pd.DataFrame([[lat,lon]], columns=['lat','lon'])
    city_list=city_list.append(coords)
    
    
city_list=city_list.reset_index()
city_list=city_list[['lat','lon']]
```


```python
%%capture

#Use Citipy to find the nearest city to each pair of latlongs

for row in range(0,len(city_list)):
    #print(city_list.loc[row]['lat'])
    lat = city_list.loc[row]['lat']
    lon = city_list.loc[row]['lon']    
    city = citipy.nearest_city(lat,lon)
    city_list.set_value(row, "city", city.city_name.title())
    city_list.set_value(row, "country", city.country_code.upper())
```


```python
#Make sure the dataframe looks reasonable
city_list.head(5)
city_list.tail(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lat</th>
      <th>lon</th>
      <th>actual_lat</th>
      <th>actual_lon</th>
      <th>city</th>
      <th>country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-7.403356</td>
      <td>-6.627921</td>
      <td>43.5024</td>
      <td>-4.91692</td>
      <td>Georgetown</td>
      <td>SH</td>
    </tr>
    <tr>
      <th>1</th>
      <td>32.935846</td>
      <td>-44.954723</td>
      <td>43.5024</td>
      <td>-4.91692</td>
      <td>Torbay</td>
      <td>CA</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.381295</td>
      <td>94.969960</td>
      <td>43.5024</td>
      <td>-4.91692</td>
      <td>Meulaboh</td>
      <td>ID</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-57.648269</td>
      <td>92.845761</td>
      <td>43.5024</td>
      <td>-4.91692</td>
      <td>Busselton</td>
      <td>AU</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3.779435</td>
      <td>9.500697</td>
      <td>43.5024</td>
      <td>-4.91692</td>
      <td>Tiko</td>
      <td>CM</td>
    </tr>
  </tbody>
</table>
</div>






<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lat</th>
      <th>lon</th>
      <th>actual_lat</th>
      <th>actual_lon</th>
      <th>city</th>
      <th>country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>595</th>
      <td>-14.434289</td>
      <td>25.355745</td>
      <td>43.5024</td>
      <td>-4.91692</td>
      <td>Kaoma</td>
      <td>ZM</td>
    </tr>
    <tr>
      <th>596</th>
      <td>52.246393</td>
      <td>-15.174460</td>
      <td>43.5024</td>
      <td>-4.91692</td>
      <td>Dingle</td>
      <td>IE</td>
    </tr>
    <tr>
      <th>597</th>
      <td>56.180804</td>
      <td>27.062354</td>
      <td>43.5024</td>
      <td>-4.91692</td>
      <td>Kraslava</td>
      <td>LV</td>
    </tr>
    <tr>
      <th>598</th>
      <td>62.814909</td>
      <td>12.771654</td>
      <td>43.5024</td>
      <td>-4.91692</td>
      <td>Roros</td>
      <td>NO</td>
    </tr>
    <tr>
      <th>599</th>
      <td>43.502384</td>
      <td>-106.638396</td>
      <td>43.5024</td>
      <td>-4.91692</td>
      <td>Casper</td>
      <td>US</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%capture

#add actual lat-longs to the dataframe - these are what we will use to get weather data

base_url = "https://maps.googleapis.com/maps/api/geocode/json"

city_list['actual_lat']=''
city_list['actual_lon']=''

city_list

for row in range(0,len(city_list)):
    
    try:    
        print(row)
        city=city_list.loc[row]['city']
        country=city_list.loc[row]['country']
        address=str(city+'+'+country)

        params = {
                "address": address,
                "key": gkey
            }

        response = requests.get(base_url, params=params)
        print(response.url)

        geo_data = response.json()
        lat = geo_data["results"][0]["geometry"]["location"]["lat"]
        lng = geo_data["results"][0]["geometry"]["location"]["lng"]

    except IndexError:
        print("No data available - skipping...")

    city_list.set_value(row, "actual_lat", lat)
    city_list.set_value(row, "actual_lon", lng)
    
```


```python
#check the dataframe again
city_list.head(5)
city_list.tail(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lat</th>
      <th>lon</th>
      <th>actual_lat</th>
      <th>actual_lon</th>
      <th>city</th>
      <th>country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-7.403356</td>
      <td>-6.627921</td>
      <td>-7.92622</td>
      <td>-14.4112</td>
      <td>Georgetown</td>
      <td>SH</td>
    </tr>
    <tr>
      <th>1</th>
      <td>32.935846</td>
      <td>-44.954723</td>
      <td>47.6581</td>
      <td>-52.7355</td>
      <td>Torbay</td>
      <td>CA</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.381295</td>
      <td>94.969960</td>
      <td>4.1437</td>
      <td>96.1281</td>
      <td>Meulaboh</td>
      <td>ID</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-57.648269</td>
      <td>92.845761</td>
      <td>-33.6555</td>
      <td>115.35</td>
      <td>Busselton</td>
      <td>AU</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3.779435</td>
      <td>9.500697</td>
      <td>4.07863</td>
      <td>9.35898</td>
      <td>Tiko</td>
      <td>CM</td>
    </tr>
  </tbody>
</table>
</div>






<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lat</th>
      <th>lon</th>
      <th>actual_lat</th>
      <th>actual_lon</th>
      <th>city</th>
      <th>country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>595</th>
      <td>-14.434289</td>
      <td>25.355745</td>
      <td>-14.8119</td>
      <td>24.7974</td>
      <td>Kaoma</td>
      <td>ZM</td>
    </tr>
    <tr>
      <th>596</th>
      <td>52.246393</td>
      <td>-15.174460</td>
      <td>52.1409</td>
      <td>-10.264</td>
      <td>Dingle</td>
      <td>IE</td>
    </tr>
    <tr>
      <th>597</th>
      <td>56.180804</td>
      <td>27.062354</td>
      <td>55.8948</td>
      <td>27.1628</td>
      <td>Kraslava</td>
      <td>LV</td>
    </tr>
    <tr>
      <th>598</th>
      <td>62.814909</td>
      <td>12.771654</td>
      <td>62.5748</td>
      <td>11.3841</td>
      <td>Roros</td>
      <td>NO</td>
    </tr>
    <tr>
      <th>599</th>
      <td>43.502384</td>
      <td>-106.638396</td>
      <td>42.8666</td>
      <td>-106.313</td>
      <td>Casper</td>
      <td>US</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%capture

#NEXT STEP: write all this weather data to a dataframe 

#print weather summary for all cities in the list

base_url = 'http://api.openweathermap.org/data/2.5/weather?'

city_list['temp']=''
city_list['clouds']=''
city_list['humidity']=''
city_list['wind_speed']=''

#for cities that have no data, add the row index to a list. Then we will drop all of them afterwards.
rows_to_drop = []

for row in range(0, len(city_list)):
    
    try:
        lat = city_list.loc[row]['actual_lat']
        lon = city_list.loc[row]['actual_lon']
        query_url = base_url + 'units=imperial&lat='+str(lat) + '&lon='+ str(lon) +'&appid=' + weather_key
        response = requests.get(query_url)
        result=response.json()

        city=city_list.loc[row]['city']
        temp=float(result['main']['temp'])
        clouds=result['clouds']['all']
        humidity=result['main']['humidity']
        wind_speed=result['wind']['speed']

        #I HAVE THE OUTPUT SUPPRESSED FOR THIS CELL because I have a config setting that makes all the set_value things produce output, which is crappy
        #but here is how one WOULD print a log:

        print(f"Processing data for City # {row}: {city}")
        print(response.url)    

        #add values to the dataframe
        city_list.set_value(row, "temp", temp)
        city_list.set_value(row, "clouds", clouds)    
        city_list.set_value(row, "humidity", humidity)
        city_list.set_value(row, "wind_speed", wind_speed) 
    except KeyError:
        #print(f"Processing data for City # {row}: {city}")
        #print(response.url)    
        #add values to the dataframe
        rows_to_drop.append(row)
        #print("No data available. Skipping...")


```

#drop these rows from the dataframe so that you can perform operations on the weather fields

city_list.drop(city_list.index[rows_to_drop])


```python
city_list.head()
city_list.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lat</th>
      <th>lon</th>
      <th>actual_lat</th>
      <th>actual_lon</th>
      <th>city</th>
      <th>country</th>
      <th>temp</th>
      <th>clouds</th>
      <th>humidity</th>
      <th>wind_speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-7.403356</td>
      <td>-6.627921</td>
      <td>-7.92622</td>
      <td>-14.4112</td>
      <td>Georgetown</td>
      <td>SH</td>
      <td>75.04</td>
      <td>68</td>
      <td>100</td>
      <td>17.81</td>
    </tr>
    <tr>
      <th>1</th>
      <td>32.935846</td>
      <td>-44.954723</td>
      <td>47.6581</td>
      <td>-52.7355</td>
      <td>Torbay</td>
      <td>CA</td>
      <td>28.4</td>
      <td>20</td>
      <td>79</td>
      <td>8.05</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.381295</td>
      <td>94.969960</td>
      <td>4.1437</td>
      <td>96.1281</td>
      <td>Meulaboh</td>
      <td>ID</td>
      <td>83.1</td>
      <td>8</td>
      <td>100</td>
      <td>2.04</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-57.648269</td>
      <td>92.845761</td>
      <td>-33.6555</td>
      <td>115.35</td>
      <td>Busselton</td>
      <td>AU</td>
      <td>65.77</td>
      <td>0</td>
      <td>99</td>
      <td>15.9</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3.779435</td>
      <td>9.500697</td>
      <td>4.07863</td>
      <td>9.35898</td>
      <td>Tiko</td>
      <td>CM</td>
      <td>77</td>
      <td>75</td>
      <td>100</td>
      <td>2.24</td>
    </tr>
  </tbody>
</table>
</div>






<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lat</th>
      <th>lon</th>
      <th>actual_lat</th>
      <th>actual_lon</th>
      <th>city</th>
      <th>country</th>
      <th>temp</th>
      <th>clouds</th>
      <th>humidity</th>
      <th>wind_speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>595</th>
      <td>-14.434289</td>
      <td>25.355745</td>
      <td>-14.8119</td>
      <td>24.7974</td>
      <td>Kaoma</td>
      <td>ZM</td>
      <td>65.05</td>
      <td>76</td>
      <td>97</td>
      <td>6.02</td>
    </tr>
    <tr>
      <th>596</th>
      <td>52.246393</td>
      <td>-15.174460</td>
      <td>52.1409</td>
      <td>-10.264</td>
      <td>Dingle</td>
      <td>IE</td>
      <td>53.49</td>
      <td>92</td>
      <td>97</td>
      <td>10.16</td>
    </tr>
    <tr>
      <th>597</th>
      <td>56.180804</td>
      <td>27.062354</td>
      <td>55.8948</td>
      <td>27.1628</td>
      <td>Kraslava</td>
      <td>LV</td>
      <td>31.44</td>
      <td>80</td>
      <td>99</td>
      <td>8.25</td>
    </tr>
    <tr>
      <th>598</th>
      <td>62.814909</td>
      <td>12.771654</td>
      <td>62.5748</td>
      <td>11.3841</td>
      <td>Roros</td>
      <td>NO</td>
      <td>12.2</td>
      <td>12</td>
      <td>100</td>
      <td>4.7</td>
    </tr>
    <tr>
      <th>599</th>
      <td>43.502384</td>
      <td>-106.638396</td>
      <td>42.8666</td>
      <td>-106.313</td>
      <td>Casper</td>
      <td>US</td>
      <td>46.4</td>
      <td>1</td>
      <td>28</td>
      <td>10.29</td>
    </tr>
  </tbody>
</table>
</div>




```python
#WRITE TO CSV
city_list.head()
city_list.to_csv('Output/weather_data_output.csv')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lat</th>
      <th>lon</th>
      <th>actual_lat</th>
      <th>actual_lon</th>
      <th>city</th>
      <th>country</th>
      <th>temp</th>
      <th>clouds</th>
      <th>humidity</th>
      <th>wind_speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-7.403356</td>
      <td>-6.627921</td>
      <td>-7.92622</td>
      <td>-14.4112</td>
      <td>Georgetown</td>
      <td>SH</td>
      <td>75.04</td>
      <td>68</td>
      <td>100</td>
      <td>17.81</td>
    </tr>
    <tr>
      <th>1</th>
      <td>32.935846</td>
      <td>-44.954723</td>
      <td>47.6581</td>
      <td>-52.7355</td>
      <td>Torbay</td>
      <td>CA</td>
      <td>28.4</td>
      <td>20</td>
      <td>79</td>
      <td>8.05</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.381295</td>
      <td>94.969960</td>
      <td>4.1437</td>
      <td>96.1281</td>
      <td>Meulaboh</td>
      <td>ID</td>
      <td>83.1</td>
      <td>8</td>
      <td>100</td>
      <td>2.04</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-57.648269</td>
      <td>92.845761</td>
      <td>-33.6555</td>
      <td>115.35</td>
      <td>Busselton</td>
      <td>AU</td>
      <td>65.77</td>
      <td>0</td>
      <td>99</td>
      <td>15.9</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3.779435</td>
      <td>9.500697</td>
      <td>4.07863</td>
      <td>9.35898</td>
      <td>Tiko</td>
      <td>CM</td>
      <td>77</td>
      <td>75</td>
      <td>100</td>
      <td>2.24</td>
    </tr>
  </tbody>
</table>
</div>




```python
%%capture
#Temperature (F) vs. Latitude
#x_lim = 2 * np.pi #set this later

#X-axis: Latitude
#Y-axis: Temperature
x_axis = city_list['actual_lat']
y_axis = city_list['temp']

x_min=x_axis.min()-10
x_max=x_axis.max()+10

y_min=y_axis.min()-10
y_max=y_axis.max()+10

plt.title("Temperature (F) vs. Latitude\nNovember 13, 2017\n\n")
plt.xlabel("Latitude\n")
plt.ylabel("\nTemp (degrees F)")

#plt.hlines(0,0, x_lim, alpha=0.2) #Idk what this is lol

plt.scatter(x_axis, y_axis, marker="o", color="red")

#SAVE TO PNG
plt.savefig('Output/Fig1_Temp_Latitude.png')
```


```python
plt.show()
```


![png](output_12_0.png)



```python
%%capture
##Humidity (%) vs. Latitude

#X-axis: Latitude
#Y-axis: Humidity (%)
x_axis = city_list['actual_lat']
y_axis = city_list['humidity']

x_min=x_axis.min()-10
x_max=x_axis.max()+10

y_min=y_axis.min()-10
y_max=y_axis.max()+10

plt.xlim(x_min,x_max)
plt.ylim(y_min,y_max)

plt.title("Humidity (%) vs. Latitude\nNovember 13, 2017\n\n")
plt.xlabel("\nLatitude")
plt.ylabel("Humidity (%)\n")

plt.scatter(x_axis, y_axis, marker="o", color="red")

#SAVE TO PNG
plt.savefig('Output/Fig2_Humidity_Latitude.png')
```


```python
plt.show()
```


![png](output_14_0.png)



```python
%%capture
#Cloudiness (%) vs. Latitude

#X-axis: Latitude
#Y-axis: Cloudiness (%)
x_axis = city_list['actual_lat']
y_axis = city_list['clouds']

x_min=x_axis.min()-10
x_max=x_axis.max()+10

y_min=y_axis.min()-10
y_max=y_axis.max()+10

plt.xlim(x_min,x_max)
plt.ylim(y_min,y_max)

plt.title("Cloudiness (%) vs. Latitude\nNovember 13, 2017\n\n")
plt.xlabel("\nLatitude")
plt.ylabel("Cloudiness (%)\n")

#plt.hlines(0,0, x_lim, alpha=0.2) #Idk what this is lol

plt.scatter(x_axis, y_axis, marker="o", color="red")

#SAVE TO PNG
plt.savefig('Output/Fig3_Cloudiness_Latitude.png')
```


```python
plt.show()
```


![png](output_16_0.png)



```python
%%capture
#Wind Speed (mph) vs. Latitude

#X-axis: Latitude
#Y-axis: Wind Speed (mph)
x_axis = city_list['actual_lat']
y_axis = city_list['wind_speed']

x_min=x_axis.min()-10
x_max=x_axis.max()+10

y_min=y_axis.min()-10
y_max=y_axis.max()+10

plt.xlim(x_min,x_max)
plt.ylim(y_min,y_max)

plt.title("Wind Speed (mph) vs. Latitude\nNovember 13, 2017\n\n")
plt.xlabel("\nLatitude")
plt.ylabel("Wind Speed (mph)\n")

#plt.hlines(0,0, x_lim, alpha=0.2) #Idk what this is lol

plt.scatter(x_axis, y_axis, marker="o", color="red")

#SAVE TO PNG
plt.savefig('Output/Fig4_WindSpeed_Latitude.png')
```


```python
plt.show()
```


![png](output_18_0.png)



```python
#TRENDS ON TRENDS

#Observation 1:

#Of the weather variables observed, the one most closely associated with latitude was Temperature, 
#which reaches its maximum close to the equator and generally tends to decrease as the latitude 
#gets farther away from 0 degrees.

#Observation 2:

#Based on the scatter plot, it appears to be the case that latitude is not a strong predictor of cloudiness. However,
#there is also a wide variation of humidities across the globe. This indicates that whatever climatic factors
#drive cloudiness are not driven by either latitude (which shows no relationship) or temperature (which is closely
#related to latitude).

#Observation 3:

#Wind speed shows a curvilinear relationship with latitude in a fashion similar to temperature, though
#with a weaker correlation. This relationship is the opposite of temperature - the farther one gets away from the
#equator, the higher the wind speeds that tend to occur. Depending on whether the temperature figures
#are affected by wind speed, this may indicate part of the causal mechanism by which distance from the equator
#drives colder temperatures. (I'm not sure whether temperature in these data take wind speed into account or not, though.)
```


```python
#print weather summary for all cities in the list (I wanted to save this code but it's not part of the assignment)

base_url = 'http://api.openweathermap.org/data/2.5/weather?'


for row in range(0, len(city_list)):
    
    try:
        lat = city_list.loc[row]['actual_lat']
        lon = city_list.loc[row]['actual_lon']
        query_url = base_url + 'units=imperial&lat='+str(lat) + '&lon='+ str(lon) +'&appid=' + weather_key
        response = requests.get(query_url)
        url=response.url
        #print(response.url)
        response=response.json()

        city=city_list.loc[row]['city']
        temp=response['main']['temp']
        clouds=response['clouds']['all']
        humidity=response['main']['humidity']
        wind_speed=response['wind']['speed']

        print(f"\n\nWeather summary for {city}")
        print(url)
        print("----------------------------")
        print(f"Temp: {temp}")
        print(f"Clouds: {clouds}")
        print(f"Humidity: {humidity}")
        print(f"Wind Speed: {wind_speed}")
    except KeyError:
        print("No data. Skipping...")

```

    
    
    Weather summary for Georgetown
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-7.926222999999999&lon=-14.4112011&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 75.04
    Clouds: 68
    Humidity: 100
    Wind Speed: 17.81
    
    
    Weather summary for Torbay
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=47.6580777&lon=-52.7355238&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 28.4
    Clouds: 20
    Humidity: 79
    Wind Speed: 8.05
    
    
    Weather summary for Meulaboh
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=4.1436951&lon=96.12811459999999&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 83.1
    Clouds: 8
    Humidity: 100
    Wind Speed: 2.04
    
    
    Weather summary for Busselton
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-33.6554927&lon=115.3500188&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 65.77
    Clouds: 0
    Humidity: 99
    Wind Speed: 15.9
    
    
    Weather summary for Tiko
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=4.078630599999999&lon=9.358984&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 77
    Clouds: 75
    Humidity: 100
    Wind Speed: 2.24
    
    
    Weather summary for Mao
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=14.1310247&lon=15.3189726&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 62.85
    Clouds: 8
    Humidity: 27
    Wind Speed: 7.52
    
    
    Weather summary for Kruisfontein
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-33.9843356&lon=24.732102&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 62.89
    Clouds: 92
    Humidity: 95
    Wind Speed: 7.92
    
    
    Weather summary for Ilebo
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-4.3253802&lon=20.5870955&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 73.42
    Clouds: 88
    Humidity: 94
    Wind Speed: 2.55
    
    
    Weather summary for Hithadhoo
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-0.6060574&lon=73.0892245&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 82.65
    Clouds: 88
    Humidity: 100
    Wind Speed: 11.27
    
    
    Weather summary for Leh
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=34.1525864&lon=77.57705349999999&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 1.33
    Clouds: 64
    Humidity: 84
    Wind Speed: 0.58
    
    
    Weather summary for Takoradi
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=4.9015794&lon=-1.7830973&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 79.9
    Clouds: 76
    Humidity: 100
    Wind Speed: 6.91
    
    
    Weather summary for Almeirim
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-1.5239568&lon=-52.5785846&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 82.47
    Clouds: 0
    Humidity: 64
    Wind Speed: 6.06
    
    
    Weather summary for Mogadishu
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=2.0469343&lon=45.3181623&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 80.4
    Clouds: 48
    Humidity: 100
    Wind Speed: 8.25
    
    
    Weather summary for Libreville
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=0.4161976&lon=9.4672676&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 77
    Clouds: 90
    Humidity: 94
    Wind Speed: 5.82
    
    
    Weather summary for Leiyang
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=26.422277&lon=112.859759&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 58.35
    Clouds: 100
    Humidity: 100
    Wind Speed: 5.79
    
    
    Weather summary for Georgetown
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-7.926222999999999&lon=-14.4112011&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 75.04
    Clouds: 68
    Humidity: 100
    Wind Speed: 17.81
    
    
    Weather summary for Touros
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-5.1961669&lon=-35.4635381&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 77.97
    Clouds: 80
    Humidity: 90
    Wind Speed: 8.59
    
    
    Weather summary for Georgetown
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-7.926222999999999&lon=-14.4112011&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 75.04
    Clouds: 68
    Humidity: 100
    Wind Speed: 17.81
    
    
    Weather summary for Colares
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=38.8066307&lon=-9.4426867&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 53.6
    Clouds: 0
    Humidity: 54
    Wind Speed: 9.17
    
    
    Weather summary for Ilhabela
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-23.8158392&lon=-45.3664809&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 70.05
    Clouds: 0
    Humidity: 100
    Wind Speed: 1.88
    
    
    Weather summary for Saint-Pierre
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=46.7758459&lon=-56.1806363&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 43.09
    Clouds: 64
    Humidity: 100
    Wind Speed: 11.65
    
    
    Weather summary for Owando
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-0.4829632&lon=15.8936224&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 70.99
    Clouds: 44
    Humidity: 94
    Wind Speed: 4.5
    
    
    Weather summary for Georgetown
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-7.926222999999999&lon=-14.4112011&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 75.04
    Clouds: 68
    Humidity: 100
    Wind Speed: 17.81
    
    
    Weather summary for Hermanus
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-34.4092004&lon=19.2504436&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 54.88
    Clouds: 92
    Humidity: 99
    Wind Speed: 3.33
    
    
    Weather summary for Kondoa
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-4.9068418&lon=35.7898106&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 57.4
    Clouds: 76
    Humidity: 90
    Wind Speed: 3
    
    
    Weather summary for Akonolinga
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=3.7827632&lon=12.2490697&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 68.7
    Clouds: 92
    Humidity: 99
    Wind Speed: 2.37
    
    
    Weather summary for Jamestown
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-15.9286343&lon=-5.7151749&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 67.21
    Clouds: 80
    Humidity: 100
    Wind Speed: 19.04
    
    
    Weather summary for Manavalakurichi
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=8.1471445&lon=77.30228679999999&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 78.8
    Clouds: 20
    Humidity: 83
    Wind Speed: 6.93
    
    
    Weather summary for Buta
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=2.8045317&lon=24.7499112&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 71.62
    Clouds: 32
    Humidity: 99
    Wind Speed: 2.1
    
    
    Weather summary for Port Elizabeth
    http://api.openweathermap.org/data/2.5/weather?units=imperial&lat=-33.7139247&lon=25.5207358&appid=25bc90a1196e6f153eece0bc0b0fc9eb
    ----------------------------
    Temp: 64.4
    Clouds: 75
    Humidity: 88
    Wind Speed: 6.93



    ---------------------------------------------------------------------------

    KeyboardInterrupt                         Traceback (most recent call last)

    <ipython-input-71-1b9c3d5b61ff> in <module>()
         10         lon = city_list.loc[row]['actual_lon']
         11         query_url = base_url + 'units=imperial&lat='+str(lat) + '&lon='+ str(lon) +'&appid=' + weather_key
    ---> 12         response = requests.get(query_url)
         13         url=response.url
         14         #print(response.url)


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/requests/api.py in get(url, params, **kwargs)
         70 
         71     kwargs.setdefault('allow_redirects', True)
    ---> 72     return request('get', url, params=params, **kwargs)
         73 
         74 


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/requests/api.py in request(method, url, **kwargs)
         56     # cases, and look like a memory leak in others.
         57     with sessions.Session() as session:
    ---> 58         return session.request(method=method, url=url, **kwargs)
         59 
         60 


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/requests/sessions.py in request(self, method, url, params, data, headers, cookies, files, auth, timeout, allow_redirects, proxies, hooks, stream, verify, cert, json)
        506         }
        507         send_kwargs.update(settings)
    --> 508         resp = self.send(prep, **send_kwargs)
        509 
        510         return resp


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/requests/sessions.py in send(self, request, **kwargs)
        616 
        617         # Send the request
    --> 618         r = adapter.send(request, **kwargs)
        619 
        620         # Total elapsed time of the request (approximately)


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/requests/adapters.py in send(self, request, stream, timeout, verify, cert, proxies)
        438                     decode_content=False,
        439                     retries=self.max_retries,
    --> 440                     timeout=timeout
        441                 )
        442 


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/urllib3/connectionpool.py in urlopen(self, method, url, body, headers, retries, redirect, assert_same_host, timeout, pool_timeout, release_conn, chunked, body_pos, **response_kw)
        599                                                   timeout=timeout_obj,
        600                                                   body=body, headers=headers,
    --> 601                                                   chunked=chunked)
        602 
        603             # If we're going to release the connection in ``finally:``, then


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/urllib3/connectionpool.py in _make_request(self, conn, method, url, timeout, chunked, **httplib_request_kw)
        355             conn.request_chunked(method, url, **httplib_request_kw)
        356         else:
    --> 357             conn.request(method, url, **httplib_request_kw)
        358 
        359         # Reset the timeout for the recv() on the socket


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1237                 encode_chunked=False):
       1238         """Send a complete request to the server."""
    -> 1239         self._send_request(method, url, body, headers, encode_chunked)
       1240 
       1241     def _send_request(self, method, url, body, headers, encode_chunked):


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1283             # default charset of iso-8859-1.
       1284             body = _encode(body, 'body')
    -> 1285         self.endheaders(body, encode_chunked=encode_chunked)
       1286 
       1287     def getresponse(self):


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/http/client.py in endheaders(self, message_body, encode_chunked)
       1232         else:
       1233             raise CannotSendHeader()
    -> 1234         self._send_output(message_body, encode_chunked=encode_chunked)
       1235 
       1236     def request(self, method, url, body=None, headers={}, *,


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/http/client.py in _send_output(self, message_body, encode_chunked)
       1024         msg = b"\r\n".join(self._buffer)
       1025         del self._buffer[:]
    -> 1026         self.send(msg)
       1027 
       1028         if message_body is not None:


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/http/client.py in send(self, data)
        962         if self.sock is None:
        963             if self.auto_open:
    --> 964                 self.connect()
        965             else:
        966                 raise NotConnected()


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/urllib3/connection.py in connect(self)
        164 
        165     def connect(self):
    --> 166         conn = self._new_conn()
        167         self._prepare_conn(conn)
        168 


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/urllib3/connection.py in _new_conn(self)
        139         try:
        140             conn = connection.create_connection(
    --> 141                 (self.host, self.port), self.timeout, **extra_kw)
        142 
        143         except SocketTimeout as e:


    /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/urllib3/util/connection.py in create_connection(address, timeout, source_address, socket_options)
         71             if source_address:
         72                 sock.bind(source_address)
    ---> 73             sock.connect(sa)
         74             return sock
         75 


    KeyboardInterrupt: 

