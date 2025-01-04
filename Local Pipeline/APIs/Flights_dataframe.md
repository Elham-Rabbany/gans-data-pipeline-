## Creating a function for multiple airports
```
def get_flight_data(icao_list):
  api_key = "14a44098c8mshe4536a007985112p1e3b4bjsn8fd805eb6bd4"

  berlin_timezone = timezone('Europe/Berlin')
  today = datetime.now(berlin_timezone).date()
  tomorrow = (today + timedelta(days=1))

  flight_items = []

  for icao in icao_list:
    # the api can only make 12 hour calls, therefore, 2 12 hour calls make a full day
    # using the nested lists below we can make a morning call and extract the data
    # then make an afternoon call and extract the data
    times = [["00:00","11:59"],
             ["12:00","23:59"]]

    for time in times:
      url = f"https://aerodatabox.p.rapidapi.com/flights/airports/icao/{icao}/{tomorrow}T{time[0]}/{tomorrow}T{time[1]}"

      querystring = {"withLeg":"true",
                    "direction":"Arrival",
                    "withCancelled":"false",
                    "withCodeshared":"true",
                    "withCargo":"false",
                    "withPrivate":"false"}

      headers = {
          'x-rapidapi-host': "aerodatabox.p.rapidapi.com",
          'x-rapidapi-key': api_key
          }

      response = requests.get(url, headers=headers, params=querystring)

      flights_json = response.json()

      retrieval_time = datetime.now(berlin_timezone).strftime("%Y-%m-%d %H:%M:%S")

      for item in flights_json["arrivals"]:
        flight_item = {
            "arrival_airport_icao": icao,
            "departure_airport_icao": item["departure"]["airport"].get("icao", None),
            "departure_airport_name": item["departure"]["airport"].get("name", None),
            "scheduled_arrival_time": item["arrival"]["scheduledTime"].get("local", None),
            "flight_number": item.get("number", None),
            "data_retrieved_at": retrieval_time
        }

        flight_items.append(flight_item)

  flights_df = pd.DataFrame(flight_items)
  flights_df["scheduled_arrival_time"] = flights_df["scheduled_arrival_time"].str[:-6]
  flights_df["scheduled_arrival_time"] = pd.to_datetime(flights_df["scheduled_arrival_time"])
  flights_df["data_retrieved_at"] = pd.to_datetime(flights_df["data_retrieved_at"])

  return flights_df
```
```
icao_list = ["EDDB"]

flights_to_db = get_flight_data(icao_list)
flights_to_db
```
# Create "flight" table in the database

```sql
CREATE TABLE flights(
	flight_id INT AUTO_INCREMENT,
    arrival_airport_icao VARCHAR(10),
    departure_airport_icao VARCHAR(10),
    departure_airport_name VARCHAR(30),
    scheduled_arrival_time DATETIME,
    flight_number VARCHAR(30),
    data_retrieved_at DATETIME,
    PRIMARY KEY (flight_id),
    FOREIGN KEY (arrival_airport_icao) REFERENCES airports(icao)
);
```
# Push the "flights_to_db" to the empty "flights" table in the database
```
from Keys import MySQL_pass

schema = "gans_local" # The name of your database
host = "127.0.0.1"
user = "root"
password = "USE YOUR MySQL PASSWORD HERE" # Your MySQL password
port = 3306

connection_string = f'mysql+pymysql://{user}:{password}@{host}:{port}/{schema}'


flights_to_db.to_sql('flights',
                    if_exists='append',
                    con=connection_string,
                    index=False

```
# Master function

```
import pandas as pd
from datetime import datetime, timedelta
import requests
from pytz import timezone
import sqlalchemy

def retreiving_and_sending_data_flights():
  connection_string = connection()
  airports_df = get_airports_data(connection_string)
  flights_df = get_flights_data(airports_df)
  send_flights_data(flights_df, connection_string)
  return "Data has been updated"

def connection():
  schema = "gans_local_2" # The name of your database
  host = "127.0.0.1"
  user = "root"
  password = "USE YOUR OWN MySQL PASSWORD" # Your MySQL password
  port = 3306
  return f'mysql+pymysql://{user}:{password}@{host}:{port}/{schema}'


def get_airports_data(connection_string):
  return pd.read_sql("airports", con=connection_string)


def get_flights_data(airports_df):
  api_key = "14a44098c8mshe4536a007985112p1e3b4bjsn8fd805eb6bd4" # USE THE PROVIDED KEY

  berlin_timezone = timezone('Europe/Berlin')
  today = datetime.now(berlin_timezone).date()
  tomorrow = (today + timedelta(days=1))

  flight_items = []



  for icao in airports_df["icao"]:
    # the api can only make 12 hour calls, therefore, 2 12 hour calls make a full day
    # using the nested lists below we can make a morning call and extract the data
    # then make an afternoon call and extract the data
    times = [["00:00","11:59"],["12:00","23:59"]]

    for time in times:
      url = f"https://aerodatabox.p.rapidapi.com/flights/airports/icao/{icao}/{tomorrow}T{time[0]}/{tomorrow}T{time[1]}"

      querystring = {"withLeg":"true",
                    "direction":"Arrival",
                    "withCancelled":"false",
                    "withCodeshared":"true",
                    "withCargo":"false",
                    "withPrivate":"false"}

      headers = {
          'x-rapidapi-host': "aerodatabox.p.rapidapi.com",
          'x-rapidapi-key': api_key
          }

      response = requests.get(url, headers=headers, params=querystring)

      flights_json = response.json()


      retrieval_time = datetime.now(berlin_timezone).strftime("%Y-%m-%d %H:%M:%S")
      icao_id = airports_df.loc[airports_df["icao"] == icao, "icao"].values[0]

      for item in flights_json["arrivals"]:
        flight_item = {
            "arrival_airport_icao": icao_id,
            "departure_airport_icao": item["departure"]["airport"].get("icao", None),
            "departure_airport_name": item["departure"]["airport"].get("name", None),
            "scheduled_arrival_time": item["arrival"]["scheduledTime"].get("local", None),
            "flight_number": item.get("number", None),
            "data_retrieved_at": retrieval_time
        }

        flight_items.append(flight_item)

  flights_df = pd.DataFrame(flight_items)
  flights_df["scheduled_arrival_time"] = flights_df["scheduled_arrival_time"].str[:-6]
  flights_df["scheduled_arrival_time"] = pd.to_datetime(flights_df["scheduled_arrival_time"])
  flights_df["data_retrieved_at"] = pd.to_datetime(flights_df["data_retrieved_at"])

  return flights_df

def send_flights_data(flights_df, connection_string):
  flights_df.to_sql('flights',
                    if_exists='append',
                    con=connection_string,
                    index=False)
```
