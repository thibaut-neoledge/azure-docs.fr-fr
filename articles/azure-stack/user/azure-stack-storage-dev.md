---
title: "Bien démarrer avec les outils de développement du stockage Azure Stack"
description: "Guide de démarrage pour l’utilisation des outils de développement du stockage Azure Stack"
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 9/25/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.openlocfilehash: 5b2898c64c0f1b5d804e63fa4e4e1218fa7a672c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Bien démarrer avec les outils de développement du stockage Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*


Microsoft Azure Stack fournit un ensemble de services de stockage, notamment le stockage Blob, Table et File d’attente Azure.

Cet article fournit des conseils rapides sur l’utilisation des outils de développement du stockage Azure Stack. Des informations plus détaillées et des exemples de code sont disponibles dans les didacticiels correspondants du stockage Azure.

Le stockage Azure et le stockage Azure Stack présentent quelques différences connues, y compris certaines exigences spécifiques pour chaque plateforme. Par exemple, Azure Stack a des bibliothèques clientes spécifiques, de même que des exigences spécifiques en matière de suffixe de point de terminaison. Pour plus d’informations, consultez [Stockage Azure Stack : Différences et considérations](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Bibliothèques clientes Azure
La version d’API REST prise en charge pour le stockage Azure Stack est 2015-04-05. Elle n’a pas de parité complète avec la dernière version de l’API REST du stockage Azure. Par conséquent, pour les bibliothèques clientes de stockage, vous devez connaître la version compatible avec l’API REST 2015-04-05.


|Bibliothèque cliente|Version prise en charge par Azure Stack|Lien|Spécification du point de terminaison|
|---------|---------|---------|---------|
|.NET     |6.2.0|Package NuGet :<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Version de GitHub :<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|Fichier app.config|
|Java|4.1.0|Package Maven :<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Version de GitHub :<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Configuration de la chaîne de connexion|
|Node.js     |1.1.0|Lien NPM :<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(exécuter : `npm install azure-storage@1.1.0)`<br><br>Version de GitHub :<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Déclaration d’instance de service||C++|2.4.0|Package NuGet :<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Version de GitHub :<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuration de la chaîne de connexion|
|C++|2.4.0|Package NuGet :<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Version de GitHub :<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuration de la chaîne de connexion|
|PHP|0.15.0|Version de GitHub :<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Installation via Composer (voir détails ci-dessous)|Configuration de la chaîne de connexion|
|Python     |0.30.0|Package PIP :<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Exécuter : `pip install -v azure-storage==0.30.0)`<br><br>Version de GitHub :<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Déclaration d’instance de service|
|Ruby|0.12.1<br>VERSION PRÉLIMINAIRE|Package RubyGems :<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Version de GitHub :<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Configuration de la chaîne de connexion|

> [!NOTE]
> Détails PHP<br><br>
>Pour installer via Composer :
>1. Créez un fichier nommé `composer.json` à la racine du projet avec le code suivant :<br>
>
>   ```
>   {
>       "require":{
>           "Microsoft/azure-storage":"0.15.0"
>        }
>    }
>   ```
>
>2. Téléchargez [composer.phar](http://getcomposer.org/composer.phar) à la racine du projet.
>3. Exécutez : `php composer.phar install`.
>


## <a name="endpoint-declaration"></a>Déclaration de point de terminaison
Un point de terminaison Azure Stack comprend deux parties : le nom d’une région et le domaine Azure Stack.
Dans le Kit de développement Azure Stack, le point de terminaison par défaut est **local.azurestack.external**.
Contactez votre administrateur cloud si vous n’êtes pas sûr de votre point de terminaison.

## <a name="examples"></a>Exemples


### <a name="net"></a>.NET

Pour Azure Stack, le suffixe de point de terminaison est spécifié dans le fichier app.config :

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```
### <a name="java"></a>Java

Pour Azure Stack, le suffixe de point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Pour Azure Stack, le suffixe de point de terminaison est spécifié dans l’instance de déclaration :

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```
### <a name="c"></a>C++

Pour Azure Stack, le suffixe de point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Pour Azure Stack, le suffixe de point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Pour Azure Stack, le suffixe de point de terminaison est spécifié dans l’instance de déclaration :

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```
### <a name="ruby"></a>Ruby

Pour Azure Stack, le suffixe de point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Stockage d'objets blob

Les didacticiels du stockage Blob Azure suivants sont applicables à Azure Stack. Notez l’exigence particulière d’un suffixe de point de terminaison pour Azure Stack, décrit dans la précédente section [Exemples](#examples).

* [Prise en main d’Azure Blob Storage à l’aide de .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Utilisation du stockage d'objets blob à partir de Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Utilisation du stockage d’objets Blob à partir de Node.js]../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Utilisation du stockage d’objets blob à partir de C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Utilisation du stockage d'objets blob à partir de PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Utilisation du stockage Blob Azure à partir de Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Utilisation du stockage d'objets blob à partir de Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Stockage de files d'attente

Les didacticiels du stockage File d’attente Azure suivants sont applicables à Azure Stack. Notez l’exigence particulière d’un suffixe de point de terminaison pour Azure Stack, décrit dans la précédente section [Exemples](#examples).

* [Prise en main du stockage de files d’attente Azure à l’aide de .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Utilisation du stockage de files d'attente à partir de Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Utilisation du stockage de files d'attente à partir de Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Utilisation du service de stockage de files d’attente à partir de C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Utilisation du stockage de files d'attente à partir de PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Utilisation du stockage de files d'attente à partir de Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Utilisation du stockage de files d'attente à partir de Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)


## <a name="table-storage"></a>Stockage de tables

Les didacticiels du stockage Table Azure suivants sont applicables à Azure Stack. Notez l’exigence particulière d’un suffixe de point de terminaison pour Azure Stack, décrit dans la précédente section [Exemples](#examples).

* [Prise en main d’Azure Table Storage à l’aide de .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Utilisation du stockage de tables à partir de Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Utilisation du stockage Table Azure à partir de Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Utilisation du stockage Table à partir de C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Utilisation du stockage de tables à partir de PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Utilisation du stockage Table dans Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Utilisation du stockage de tables à partir de Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Étapes suivantes

* [Introduction à Microsoft Azure Storage](../../storage/common/storage-introduction.md)