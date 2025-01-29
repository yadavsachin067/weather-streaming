Project Explaination

The main objective is to create a real time weather report. This 
report will be continuously updated with weather information such
as temperature, condition, air quality etc for a preferred location.
Additionally a key feature is the ability to receive real time alerts
via email in the event of any extreme unexpected weather condition.

Environment Setup
-> we are using weatherapi.com website to configure our data source
-> firstally we created resource group, choose the region which 
   is closest to your location
-> then create azure databricks workspace
-> then goto create function app, choose infrastructure/hosting model
   which is consumption model in our case
-> choose python as runtime stack, which is basically the language that 
   we are going to use while creating function app
-> In montioring tab, enable application insights as it helps in monitoring
   and debugging the function app in case of a failure. finally click create.
-> Create a EventHubs namespace
-> Go ,Create Azure KeyVault, enable soft delete
-> In Access Configuration tab, choose permission model carefully.
-> Now, go to keyvault, expands objects, go to secrets, you will see
   unauthorised error.
-> Now go to Access Control(IAM), then go to Role Assignments 
-> Now click on Add dropdown and then click Add Role Assignment
   and choose "Key Vault Secret Officer" from list of roles.
-> Now in Members tab, click select members and choose your account name
   from the list, finally click create.
-> Now again go to secrets, there won't be any authorisation error.
-> Create weatherapi secret key in secrets using api key value.
-> Now Create fabric capacity, choose size wisely, F2 in our case.
-> We can pause fabric capacity when not in use as it is expensive.
-> Now go to PowerBi->Workspaces->MyWoskpace->options(...)->WorkspacesSettings
   ->LicenseInfo->LicenseConfiguration->Edit->FabricCapacity
-> From Fabric Capacity dropdown choose the capacity we just created.

Data Ingestion Using Databricks(DB)
1)Setup EventHub
-> Go to EventHub namespace, then click create(+) Event Hub at the top.
-> Give name to Event Hub, choose partition count, which is a configuration
   for parallel processing. Higher the partition count value, better the 
   performance in terms of throughput and scalability. It has additional 
   cost(billing) associated with it.
-> Choose Cleanup policy which associates with the cleaning of events that 
   are arranged in events hub. Give Retention time value which is basically
   the time for which we want the events to be retained before cleanup.
-> Finally click create, now eventHub instance is visible in Event Hubs list.
-> Now go inside the eventhub instance, now setup its access to databricks.
   goto settings->sharedAccessPolicies, which is basically connection string 
   for this Eventhub instance. Click Add(+), give policyName and now select 
   permissions from the list send/manage/listen. Send in our case. Finally 
   click create. Now the policy(named fordatabricks) is visible.
-> Click on the policy, we'll see multiple connection strings which can be 
   used any. Now copy connectionStringPrimaryKey. Goto keyvault->secrets
   click generate/import to create a secret key, give name, give copied 
   value in secret value and click create. Secret key has been created.
2)Create a Cluster
-> Now go to databricks workspace. goto compute->createWithPersonalCompute
   configure name, policy, single user access, databricks runtime version,
   nodeType. Give 0 value to terminate after __ minutes of inactivity as we
   are dealing with streaming data and we want the cluster to remain active 
   throughout. Click create and finally the cluster is created.
3) Install EventHub Library in Cluster
-> Goto cluster->libraries, click->installNew->Pypi->package, paste package 
   name from pypi.org/project/azure-eventhub/ . Restart the cluster for 
   successfull installation of library.
4) Configure KeyVault in Databricks(DB) & 5) Sending Test Event from DB to EventsHub
6) Weather API testing in DB & 7)Developing complete code for getting weather data
8) Final data ingestion from DB to eventHub
-> Firstally create secretScope so that we can use secrets keys inside our code in 
   Databricks, refer azure documentation for Azure Key Vault-backed secret scopes
   it https://learn.microsoft.com/en-us/azure/databricks/security/secrets/.
-> Since we want to use secret keys inside our code in databricks but we don't have
   the access,we'll add a new role. Goto keyvault->IAM->RoleAssignment->
   AddRoleAssignment->keyVaultSecretsUser->selectMembers , search "azuredatabricks"
   and select. Now we can read secrets from keyVault in our code in databricks.
-> Now open a new notebook in databricks, rename notebook, attach the cluster,
   now write the logic to send a test event to eventshub.
-> Goto eventhub namespace, click on eventhub instance, then go to Data Explorer
   click on view events, now you can see the test event.

Data Ingestion Using Azure Functions(Another approach to do above step)
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

Event Processing and Loading
-> Firstally create a new workspace for our project.
-> Goto PowerBi->workspaces->new workspace->give name(weather-streaming)->create
-> Now we need to get data from eventHub to KQL Database in Fabric.
-> KQL Database is ideal for handling streaming data, it is created inbuilt within
   an eventhouse with same name as the eventhouse. 
-> Now create an eventhouse in fabric.
-> Goto workspace->newItem(+)->eventhouse->give name(weather-eventhouse)-> create
-> Now create an eventstream in fabric to get data from eventHub.
-> Goto workspace->newItem(+)->eventstream->give name(weather-eventstream1)-> create
-> Now we'll add source ie Eventhub to this eventstream
-> Goto eventstream->AddSource->ExternalSources->AzureEventHub->Connect, now configure
   connection settings, for that we'll create connection string in eventhub.
-> Go inside the eventhub instance, now setup its access to fabric.
   goto settings->sharedAccessPolicies, which is basically connection string 
   for this Eventhub instance. Click Add(+), give policyName and now select 
   permissions from the list send/manage/listen. Listen in our case. Finally 
   click create. Now the policy named forfabric is visible.
-> Now go back to the Connect window in fabric.
-> Now give eventhub namespace name, eventhub name,  new connection, authentication
   type as Shared Access Key and give keyname(forfabric) and value(primary key) from
   above step, click connect, give consumer name as dataExplorer->consumername from
   azure eventhub instance. Click next->next->add.
-> Now pipeline(eventstream) has been created, we can preview data received from eventhub.
-> We've successfuly configured datasource(Eventhub), now we'll configure destination.
-> Right click destinations, we can choose from 'operations' to do any transformations
   (skip in our case) and choose eventhouse under 'destinations' list. configure window
-> choose 'Event processing before ingestion' as data ingestion mode as it will create
   a KQL destination table automatically, give destination name(weather-target),choose
   weather-streaming as workspace, weather-eventhouse as Eventhouse and KQL database
   both, create new table(weather table) as KQL destination table, JSON as input data
   format, check 'activate ingestion...' option, click save.
-> We've configured destination successfully.
-> Click refresh at rightmost in test result window to preview data.
-> Now publish the pipeline using 'publish' button at top right. Publish successfull.
-> Pipeline execution will start automatically on publishing.
-> Pipeline will run 24*7 until stopped manually.
-> Now check if the pipeline is working fine.
-> Go to weather-eventhouse-> KQL Databases->Tables->WeatherTable->options(...)->
   queryTable->ShowAny100Rows, its show us the received data. Pipeline sucessfull.
-> We can use the data in weather table to create reports using PowerBI.
   
