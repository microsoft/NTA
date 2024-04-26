# Deploying the Backend to Azure

### Creating Azure Resources

1. Login to the Azure Subscription 
2. Go to the [Azure Cloud Shell](https://learn.microsoft.com/en-us/azure/cloud-shell/new-ui-shell-window) PowerShell command terminal
3. [Upload](https://learn.microsoft.com/en-us/azure/cloud-shell/new-ui-shell-window) the [*.bicep](/build) files to the Azure Cloud Shell environment

> ***Note:***
> The *.bicep files are located ./charity-suite-api/build/

4. Execute the main.bicep file to deploy the pre-defined resource.

```powershell
az deployment sub create --location <azure region> --template-file "main.bicep" --parameters resourceGroupName=<RESOURCE NAME> resourceGroupLocation=<azure region> dbServerName=<name of db server> dbName=<name of database> serverFarmName=<name of app service> apiName=<API name> adminDbLogin='<admin database user name>' adminDbPassword='<admin db password>' 
```

**Example**
```powershell
az deployment sub create --location eastus --template-file "main.bicep" --parameters resourceGroupName=DEV resourceGroupLocation=eastus dbServerName=d-gwb-db dbName=d-gwb-conference-db serverFarmName=ASP-dev-bfea apiName=d-gwb-conference-api adminDbLogin='dbadminuser' adminDbPassword='xxxxxxxxxxxxx' 
```

### Configuring Database Network Security
1. Login to the Azure Subscription
2. Navigate to the database server resource
3. Select the Networking option from the Security menu blade
4. Check the "Allow Azure services and resources to access this server" option

> ***Note:***
> For details on configuring this setting see the [Allow Azure Service](https://learn.microsoft.com/en-us/azure/azure-sql/database/network-access-controls-overview?view=azuresql#allow-azure-services) located [here](https://learn.microsoft.com/en-us/azure/azure-sql/database/network-access-controls-overview?view=azuresql#allow-azure-services).
>
> ![Alt text](https://github.com/microsoft/NTA/blob/main/Event%20Application/image.png)

### Deploying the API to Azure

**Download the Publish Profile**
1. Login to the Azure Subscription
2. Navigate to the newly created Azure API resource
3. [Download the Publish Profile](https://learn.microsoft.com/en-us/visualstudio/azure/how-to-get-publish-profile-from-azure-app-service?view=vs-2022)

**Create GitHub Action Secret**
1. Login to your GitHub code repository
2. [Create an Action Secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions) named *AZURE_PROFILE_NAME* to store the Azure Publish Profile

> ***Note:***
> This Action secret will be used in the GitHub Action.

**Create GitHub Action**
1. Login to you GitHub code repository
2. Make copy the [main.yml](/.github/workflows/main.yml) file

> ***Note:*** Consider naming the file to represent the deployment environment. For example: *second-nonprofit.yml*.

3. Edit the newly created Action workflow by changing the following:
- AZURE_WEBAPP_NAME
- AZURE_PROFILE_NAME

> ***Note:*** 
>
> - AZURE_WEBAPP_NAME should represent the name you gave the API name from the [Creating Azure Resources](#creating-azure-resources) section
> - AZURE_PROFILE_NAME should represent the name of the Action Secret defined in the *Create GitHub Action Secret* section.

4. Execute the newly created workflow.

### Configure the database connection string for the API
1. Login to the Azure Subscription
2. Navigate to the newly created Azure Database Server Resource
3. Make a copy of the connection string
2. Navigate to the newly created Azure API resource
3. Add a configuration setting for the database connection string. The configuration setting name must be *AppSettings__DBConnection*

>**Note:** See the [Configure an App Service app](https://learn.microsoft.com/en-us/azure/app-service/configure-common?tabs=portal) [here](https://learn.microsoft.com/en-us/azure/app-service/configure-common?tabs=portal) for details.

### Deploying the Database Schema
1. Login to you GitHub code repository
2. Make copy the [main-db.yml](https://github.com/microsoft/NTA/blob/main/Event%20Application/main-db.yml) file

> ***Note:*** Consider naming the file to represent the deployment environment. For example: *second-nonprofit-db.yml*.

3. [Create an Action Secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions) to store the database connection string

4. Edit the newly created Action workflow by changing the **DB_CONNECTION_STRING** value.
5. Execute the newly created workflow
