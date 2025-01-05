


# Elevating the Data Pipeline to the Cloud
With the local pipeline fully operational, the next step was transitioning to the cloud. This phase leveraged Google Cloud Platform (GCP) to unlock automation, scalability, and real-time updates, transforming the pipeline into a robust and cloud-native solution.

## Key Steps
### 1. Setting Up the Cloud Database
Migrated the MySQL database from the local pipeline to Google Cloud SQL.
Retained the schema from Phase 1, with static tables for cities and airports remaining unchanged.
Dynamic tables for weather and flight data were configured for frequent updates, ensuring seamless integration between local systems and the cloud database.
Adjusted the database connection string for compatibility with the cloud-hosted instance.
### 2. Deploying Python Scripts on Cloud Functions
Deployed Python scripts for data collection as serverless functions using Google Cloud Functions.
These functions automated the retrieval of real-time data from external APIs:
OpenWeatherMap API: Provided weather forecasts crucial for understanding e-scooter usage.
AeroDataBox API: Retrieved flight data to track tourist activity influencing scooter demand.
Packaged all dependencies (e.g., sqlalchemy, pandas, requests) to ensure compatibility within the GCP environment, avoiding deployment issues.
### 3. Automating Updates with Cloud Scheduler
Utilized Google Cloud Scheduler to trigger cloud functions at defined intervals, automating daily data updates.
Scheduled scripts to fetch weather and flight data once per day, ensuring the database remained up-to-date without manual intervention.
## Challenges and Solutions
Cloud Function Errors: Debugging deployment issues (e.g., undefined variables and authentication settings) ensured stable execution.
Automation Tuning: Proper scheduling with GCP Scheduler optimized the system for real-time data updates.
## Results and Impact
The cloud pipeline achieved the following:

Real-Time Data Updates: Daily updates for weather and flight data enabled smarter, data-driven decision-making.
Scalable Infrastructure: The serverless architecture supports future data sources and scaling without additional complexity.
Operational Efficiency: Fully automated updates eliminated manual intervention, saving time and reducing errors.# Real-Time Data Pipeline for GAN Insights



