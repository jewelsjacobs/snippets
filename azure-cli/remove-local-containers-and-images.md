# Useful Azure CLI Commands

## Set up a MEAN Stack site with repo

```
az login
az group create -n myResourceGroup -l uswest
```

### Mongodb Server
```
#!/bin/bash

# Set variables for the new account, database, and collection
resourceGroupName='myResourceGroup'
location='southcentralus'
name='docdb-test'
databaseName='docdb-mongodb-database'
collectionName='docdb-mongodb-collection'

# Create a resource group
az group create \
	--name $resourceGroupName \
	--location $location

# Create a MongoDB API Cosmos DB account
az cosmosdb create \
	--name $name \
	--kind MongoDB \
	--resource-group $resourceGroupName \
	--max-interval 10 \
	--max-staleness-prefix 200

# Create a database 
az cosmosdb database create \
	--name $name \
	--db-name $databaseName \
	--resource-group $resourceGroupName

# Create a collection
az cosmosdb collection create \
	--collection-name $collectionName \
	--name $name \
	--db-name $databaseName \
	--resource-group $resourceGroupName

# Get the connection string for MongoDB apps
az cosmosdb list-connection-strings \
	--name $name \
	--resource-group $resourceGroupName
```

### Connect a web app to Cosmos DB

```
#/bin/bash

# Variables
appName="webappwithcosmosdb$random"
storageName="webappwithcosmosdb$random"
location="WestUS"

# Create a Resource Group 
az group create --name myResourceGroup --location $location

# Create an App Service Plan
az appservice plan create --name WebAppWithCosmosDBPlan --resource-group myResourceGroup --location $location

# Create a Web App
az webapp create --name $appName --plan WebAppWithCosmosDBPlan --resource-group myResourceGroup 

# Create a Cosmos DB
cosmosdb=$(az cosmosdb create --name $appName --resource-group myResourceGroup --query documentEndpoint --output tsv)
cosmosCreds=$(az cosmosdb list-keys --name $appName --resource-group myResourceGroup --query primaryMasterKey --output tsv)

# Assign the connection string to an App Setting in the Web App
az webapp config appsettings set --settings "COSMOSDB_URL=$cosmosdb" "COSMOSDB_KEY=$cosmosCreds" --name $appName --resource-group myResourceGroup
```

### Create a web app with continuous deployment from GitHub
https://docs.microsoft.com/en-us/azure/app-service-web/scripts/app-service-cli-continuous-deployment-github?toc=%2fcli%2fazure%2ftoc.json

```
#!/bin/bash

gitrepo=<replace-with-URL-of-your-own-GitHub-repo>
token=<replace-with-a-GitHub-access-token>
webappname=mywebapp$RANDOM

# Create a resource group.
az group create --location westeurope --name myResourceGroup

# Create an App Service plan in `FREE` tier.
az appservice plan create --name $webappname --resource-group myResourceGroup --sku FREE

# Create a web app.
az webapp create --name $webappname --resource-group myResourceGroup --plan $webappname

# Configure continuous deployment from GitHub. 
# --git-token parameter is required only once per Azure account (Azure remembers token).
az webapp deployment source config --name $webappname --resource-group myResourceGroup \
--repo-url $gitrepo --branch master --git-token $token

# Browse to the web app.
az webapp browse --name $webappname --resource-group myResourceGroup
```

### Create a VM with Docker

```
#!/bin/bash

# Create a resource group.
az group create --name myResourceGroup --location westeurope

# Create a new virtual machine, this creates SSH keys if not present.
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys

# Open port 80 to allow web traffic to host.
  az vm open-port --port 80 --resource-group myResourceGroup --name myVM

# Install Docker and start container.
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DockerExtension \
  --publisher Microsoft.Azure.Extensions \
  --version 1.1 \
  --settings '{"docker": {"port": "2375"},"compose": {"web": {"image": "nginx","ports": ["80:80"]}}}'
```

