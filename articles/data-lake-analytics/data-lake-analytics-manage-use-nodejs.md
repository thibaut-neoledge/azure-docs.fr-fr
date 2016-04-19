<properties
   pageTitle="Gestion d’Azure Data Lake Analytics à l’aide du kit de développement logiciel (SDK) Azure pour Node.js | Azure"
   description="Apprenez à gérer des comptes Data Lake Analytics, des sources de données, des utilisateurs et des tâches à l’aide du kit de développement logiciel Azure pour Node.js"
   services="data-lake-analytics"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Gestion d’Azure Data Lake Analytics à l’aide du kit de développement logiciel (SDK) Azure pour Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Le Kit de développement d’application (ADK) Azure pour Node.js peut être utilisé pour gérer les comptes Azure Data Lake Analytics, les tâches et les catalogues. Pour afficher la rubrique de gestion à l'aide d'autres outils, cliquez sur l'onglet de sélection ci-dessus.

**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Un compte Azure Data Lake Analytics**. Pour créer un compte, consultez [Prise en main d’Azure Data Lake Analytics avec le portail Azure](data-lake-analytics-get-started-portal.md).
- **Un principal du service avec des autorisations d’accès au compte Data Lake Analytics**. Consultez [Authentification d’un principal du service à l’aide d’Azure Resource Manager](../resource-group-authenticate-service-principal.md).

## Installer le Kit de développement logiciel (SDK)

Suivez les étapes suivantes pour installer le Kit de développement logiciel (SDK) :

1. Installez [Node.js](https://nodejs.org/).
2. Exécutez les commandes suivantes à partir d’une invite de commandes, d’une fenêtre de terminal ou d’un script bash :

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-analytics

## Exemple Node.js

L’exemple suivant obtient la liste des tâches pour un compte Azure Data Lake Analytics donné.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeAnalytics = require('azure-arm-datalake-analytics');

	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'

	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';

	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adla_resourcegroup_name';

	var accountName = 'adla_account_name';

	var context = new adalNode.AuthenticationContext(loginUri+tenantId);

	var client;
	var response;

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

			client = azureDataLakeAnalytics.createDataLakeAnalyticsJobManagementClient(credentials, 'azuredatalakeanalytics.net');

			next();
		},
		function (next) {
			client.jobs.list(resourceGroup, accountName, function(err, result){
				if (err) throw err;
				console.log(result);
			});
		}
	]);


##Voir aussi

- [Kit de développement logiciel (SDK) Azure pour Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Prise en main des analyses Data Lake à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
- [Gérer les analyses Azure Data Lake à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
- [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0413_2016-->