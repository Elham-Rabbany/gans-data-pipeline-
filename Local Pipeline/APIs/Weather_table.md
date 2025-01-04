# Making the API call and viewing the json
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
