# Project Explaination

The main objective is to create a real time weather report. This 
report will be continuously updated with weather information such
as temperature, condition, air quality etc for a preferred location.

## Environment Setup
1. we are using weatherapi.com website to configure our data source
2. firstally we created resource group
3. then create azure databricks workspace
4. Create a EventHubs namespace
5. Create Azure KeyVault

## Data Ingestion Using Databricks(DB)
1) Setup EventHub
2) Create a Cluster
3) Install EventHub Library in Cluster
4) Configure KeyVault in Databricks(DB) & 5) Sending Test Event from DB to EventsHub
6) Weather API testing in DB & 7)Developing complete code for getting weather data
8) Final data ingestion from DB to eventHub
