
# Data Collection by API  

To enrich the pipeline with real-time data, two key APIs were integrated:  

## OpenWeatherMap API  

The **OpenWeatherMap API** provided real-time weather data, crucial for understanding scooter usage patterns, especially during cold or rainy days.  

### Key Data Collected:  
- **Temperature**: Helps analyze user comfort levels.  
- **Precipitation**: Tracks rain and snow conditions that deter scooter usage.  
- **Forecast Time**: Enables predictions based on changing weather patterns.  

### Storage:  
The collected data was structured and stored in the **Weather Table**, reflecting the API’s response schema. Automated daily updates ensured consistent access to up-to-date weather data for analysis.  

---

## AeroDataBox API  

The **AeroDataBox API** was used to analyze tourist activity, which significantly impacts scooter demand near airports and urban hotspots.  

### Key Data Collected:  
- **Arrival Times**: Tracks the busiest times at airports.  
- **Airline Names**: Identifies tourist trends by carriers.  
- **Airport Codes**: Links flight data to specific locations.  

### Storage:  
This data was organized into the **Flights Table**, designed to mirror the API’s schema for seamless integration and analysis.  

---

# Static Airport Data  

In addition to real-time data, static airport information was included to enhance the dataset:  
- **ICAO Codes**: Unique airport identifiers were collected.  
- **City Linkage**: Each airport was connected to its corresponding city using a **foreign key** in the **Airports Table**, establishing a clear relationship for analysis.  

---

# Weather Data Collector  

This repository includes a script that automates the collection of weather data using the **OpenWeatherMap API**.  

## Features  
- Fetches 5-day weather forecasts for multiple cities.  
- Extracts key metrics such as temperature, weather conditions, and timestamps.  
- Outputs the data in structured formats, including **CSV** or **Pandas DataFrame**.  

---

## Setup  

### 1. Install Dependencies  
Install the required Python libraries:  
```bash
pip install requests pandas
```  

### 2. Obtain an API Key  
Get a free API key from [OpenWeather](https://home.openweathermap.org/users/sign_up) and replace `YOUR_API_KEY` in the script.  

### 3. Define Cities  
Add a list of cities in the script to fetch weather data for those locations.  

---

## How to Run  

Execute the script using the following command:  
```bash
python main.py
```  

The processed weather data will be saved as a **CSV** file for further analysis.  

---

## Example Output  

| datetime         | temperature | weather        | city      |  
|------------------|-------------|----------------|-----------|  
| 2024-12-03 12:00 | 5.5         | clear sky      | New York  |  
| 2024-12-03 15:00 | 6.0         | broken clouds  | London    |  
