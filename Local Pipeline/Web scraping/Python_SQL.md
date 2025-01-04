
# Python to SQL, and back again
In this codealong we will show you how to create a relational database from your pandas DataFrames.

## 1.&nbsp; Import libraries 💾
If you haven't already installed sqlalchemy, you will need to. Uncomment the code below, install, and then recomment the code - you only need to install it once.


```
import pandas as pd
import requests
from bs4 import BeautifulSoup
import sqlalchemy
```
## 2 Create two tbles from the "cities_df"

Remember we want to have a relational database!!!


## 3 Creating the database and the backbone of cities & population tables

Before we can send the information in SQL, we need to make tables that have the same columns and data types to recieve the data. While we are creating a table for cities, we can also create the population table too.

Open MySQL Workbench, open a local connection, and open a new file. Then copy and paste the code from below.

```sql
-- Drop the database if it already exists
-- DROP DATABASE IF EXISTS gans_local;


-- Create the database
CREATE DATABASE gans_local;

-- Use the database
USE gans_local;


CREATE TABLE cities (
    City_id INT AUTO_INCREMENT, -- Automatically generated ID for each city
    City_name VARCHAR(255) NOT NULL, -- Name of the city
    Country VARCHAR(255) NOT NULL, -- Name of the country
    PRIMARY KEY (City_id) -- Primary key to uniquely identify each city
);


CREATE TABLE population (
    Population_id INT AUTO_INCREMENT,
    Population INT NOT NULL,
    Year_Data_Retrieved VARCHAR(255),
    City_id INT,
    PRIMARY KEY (Population_id),
    FOREIGN KEY (City_id) REFERENCES cities(City_id)
);
```



## 4 Push the "cities_to_db" to the empty "cities" table in the database

```

# Establishment of connection with the SQL database

schema = "gans_local" # The name of your database
host = "127.0.0.1"
user = "root"
password = # USE Your MySQL password
port = 3306

connection_string = f'mysql+pymysql://{user}:{password}@{host}:{port}/{schema}'


# Push the "cities_to_db" to the empty "cities" table in the MySQL data base

cities_to_db.to_sql('cities',
                  if_exists='append',
                  con=connection_string,
                  index=False)

```


## 5 Read the "cities" table from the database into the notebook

This step is needed to fetch the "city_id" column and integrate it into the "population_to_db" dataframe. The "city_id" column in "population_to_db" dataframe will serve as a foreign key in order to establish a relation between both "cities" & "population" tables


```
cities_from_sql = pd.read_sql("cities", con=connection_string)
cities_from_sql


# Getting the "City_id" to the population df

population_to_db["City_id"] = cities_from_sql["City_id"]
population_to_db

```

## 6 Push the "population_to_db" to the empty "population" table in the database

```
# Push the "population_to_db" to the empty "population" table in the MySQL data base

population_to_db.to_sql('population',
                  if_exists='append',
                  con=connection_string,
                  index=False)
```










