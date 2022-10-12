## ChatBot

** Infra Deployment Steps

-- To create Managed Identity
az identity create --name UMI_deliverybot --resource-group RG-FedExEurope_DeliveryBot --location westeurope

-- To Deploy Appservice for Bot (arm templates are in deploymentTemplates folder)
az deployment group create --template-file template-BotApp-with-rg.json --parameters @parameters-for-template-BotApp-with-rg.json --resource-group RG-FedExEurope_DeliveryBot

-- To Deploy Bot Service
az deployment group create --template-file template-AzureBot-with-rg.json --parameters @parameters-for-template-AzureBot-with-rg.json --resource-group RG-FedExEurope_DeliveryBot

set RESOURCE_GROUP_NAME=RG-FedExEurope_DeliveryBot
set APP_SERVICE_PLAN_NAME=asp-chatbot-delivery
set APP_SERVICE_NAME=chatbot-delivery
set LOCATION="westeurope"
set LOG_ANALYTICS_WORKSPACE="chatbot-delivery-workspace"
set APP_INSIGHTS_NAME="chatbot-delivery-app"

az webapp log config --application-logging filesystem --detailed-error-messages true --failed-request-tracing true --resource-group %RESOURCE_GROUP_NAME%  --name %APP_SERVICE_NAME% --level verbose --web-server-logging filesystem

az monitor log-analytics workspace create --resource-group %RESOURCE_GROUP_NAME% -n %LOG_ANALYTICS_WORKSPACE%

az monitor app-insights component create --app %APP_INSIGHTS_NAME% --location %LOCATION% --kind web --resource-group %RESOURCE_GROUP_NAME% --application-type web --workspace %LOG_ANALYTICS_WORKSPACE%

az webapp config appsettings set --name %APP_SERVICE_NAME% --resource-group %RESOURCE_GROUP_NAME% --settings APPINSIGHTS_INSTRUMENTATIONKEY=a77e9c5a-8e7e-4382-8c0b-66a813861b0c APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=a77e9c5a-8e7e-4382-8c0b-66a813861b0c WEBSITE_NODE_DEFAULT_VERSION=10.14.1 ApplicationInsightsAgent_EXTENSION_VERSION=~2



