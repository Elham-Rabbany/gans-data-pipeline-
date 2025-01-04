## Create "airports" dataframe

```
# coordinates for Berlin, Hamburg, Munich
# latitudes = [52.5200, 53.5511, 48.1374]
# longitudes = [13.4050, 9.9937, 11.5755]

def icao_airport_codes(latitudes, longitudes):

  #assert len(latitudes) == len(longitudes)

  list_for_df = []

  for i in range(len(latitudes)):

    url = "https://aerodatabox.p.rapidapi.com/airports/search/location"

    querystring = {"lat":latitudes[i],"lon":longitudes[i],"radiusKm":"50","limit":"5","withFlightInfoOnly":"true"}
    headers = {
      "X-RapidAPI-Host": "aerodatabox.p.rapidapi.com",
      "X-RapidAPI-Key": "14a44098c8mshe4536a007985112p1e3b4bjsn8fd805eb6bd4" #USE THIS KEY
    }

    response = requests.request("GET", url, headers=headers, params=querystring)

    list_for_df.append(pd.json_normalize(response.json()['items']))

  return pd.concat(list_for_df, ignore_index=True)

```
```
# coordinates for Berlin, Hamburg, Munich
latitudes = [52.5200, 53.5511, 48.1374]
longitudes = [13.4050, 9.9937, 11.5755]

icao_airport_codes_df = icao_airport_codes(latitudes, longitudes)
icao_airport_codes_df
```
## Prepare a dataframe for pushing to the database
```
# Selecting only the columns we need
airports_to_db = icao_airport_codes_df[["icao", "name", "municipalityName"]]
airports_to_db
```


### Read the cities table from the database to fetch the "city_id" column

In the same manner as with the "weather" dataframe the "airports" dataframe will be related to the "cities" table in the database. Thus, we need to get the corresponding city id from the cities table and implement it in the airports one (will become a foreign key here). Since the "airports" is a static table (it won't be updated on a regular basis wich means that the script for creating it will not be run on the cloud) we can get the city_ids there via merging it with the "cities" table.


```

from Keys import MySQL_pass

schema = "gans_local" # The name of your database
host = "127.0.0.1"
user = "root"
password = "USE YOUR MySQL PASSWORD HERE" # Your MySQL password
port = 3306
connection_string = f'mysql+pymysql://{user}:{password}@{host}:{port}/{schema}'
```

```
cities_df = pd.read_sql("cities", con=connection_string)
cities_df
```
#### Getting the "City_id" to the airports_to_db dataframe

```
cities_airports_merged = cities_df.merge(airports_to_db,
                                   left_on = "City_name",
                                   right_on = "municipalityName",
                                   how="left")

cities_airports_merged

# Selecting only the columns we need
airports_to_db = cities_airports_merged[["icao","name", "City_id"]]


airports_to_db.rename(columns={"name": "Airport_name"}, inplace=True)

```
### Push the "airports_to_db" to the empty "airports" table in the database

```
airports_to_db.to_sql('airports',
                  if_exists='append',
                  con=connection_string,
                  index=False)
```




