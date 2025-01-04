
Web scraping to collect city-specific data (e.g., population, latitude, longitude).
# Web scraping

Web scraping is a technique used to extract data from websites. It involves sending HTTP requests to websites, parsing the returned HTML code, and extracting the desired data. Web scraping is a powerful tool for data scientists as it allows them to collect large amounts of data from the web. This data can then be used to train machine learning models, analyse trends, and make informed business decisions.

## 2.&nbsp; Beautiful Soup 🍲

[Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) is a Python library that simplifies the process of web scraping. It provides a user-friendly interface for parsing HTML documents, enabling users to extract specific information from websites. Through Beautiful Soup, you can navigate the HTML tree structure, locate elements based on their tags, attributes, and content, and extract the desired data into a structured format.


## Challenge 2 😀
Utilise web scraping to gather information about three German cities – Berlin, Hamburg, and Munich – from Wikipedia. we will start by extracting basic information: the country, the latitude and the longitude of each city and then expand to more dynamic data such as the population.

1. Scraping Basic Information

  1.1. Begin by scraping the country, the latitude and the longitude of each city from their respective Wikipedia pages:

 - Berlin: https://en.wikipedia.org/wiki/Berlin
 - Hamburg: https://en.wikipedia.org/wiki/Hamburg
 - Munich: https://en.wikipedia.org/wiki/Munich

  1.2. Once we have scraped the basic information of each city, reflect on the similarities and patterns in accessing them across the three pages. Also, analyse the URLs to identify any commonalities. Make a loop that executes once and retrieves the country, latitude, and longitude for all three cities.

2. Data Organisation

  2.1 Utilise pandas DataFrame to effectively store the extracted information. This DataFrame should have a row for each city, and columns for each type of information (cityname, country, latitude, longitude). If you feel brave, change latitude and longitude into decimal format.

  2.2 Looking ahead (optional): Create a function from the loop and DataFrame to encapsulate the scraping process. This function can be used repeatedly to fetch updated data whenever necessary. It should return a clean, properly formatted DataFrame.

  ```
import pandas as pd
import requests
from bs4 import BeautifulSoup
from datetime import datetime # to get today's date


def populations_dataframe(cities):

    population_data = []

    for city in cities:
        url = f"https://www.wikipedia.org/wiki/{city}"
        response = requests.get(url)
        city_soup = BeautifulSoup(response.content, 'html.parser')

        # extract the relevant information
        city_population = city_soup.find(string="Population").find_next("td").get_text()
        city_population_clean = int(city_population.replace(",", ""))
        today = datetime.today().strftime("%d.%m.%Y")

        # keep track of data per city
        population_data.append({"City": city,
                        "Population": city_population_clean,
                        "Timestamp_Population": today
                        })

    return pd.DataFrame(population_data)

 ```

### call the populations function

 ```
cities = ["Berlin", "Hamburg", "Munich"]

population_df = populations_dataframe(cities)
population_df
 ```
