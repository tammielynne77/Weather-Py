

```python
# I am having trouble creating an exception for the city not found error that is happening.  A ran the code on a smaller
#number of cities because the error tends not to appear but the higher numbers tend to produce the error.
```


```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import openweathermapy as owm
from citipy import citipy
from config import weather_key
import numpy as np
import json
import requests
from pandas.io.json import json_normalize
```


```python
with open('city.list.json') as data_file:    
    city_list = json.load(data_file)
```


```python
cities_normal = json_normalize(city_list)
cities_normal.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>coord.lat</th>
      <th>coord.lon</th>
      <th>country</th>
      <th>id</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>44.549999</td>
      <td>34.283333</td>
      <td>UA</td>
      <td>707860</td>
      <td>Hurzuf</td>
    </tr>
    <tr>
      <th>1</th>
      <td>55.683334</td>
      <td>37.666668</td>
      <td>RU</td>
      <td>519188</td>
      <td>Novinki</td>
    </tr>
    <tr>
      <th>2</th>
      <td>28.000000</td>
      <td>84.633331</td>
      <td>NP</td>
      <td>1283378</td>
      <td>Gorkhā</td>
    </tr>
    <tr>
      <th>3</th>
      <td>29.000000</td>
      <td>76.000000</td>
      <td>IN</td>
      <td>1270260</td>
      <td>State of Haryāna</td>
    </tr>
    <tr>
      <th>4</th>
      <td>44.599998</td>
      <td>33.900002</td>
      <td>UA</td>
      <td>708546</td>
      <td>Holubynka</td>
    </tr>
  </tbody>
</table>
</div>




```python
city_select = cities_normal.sample(100)
city_select.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>coord.lat</th>
      <th>coord.lon</th>
      <th>country</th>
      <th>id</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>197406</th>
      <td>37.833389</td>
      <td>-86.632759</td>
      <td>US</td>
      <td>4288101</td>
      <td>Cloverport</td>
    </tr>
    <tr>
      <th>194999</th>
      <td>38.059738</td>
      <td>37.187859</td>
      <td>TR</td>
      <td>315835</td>
      <td>Celeyke</td>
    </tr>
    <tr>
      <th>129213</th>
      <td>28.863380</td>
      <td>117.165047</td>
      <td>CN</td>
      <td>1924418</td>
      <td>Lilin</td>
    </tr>
    <tr>
      <th>4782</th>
      <td>50.186050</td>
      <td>15.041670</td>
      <td>CZ</td>
      <td>3069236</td>
      <td>Nymburk</td>
    </tr>
    <tr>
      <th>11367</th>
      <td>63.049438</td>
      <td>147.185837</td>
      <td>RU</td>
      <td>2123100</td>
      <td>Myaundzha</td>
    </tr>
  </tbody>
</table>
</div>




```python
url = 'http://api.openweathermap.org/data/2.5/weather?'
units = 'imperial'

query_url = url + 'appid=' + weather_key + '&units=' + units + '&q=' 
```


```python
cities = city_select['name']
```


```python
name = cities

lat = []
temp = []
humidity = []
cloudiness = []
wind = []
          
        
for city in name:
    try:
        response = requests.get(query_url + city).json()
    except NameError:
        continue
    else:
        lat.append(response['coord']['lat'])
        temp.append(response['main']['temp'])
        humidity.append(response['main']['humidity'])
        cloudiness.append(response['clouds']['all'])
        wind.append(response['wind']['speed'])
```


```python
response
```




    {'base': 'stations',
     'clouds': {'all': 100},
     'cod': 200,
     'coord': {'lat': 40.04, 'lon': -74.58},
     'dt': 1522772100,
     'id': 7258259,
     'main': {'humidity': 87,
      'pressure': 1020,
      'temp': 40.73,
      'temp_max': 44.6,
      'temp_min': 37.4},
     'name': 'McGuire AFB',
     'sys': {'country': 'US',
      'id': 1981,
      'message': 0.1763,
      'sunrise': 1522751878,
      'sunset': 1522797922,
      'type': 1},
     'visibility': 16093,
     'weather': [{'description': 'light rain',
       'icon': '10d',
       'id': 500,
       'main': 'Rain'},
      {'description': 'mist', 'icon': '50d', 'id': 701, 'main': 'Mist'}],
     'wind': {'deg': 100, 'speed': 5.82}}




```python
weather = {'city': name, 'lat': lat, 'temp': temp, 'humidity' : humidity, 'clouds' : cloudiness, 'wind' : wind}
weather_final = pd.DataFrame(weather)
weather_final.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>clouds</th>
      <th>humidity</th>
      <th>lat</th>
      <th>temp</th>
      <th>wind</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>197406</th>
      <td>Cloverport</td>
      <td>1</td>
      <td>73</td>
      <td>37.83</td>
      <td>69.80</td>
      <td>20.80</td>
    </tr>
    <tr>
      <th>194999</th>
      <td>Celeyke</td>
      <td>0</td>
      <td>20</td>
      <td>38.06</td>
      <td>62.60</td>
      <td>13.87</td>
    </tr>
    <tr>
      <th>129213</th>
      <td>Lilin</td>
      <td>0</td>
      <td>88</td>
      <td>22.98</td>
      <td>73.40</td>
      <td>11.18</td>
    </tr>
    <tr>
      <th>4782</th>
      <td>Nymburk</td>
      <td>0</td>
      <td>26</td>
      <td>50.19</td>
      <td>67.55</td>
      <td>5.82</td>
    </tr>
    <tr>
      <th>11367</th>
      <td>Myaundzha</td>
      <td>64</td>
      <td>87</td>
      <td>63.05</td>
      <td>18.78</td>
      <td>6.26</td>
    </tr>
  </tbody>
</table>
</div>




```python
sns.set_context('notebook', font_scale = 1)
sns.lmplot(x = 'temp', y = 'lat', data = weather_final, fit_reg = False, size = 5, aspect = 2)
plt.title('Temperature vs Latitude')
plt.ylabel('Latitude')
plt.xlabel('Temperature')
plt.show()
```


![png](output_10_0.png)



```python
sns.set_context('notebook', font_scale = 1)
sns.lmplot(x = 'humidity', y = 'lat', data = weather_final, fit_reg = False, size = 5, aspect = 2)
plt.title('Humidity vs. Latititude')
plt.ylabel('Latitude')
plt.xlabel('Humidity')
plt.show()
```


![png](output_11_0.png)



```python
sns.set_context('notebook', font_scale = 1)
sns.lmplot(x = 'clouds', y = 'lat', data = weather_final, fit_reg = False, size = 5, aspect = 2)
plt.title('Cloudiness vs. Latitude')
plt.ylabel('Latitude')
plt.xlabel('Cloudiness')
plt.show()
```


![png](output_12_0.png)



```python
sns.set_context('notebook', font_scale = 1)
sns.lmplot(x = 'wind', y = 'lat', data = weather_final, fit_reg = False, size=5, aspect=2)
plt.title('Wind vs. Latitude')
plt.ylabel('Latitude')
plt.xlabel('Wind')
plt.show()
```


![png](output_13_0.png)

