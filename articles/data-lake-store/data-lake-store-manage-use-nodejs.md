<properties 
   pageTitle="Gérer des magasins Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Azure pour Node.js | Microsoft Azure"
   description="Découvrez comment gérer les comptes Data Lake Store et le système de fichiers." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# Gérer Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Azure pour Node.js

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
- [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Interface de ligne de commande Azure](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)


Le Kit de développement logiciel (SDK) Azure pour Node.js permet de gérer les comptes Azure Data Lake Store ainsi que les opérations sur le système de fichiers :

Pour le moment, il prend en charge :

  *  **Node.js version 0.10.0 ou supérieure**
  *  **Version de l’API REST pour le compte : 2015-10-01-preview**
  *  **Version de l’API REST pour FileSystem : 2015-10-01-preview**

##Composants requis

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

## Caractéristiques

- Gestion de compte : créer, obtenir, répertorier, mettre à jour et supprimer.
- Gestion de système de fichiers : créer, obtenir, charger, ajouter, télécharger, lire, supprimer, répertorier.

## Procédure d’installation

```bash
npm install azure-arm-datalake-store
```

## S’authentifier à l’aide d’Azure Active Directory

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## Créer les clients Data Lake Analytics

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## Créer un compte Data Lake Store

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Créez un fichier avec du contenu
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## Obtenir une liste de fichiers et de dossiers

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Voir aussi

- [Kit de développement logiciel (SDK) Microsoft Azure pour Node.js](https://github.com/azure/azure-sdk-for-node)
- [Kit de développement logiciel (SDK) Microsoft Azure pour Node.js - Gestion de Data Lake Analytics](https://www.npmjs.com/package/azure-arm-datalake-analytics)

<!---HONumber=AcomDC_0914_2016-->