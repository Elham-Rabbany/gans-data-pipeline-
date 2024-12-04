# Gans Data Pipeline

## Overview
This project is a two-phase data pipeline designed for Gans e-scooter mobility analytics. It begins with local development for data collection and transformation, followed by cloud migration for enhanced scalability and automation.

## Features
- **Phase 1: Local Pipeline**
  - Web scraping to collect city-specific data (e.g., population, latitude, longitude).
  - Integration of APIs (e.g., weather and flight data).
  - Data storage in a local SQL database.

- **Phase 2: Cloud Pipeline**
  - Migration of the SQL database to Google Cloud Platform (GCP).
  - Automation of data collection scripts using GCP Cloud Functions.
  - Scheduling of tasks with GCP Cloud Scheduler.

## Repository Structure
- `/scripts`: Python scripts for web scraping, API integration, and database operations.
- `/data`: Sample datasets used during development.
- `/notebooks`: Jupyter notebooks demonstrating the pipeline implementation.
- `/docs`: Documentation and visualizations.

## How to Use
- Setting up the MySQL database.
- Running the local pipeline.
- Deploying the pipeline to the cloud.



### Sub-Project: Weather Data Collection via OpenWeatherMap API

**Objective**: Automate weather data collection for multiple cities using the OpenWeatherMap API.

**Key Details**:
- **API Endpoint**:  
  `https://api.openweathermap.org/data/2.5/forecast?lat={lat}&lon={lon}&appid={API key}&units=metric`
- **Input**: Latitude, Longitude, and API Key.
- **Output**: JSON weather data for a 5-day forecast in metric units.

**Steps**:
1. Fetch weather data using the above API endpoint.
2. Parse and extract relevant information.
3. Convert data into a structured Pandas DataFrame.
4. Automate for multiple cities with a reusable function.

**Useful Links**:
- [OpenWeatherMap API Documentation](https://openweathermap.org/api)
- [Requests Library Documentation](https://docs.python-requests.org/)
- [Pandas Documentation](https://pandas.pydata.org/docs/)

**Usage**: Provide a list of cities with coordinates and retrieve weather data in an analyzable format. Perfect for automating weather-based decisions.
