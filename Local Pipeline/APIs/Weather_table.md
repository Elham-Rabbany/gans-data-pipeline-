# Making the API call and viewing the json

If you want to search for the city by name and country instead of latitude and longitude, try using the [Geocoding API](https://openweathermap.org/api/geocoding-api).
```
# Berlin
latitude = 52.520008
longitude = 13.404954

API_key = userdata.get('openWeatherApi')

# check out the docs for more info on making an api call https://openweathermap.org/forecast5
url = (f"https://api.openweathermap.org/data/2.5/forecast?lat={latitude}&lon={longitude}&appid={API_key}&units=metric")

response = requests.get(url)
json = response.json()
```

# Creating a function for multiple cities

```
import pandas as pd
from datetime import datetime
from pytz import timezone

def fetch_weather_data(cities):
  # Let's ensure the timezone is our local timezone
  berlin_timezone = timezone('Europe/Berlin')
  API_key = userdata.get('openWeatherApi')
  weather_items = []

  for city in cities:

    country = "DE"

    # Geocoder api to get the latitude and longitude
    # This information should be from your SQL table, but we don't have that here
    geo_url = (f"http://api.openweathermap.org/geo/1.0/direct?q={city},{country}&limit=5&appid={API_key}")
    geo_response = requests.get(geo_url)
    geo_json = geo_response.json()
    latitude = geo_json[0]["lat"]
    longitude = geo_json[0]["lon"]

    # weather api
    weather_url = (f"https://api.openweathermap.org/data/2.5/forecast?lat={latitude}&lon={longitude}&appid={API_key}&units=metric")
    weather_response = requests.get(weather_url)
    weather_json = weather_response.json()

    # Added the time retrieved so we know when the forecast was made
    retrieval_time = datetime.now(berlin_timezone).strftime("%Y-%m-%d %H:%M:%S")

    for item in weather_json["list"]:
        weather_item = {
            # Added the city name, so the information is clear when looking at multiple cities
            "city": city,
            "forecast_time": item.get("dt_txt", None),
            "temperature": item["main"].get("temp", None),
            "forecast": item["weather"][0].get("main", None),
            "rain_in_last_3h": item.get("rain", {}).get("3h", 0),
            "wind_speed": item["wind"].get("speed", None),
            "data_retrieved_at": retrieval_time
        }

        weather_items.append(weather_item)

  weather_df = pd.DataFrame(weather_items)

  return weather_df
```
```
fetch_weather_data(["Berlin", "Hamburg", "Munich"])
```

# Making our function work with our SQL cities data
This section delves into a more advanced aspect of the project, which may be beyond the point many of you got to. However, it serves as an illustrative example of how to structure your work effectively. It demonstrates how we can compartmentalise various aspects of our data processing pipeline into distinct functions, enhancing code readability and facilitating debugging.
> This part will only work locally, not on colab. You must also create your weather table in SQL first.

```

import pandas as pd
import requests
from pytz import timezone
from datetime import datetime

def retrieve_and_send_data():
  connection_string = create_connection_string()
  cities_df = fetch_cities_data(connection_string)
  weather_df = fetch_weather_data(cities_df)
  store_weather_data(weather_df, connection_string)
  return "Data has been updated"

def create_connection_string():
  schema = "gans"
  host = "127.0.0.1"
  user = "root"
  password = "YOUR_PASSWORD_HERE"
  port = 3306
  return f'mysql+pymysql://{user}:{password}@{host}:{port}/{schema}'

def fetch_cities_data(connection_string):
  return pd.read_sql("cities", con=connection_string)

def fetch_weather_data(cities_df):
  berlin_timezone = timezone('Europe/Berlin')
  API_key = 'YOUR_API_HERE'
  weather_items = []

  for _, city in cities_df.iterrows():
      latitude = city["latitude"]
      longitude = city["longitude"]
      city_id = city["city_id"]

      url = (f"https://api.openweathermap.org/data/2.5/forecast?lat={latitude}&lon={longitude}&appid={API_key}&units=metric")
      response = requests.get(url)
      weather_data = response.json()

      retrieval_time = datetime.now(berlin_timezone).strftime("%Y-%m-%d %H:%M:%S")

      for item in weather_data["list"]:
          weather_item = {
              "city_id": city_id,
              "forecast_time": item.get("dt_txt"),
              "temperature": item["main"].get("temp"),
              "forecast": item["weather"][0].get("main"),
              "rain_in_last_3h": item.get("rain", {}).get("3h", 0),
              "wind_speed": item["wind"].get("speed"),
              "data_retrieved_at": retrieval_time
          }
          weather_items.append(weather_item)

  weather_df = pd.DataFrame(weather_items)
  weather_df["forecast_time"] = pd.to_datetime(weather_df["forecast_time"])
  weather_df["data_retrieved_at"] = pd.to_datetime(weather_df["data_retrieved_at"])

  return weather_df

def store_weather_data(weather_df, connection_string):
  weather_df.to_sql('weather',
                    if_exists='append',
                    con=connection_string,
                    index=False)


retrieve_and_send_data()
```









