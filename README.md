# Project Explaination

The main objective is to create a real time weather report. This 
report will be continuously updated with weather information such
as temperature, condition, air quality etc for a preferred location.

## Environment Setup
-> we are using weatherapi.com website to configure our data source
-> firstally we created resource group
-> then create azure databricks workspace
-> Create a EventHubs namespace
-> Create Azure KeyVault

## Data Ingestion Using Databricks(DB)
1) Setup EventHub
2) Create a Cluster
3) Install EventHub Library in Cluster
4) Configure KeyVault in Databricks(DB) & 5) Sending Test Event from DB to EventsHub
6) Weather API testing in DB & 7)Developing complete code for getting weather data
8) Final data ingestion from DB to eventHub


## Data Ingestion Using Azure Functions(Another approach to do above step)
-> Go to Azure function app created in Environment Setup step.
-> Then go to functions which gives multiple approaches to create a function.
-> We'd go with creating a function using VScode.
-> Open Vscode. Goto extensions and search for azure functions and install it.
-> Upon succssfull installation, Azure icon appears below extensions icon.
-> Click on azure icon, we see 2 different sections Resources and Workspaces.
-> Install python extension.
-> Now we are going to estabish connection between VScode and Azure tenant.
-> Now goto AzureIcon->Resources->signInToAzure and complete sign in.
-> Upon successfull sign in we can see 'Azure Subscriptions' under Resources
   section in Azure. We can see our function app here under Azure Subscriptions.
-> Now go AzureIcon->workspace->CreateFunctionProject->NewFolder->giveFoldername
   as weather-streaming-function-app->create->select
-> Now choose "python" as programming language
-> choose "model V2" as python programming model
-> choose "skip virtual environment" as python interpreter
-> choose "timer trigger" as template for your project's first function
-> choose "weatherapifunction" as name of the function
-> now enter cron expression for 30 sec timer, refer microsoft documentation
   https://learn.microsoft.com/en-us/azure/azure-functions/functions-bindings-timer?tabs=python-v2%2Cisolated-process%2Cnodejs-v4&pivots=programming-language-csharp
-> enter '*/30*****' in this case for 30seconds and open proejct in the
   current window.
-> Now multiple files have been added under project name in the lelft pane.
-> open function_app.py file, here we'll write our main code
-> requirement.txt file is used to list all the libraries used to create function app.
-> Now we need to make changes to the code that we wrote in Databricks notebook,
   the updated code file can be referred in data workspace.
-> After completing the code logic, we'll now deploy it to Azure.
-> Goto AzureIcon->workspace->project->cloudicon(in front of project name)->
   choose function-app(fp-weather-streaming)
-> Now goto AzurePortal-> function-app->refresh->functions, we can see 
   weatherapifunction under functions, which means deployment successfull.
-> If we want to stop sending events we can click 'stop' inside function app.

