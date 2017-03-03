---
title: "Développement avec plusieurs régions dans DocumentDB | Microsoft Docs"
description: "Découvrez comment accéder aux données dans plusieurs régions Azure DocumentDB, un service de base de données NoSQL entièrement géré."
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: d4579378-0b3a-44a5-9f5b-630f1fa4c66d
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 240f45383639f1eb8a4a9b7c4e4489591be23dba
ms.openlocfilehash: aac8c26cd9af94659abc80b2ae260dc380cf38c9
ms.lasthandoff: 02/10/2017


---
# <a name="developing-with-multi-region-documentdb-accounts"></a>Développement avec des comptes DocumentDB à plusieurs régions

Découvrez les comptes DocumentDB à plusieurs régions dans cette vidéo Azure Friday avec Scott Hanselman et Karthik Raman, responsable principal de l’ingénierie DocumentDB.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a name="introduction"></a>Introduction

Pour tirer parti de la [distribution mondiale](documentdb-distribute-data-globally.md), les applications clientes peuvent spécifier la liste ordonnée de préférences de régions à utiliser pour effectuer des opérations sur les documents. Pour cela, vous devez configurer la stratégie de connexion. Selon la configuration du compte Azure DocumentDB, la disponibilité régionale actuelle et la liste de préférences spécifiée, le Kit de développement logiciel (SDK) choisit le point de terminaison optimal pour les opérations de lecture et d’écriture.

Cette liste de préférences est spécifiée lors de l’initialisation d’une connexion en utilisant les SDK clients. Les SDK acceptent un paramètre facultatif « PreferredLocations » qui est une liste ordonnée des régions Azure.

Le SDK envoie automatiquement toutes les écritures vers la région d’écriture en cours.

Toutes les lectures sont envoyées vers la première région disponible dans la liste PreferredLocations. Si la demande échoue, le client passe à la région suivante dans la liste et ainsi de suite.

Les SDK clients tenteront seulement de lire à partir des régions spécifiées dans PreferredLocations. Ainsi, par exemple, si le compte de base de données est disponible dans trois régions, mais que le client spécifie uniquement deux des régions sans écriture de PreferredLocations, aucune lecture n’est traitée hors de la région d’écriture, même en cas de basculement.

L’application peut vérifier le point de terminaison d’écriture et le point de terminaison de lecture actuels choisis par le SDK en vérifiant deux propriétés, WriteEndpoint et ReadEndpoint, disponibles dans le SDK version 1.8 et ultérieure.

Si la propriété PreferredLocations n’est pas définie, toutes les demandes seront traitées par la zone d’écriture en cours.

## <a name="net-sdk"></a>Kit de développement logiciel (SDK) .NET
Le SDK peut être utilisé sans aucune modification du code. Dans ce cas, le SDK dirige automatiquement les lectures et les écritures vers la région d’écriture en cours.

Dans la version 1.8 et ultérieure du SDK .NET, le paramètre ConnectionPolicy du constructeur DocumentClient comporte une propriété appelée Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Cette propriété est de type Collection `<string>` et doit contenir une liste des noms de région. Les valeurs de chaîne sont mises en forme par la colonne Nom de la région, sur la page [Régions Azure][regions], sans espaces avant ou après le premier et le dernier caractère, respectivement.

Les points de terminaison d’écriture et de lecture en cours sont disponibles dans DocumentClient.WriteEndpoint et DocumentClient.ReadEndpoint, respectivement.

> [!NOTE]
> Les URL des points de terminaison ne doivent pas être considérées comme des constantes à long terme. Le service peut les mettre à jour à tout moment. Le SDK gère ce changement automatiquement.
>
>

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
    
    ConnectionPolicy connectionPolicy = new ConnectionPolicy();

    //Setting read region selection preference
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>SDK Node.js, JavaScript et Python
Le SDK peut être utilisé sans aucune modification du code. Dans ce cas, le SDK dirige automatiquement les lectures et les écritures vers la région d’écriture en cours.

Dans la version 1.8 et ultérieure de chaque SDK, le paramètre ConnectionPolicy du constructeur DocumentClient comporte une nouvelle propriété appelée DocumentClient.ConnectionPolicy.PreferredLocations. Ce paramètre est un tableau de chaînes qui prend une liste de noms de régions. Les noms sont mis en forme en fonction de la colonne Nom de la région, sur la page [Régions Azure][regions]. Vous pouvez également utiliser des constantes prédéfinies dans l’objet de commodité AzureDocuments.Regions

Les points de terminaison d’écriture et de lecture en cours sont disponibles dans DocumentClient.getWriteEndpoint et DocumentClient.getReadEndpoint, respectivement.

> [!NOTE]
> Les URL des points de terminaison ne doivent pas être considérées comme des constantes à long terme. Le service peut les mettre à jour à tout moment. Le SDK gère ce changement automatiquement.
>
>

Voici un exemple de code pour NodeJS/Javascript. Python et Java suivent le même modèle.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();

    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>REST
Une fois qu’un compte de base de données est mis à disposition dans plusieurs régions, les clients peuvent interroger sa disponibilité en exécutant une requête GET sur l’URI suivant.

    https://{databaseaccount}.documents.azure.com/

Le service renvoit une liste des régions et leurs URI de points de terminaison DocumentDB correspondants pour les réplicas. La région d’écriture en cours est indiquée dans la réponse. Le client peut ensuite sélectionner le point de terminaison approprié pour toutes les autres requêtes d’API REST, comme suit.

Exemple de réponse

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Les requêtes PUT, POST et DELETE doivent accéder à l’URI d’écriture indiqué
* Toutes les requêtes GET et autres demandes en lecture seule (par ex., Requêtes) peuvent accéder à n’importe quel point de terminaison choisi par le client

L’écriture de demandes dans les régions en lecture seule échoue avec le code d’erreur HTTP 403 (« Interdit »).

Si la région d’écriture change après la phase de découverte initiale du client, les écritures suivantes dans la région d’écriture précédente échouent avec le code d’erreur HTTP 403 (« Interdit »). Le client doit alors exécuter à nouveau la requête GET sur la liste des régions pour obtenir la région d’écriture mise à jour.

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en plus sur la distribution mondiale de données avec DocumentDB dans les articles suivants :

* [Distribution mondiale des données avec DocumentDB](documentdb-distribute-data-globally.md)
* [Niveaux de cohérence](documentdb-consistency-levels.md)
* [Ajout de régions à l’aide du Portail Azure](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/

