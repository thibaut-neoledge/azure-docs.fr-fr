<properties 
   pageTitle="Gérer Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Azure pour Node.js | Azure" 
   description="Découvrez comment gérer les comptes Data Lake Store et le système de fichiers." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Gérer Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Azure pour Node.js

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)


Le Kit de développement logiciel (SDK) Azure pour Node.js peut être utilisé pour gérer les comptes Azure Data Lake Store et le système de fichiers :

- Gestion de compte : créer, obtenir, répertorier, mettre à jour et supprimer.
- Gestion de système de fichiers : créer, obtenir, charger, ajouter, télécharger, lire, supprimer, répertorier.

**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Un compte Azure Data Lake Store**. Pour créer un compte, consultez [Prise en main d’Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md).
- **Un principal de service avec des autorisations d’accès au compte Data Lake Analytics**. Consultez [Authentification d’un principal du service à l’aide d’Azure Resource Manager](resource-group-authenticate-service-principal.md).

## Installer le Kit de développement logiciel (SDK)

Effectuez les étapes suivantes pour installer le Kit de développement logiciel (SDK) :

1. Installez [Node.js](https://nodejs.org/).
2. Exécutez les commandes suivantes à partir d’une invite de commandes, d’une fenêtre de terminal ou d’un script bash :

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-store
	
## Exemple Node.js

L’exemple suivant crée un fichier dans un compte Data Lake Store et y ajoute des données.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeStore = require('azure-arm-datalake-store');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adls_resourcegroup_name';
	
	var accountName = 'adls_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
	var destinationFilePath = '/newFileName.txt';
	var content = 'desired file contents';
	
	async.series([
		function (next) {
			context.acquireTokenWithClientCredentials(resourceUri, clientId, clientSecret, function(err, result){
				if (err) throw err;
				response = result;
				next();
			});
		},
		function (next) {
			var credentials = new azureCommon.TokenCloudCredentials({
				subscriptionId : subscriptionId,
				authorizationScheme : response.tokenType,
				token : response.accessToken
			});
		
			client = azureDataLakeStore.createDataLakeStoreFileSystemManagementClient(credentials, 'azuredatalakestore.net');
	
			next();
		},
		function (next) {
			client.fileSystem.directCreate(destinationFilePath, accountName, content, function(err, result){
				if (err) throw err;
			});
		}
	]);


##Voir aussi 

- [Kit de développement logiciel (SDK) Azure pour Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Gérer Azure Data Lake Analytics à l’aide de Node.js](data-lake-analytics-use-nodejs.md)

<!---HONumber=AcomDC_1217_2015-->