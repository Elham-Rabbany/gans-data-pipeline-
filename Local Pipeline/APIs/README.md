
# Data Collection by API  

To enrich the pipeline with real-time data, two key APIs were integrated:  

## OpenWeatherMap API  

The  [OpenWeather](https://home.openweathermap.org/users/sign_up) provided real-time weather data, crucial for understanding scooter usage patterns, especially during cold or rainy days.  

### Key Data Collected:  
- **Temperature**: Helps analyze user comfort levels.  
- **Precipitation**: Tracks rain and snow conditions that deter scooter usage.  
- **Forecast Time**: Enables predictions based on changing weather patterns.  

### Storage:  
The collected data was structured and stored in the **Weather Table**, reflecting the API’s response schema. Automated daily updates ensured consistent access to up-to-date weather data for analysis.  

---

## AeroDataBox API  

The [AeroDataBox API](https://rapidapi.com/aedbx-aedbx/api/aerodatabox)  was used to analyze tourist activity, which significantly impacts scooter demand near airports and urban hotspots.  

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

