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

- ## How to Use
- Setting up the MySQL database.
- Running the local pipeline.
- Deploying the pipeline to the cloud.
