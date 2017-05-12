---
title: "Prendre en main Azure Data Lake Store à l’aide du Kit de développement Logiciel (SDK) Azure pour Node.js | Microsoft Docs"
description: "Découvrez comment utiliser Node.js pour travailler avec les comptes Data Lake Store et le système de fichiers."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 2fee173c-69ae-4e1d-8773-48618cda9e16
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: f33ccee7dd520adf074856616005c929040116dd
ms.openlocfilehash: 091ab246826c96b9d816c87b27014c1e54039429
ms.contentlocale: fr-fr
ms.lasthandoff: 02/01/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Prendre en main Azure Data Lake Store à l’aide du Kit de développement Logiciel (SDK) Azure pour Node.js
> [!div class="op_single_selector"]
> * [Portail](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interface de ligne de commande Azure](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

> [!NOTE]
> Pour charger et télécharger de grandes quantités de données (des fichiers volumineux, un grand nombre de fichiers ou les deux), nous vous recommandons d’utiliser le [SDK Python](data-lake-store-get-started-python.md), le [SDK .NET](data-lake-store-get-started-net-sdk.md) ou [Azure PowerShell](data-lake-store-get-started-powershell.md). Ces options offrent de meilleures performances, car elles utilisent plusieurs threads pour paralléliser le déplacement des données.
> 
> 

Apprenez à utiliser le Kit de développement logiciel (SDK) Azure pour Node.js pour créer un compte Azure Data Lake Store et effectuer des opérations de base comme créer des dossiers, télécharger des fichiers de données, supprimer votre compte, etc. Pour plus d’informations sur Data Lake Store, consultez [Vue d’ensemble de Data Lake Store](data-lake-store-overview.md). Le Kit de développement logiciel (SDK) prend actuellement en charge

* **Node.js version 0.10.0 ou supérieure**
* **Version de l’API REST pour le compte : 2015-10-01-preview**
* **Version de l’API REST pour FileSystem : 2015-10-01-preview**

## <a name="prerequisites"></a>Composants requis
Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Créez une application Azure Active Directory**. Vous utilisez l’application Azure AD pour authentifier l’application Data Lake Store auprès d’Azure AD. Il existe différentes approches pour l’authentification auprès d’Azure AD : **authentification de l’utilisateur final** ou **authentification de service à service**. Pour plus d’informations sur l’authentification et la procédure associée, consultez [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)(Authentification auprès de Data Lake Store à l’aide d’Azure Active Directory).

## <a name="how-to-install"></a>Procédure d’installation
```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>S’authentifier à l’aide d’Azure Active Directory
Les extraits de code ci-dessous montrent deux méthodes distinctes d’authentification sur Data Lake Store à l’aide d’Azure AD. Pour une présentation détaillée des différentes méthodes d’authentification sur Data Lake Store, voir [S’authentifier sur Data Lake Store à l’aide d’Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

L’extrait de code ci-dessous requiert également des entrées telles qu’un nom de domaine Azure AD, un ID de client pour une application Azure AD, etc. Toutes ces informations peuvent être extraites d’une application Azure AD que vous devez créer, dont les détails sont également inclus dans le lien ci-dessus.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Créer les clients Data Lake Store
```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Créer un compte Data Lake Store
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

## <a name="create-a-file-with-content"></a>Créez un fichier avec du contenu
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

## <a name="get-a-list-of-files-and-folders"></a>Obtenir une liste de fichiers et de dossiers
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

## <a name="see-also"></a>Voir aussi
* [Kit de développement logiciel (SDK) Microsoft Azure pour Node.js](https://github.com/azure/azure-sdk-for-node)
* [Kit de développement logiciel (SDK) Microsoft Azure pour Node.js - Gestion de Data Lake Analytics](https://www.npmjs.com/package/azure-arm-datalake-analytics)


