# Weather Data Collector

This repository automates the collection of weather data using the OpenWeather API. 
The project retrieves 5-day weather forecasts for multiple cities and organizes the data into a structured format for further analysis.

## Features
- Fetches weather forecasts for multiple cities.
- Extracts useful data like temperature, weather conditions, and timestamps.
- Outputs the data in a clean CSV or DataFrame format.

## Setup
1. **Requirements**: Install Python libraries with:
   ```bash
   pip install requests pandas
   ```
2. **API Key**: Get a free API key from [OpenWeather](https://home.openweathermap.org/users/sign_up) ,  recommend using the [5 day forecast](https://openweathermap.org/forecast5) and replace `YOUR_API_KEY` in the script.

## How to Use
1. Define a list of cities in the script.
2. Run the script:
   ```bash
   python main.py
   ```
3. Processed weather data will be saved to a CSV file.

## Example Output
| datetime         | temperature | weather        | city      |
|------------------|-------------|----------------|-----------|
| 2024-12-03 12:00 | 5.5         | clear sky      | New York  |
| 2024-12-03 15:00 | 6.0         | broken clouds  | London    |


