---
title: Indexation du stockage de tables Azure avec Azure Search | Microsoft Docs
description: "Découvrez comment indexer les données stockées dans le stockage de tables Azure avec Azure Search"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/10/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 7679aa86aa24396d9cd7cf84a8cafe7950ad6d62
ms.lasthandoff: 04/20/2017

---

# <a name="index-azure-table-storage-with-azure-search"></a>Indexer le stockage de tables Azure avec Azure Search
Cet article montre comment utiliser Azure Search pour indexer les données stockées dans le stockage de tables Azure.

## <a name="set-up-azure-table-storage-indexing"></a>Configurer l’indexation du stockage de tables Azure

Vous pouvez configurer un indexeur de stockage de tables Azure à l’aide des ressources suivantes :

* [Portail Azure](https://ms.portal.azure.com)
* [API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) de la Recherche Azure
* [Kit de développement logiciel .NET (SDK)](https://aka.ms/search-sdk) de la Recherche Azure

Ici, nous vous présentons le flux à l’aide de l’API REST. 

### <a name="step-1-create-a-datasource"></a>Étape 1 : Création d’une source de données

Une source de données spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données et les stratégies qui permettent à Azure Search d’identifier efficacement les changements dans les données.

Pour l’indexation des tables, la source de données doit avoir les propriétés suivantes :

- **name** est le nom unique de la source de données au sein de votre service de recherche.
- **type** doit être `azuretable`.
- Le paramètre **credentials** contient la chaîne de connexion du compte de stockage. Pour plus d’informations, consultez la section [Spécifier des informations d’identification](#Credentials).
- **container** définit le nom de table et une requête facultative.
    - Spécifiez le nom de la table à l’aide du paramètre `name`.
    - Si vous le souhaitez, spécifiez une requête en utilisant le paramètre `query`. 

> [!IMPORTANT] 
> Si possible, utilisez un filtre sur PartitionKey pour de meilleures performances. Toute autre requête effectue une analyse complète, ce qui entraîne une dégradation des performances pour les grandes tables. Consultez la section [Considérations relatives aux performances](#Performance).


Pour créer une source de données :

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Pour plus d’informations sur l’API Créer une source de données, consultez [Créer une source de données](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Manières de spécifier des informations d’identification ####

Vous pouvez fournir les informations d’identification de la table de l’une des manières suivantes : 

- **Chaîne de connexion de compte de stockage avec accès complet** : `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Vous pouvez obtenir la chaîne de connexion sur le portail Azure en accédant au **panneau du compte de stockage** > **Paramètres** > **Clés** (pour les comptes de stockage Classic) ou **Paramètres** > **Clés d’accès** (pour les comptes de stockage ARM).
- **Chaîne de connexion de signature d’accès partagé au compte de stockage** : `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` La signature d’accès partagé doit disposer d’autorisations de liste et de lecture sur les conteneurs (tables dans le cas présent) et les objets (lignes de table).
-  **Signature d’accès partagé de table** : `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` la signature d’accès partagé doit disposer d’autorisations de requête (lecture) sur la table.

Pour plus d’informations sur les signatures d’accès partagé au stockage, consultez [Utilisation des signatures d’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Si vous utilisez des informations d’identification d’une signature d’accès partagé, vous devez mettre à jour les informations d’identification de la source de données régulièrement avec des signatures renouvelées afin d’éviter leur expiration. Si les informations d’identification d’une signature d’accès partagé expirent, l’indexeur échoue avec un message d’erreur tel que « Les informations d’identification fournies dans la chaîne de connexion sont invalides ou ont expiré. »  

### <a name="step-2-create-an-index"></a>Étape 2 : Création d’un index
L’index spécifie les champs d’un document, les attributs et d’autres constructions qui façonnent l’expérience de recherche.

Pour créer un index :

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Pour plus d’informations sur la création d’index, consultez [Création d’un index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>Étape 3 : Création d’un indexeur
Un indexeur connecte une source de données à un index de recherche cible et fournit une planification afin d’automatiser l’actualisation des données. 

Une fois l’index et la source de données créés, vous êtes prêt à créer l’indexeur :

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Cet indexeur s’exécute toutes les deux heures. (L’intervalle de planification est définie sur « PT2H ».) Pour exécuter un indexeur toutes les 30 minutes, définissez l’intervalle sur « PT30M ». Le plus court intervalle pris en charge est de 5 minutes. La planification est facultative : en cas d’omission, un indexeur ne s’exécute qu’une seule fois lorsqu’il est créé. Toutefois, vous pouvez à tout moment exécuter un indexeur à la demande.   

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Gérer différents noms de champs
Les noms de champ figurant dans votre index existant diffèrent parfois des noms de propriétés dans votre table. Dans ce cas, vous pouvez utiliser les mappages de champs pour mapper les noms de propriété de la table aux noms de champ de votre index de recherche. Pour en savoir plus sur les mappages de champs, consultez [Les mappages de champs de l’indexeur Azure Search comblent les différences entre les sources de données et les index de recherche](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Gérer les clés de document
Dans Azure Search, la clé de document identifie un document de manière unique. Chaque index de recherche doit comporter exactement un champ de clé de type `Edm.String`. Ce champ de clé est nécessaire pour chaque document ajouté à l’index (il constitue en fait le seul champ obligatoire).

Puisque les lignes d’une table ont une clé composée, Azure Search génère un champ synthétique appelé `Key` qui est une concaténation des valeurs de la clé de partition et de la clé de ligne. Par exemple, si la valeur PartitionKey d’une ligne est `PK1` et que RowKey est `RK1`, alors la valeur du champ `Key` est `PK1RK1`.

> [!NOTE]
> La valeur `Key` peut contenir des caractères non valides dans les clés de document, par exemple des tirets. Vous pouvez traiter les caractères non valides à l’aide de la `base64Encode` [fonction de mappage de champ](search-indexer-field-mappings.md#base64EncodeFunction). Si vous procédez ainsi, n’oubliez pas d’utiliser également l’encodage Base64 sécurisé pour les URL lorsque vous transmettez des clés de document dans des appels d’API tels que Recherche.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Indexation incrémentielle et détection des suppressions
Lorsque vous configurez un indexeur de table pour l’exécuter de manière planifiée, cet indexeur répertorie uniquement les lignes nouvelles ou mises à jour, comme le détermine la valeur `Timestamp` de la ligne. Vous n’êtes pas obligé de spécifier une stratégie de détection de modification. L’indexation incrémentielle est automatiquement activée pour vous.

Pour indiquer que certains documents doivent être supprimés de l’index, vous pouvez utiliser une stratégie de suppression réversible. Plutôt que de supprimer une ligne, ajoutez une propriété pour signaler sa suppression, puis configurez une stratégie de détection des suppressions réversibles sur la source de données. Par exemple, la stratégie suivante considère qu’une ligne est supprimée si elle a une propriété `IsDeleted` avec la valeur `"true"` :

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Considérations relatives aux performances

Par défaut, la Azure Search utilise le filtre de requête suivant : `Timestamp >= HighWaterMarkValue`. Dans la mesure où les tables Azure n’ont pas d’index secondaire sur le champ `Timestamp`, ce type de requête nécessite une analyse de table complète et est donc lente pour les tables volumineuses.


Voici deux approches possibles pour améliorer les performances d’indexation de table. Ces deux approches s’appuient sur l’utilisation de partitions de table : 

- Si vos données peuvent être partitionnées naturellement en plusieurs plages de partition, créez une source de données et un indexeur correspondant pour chaque plage. Maintenant, chaque indexeur n’a à traiter qu’une plage de partition spécifique, ce qui conduit à de meilleures performances pour les requêtes. Si les données à indexer ont un petit nombre de partitions fixes, c’est encore mieux : chaque indexeur procède uniquement à une analyse de partition. Par exemple, pour créer une source de données pour le traitement d’une plage de partition avec des clés de `000` à `100`, utilisez une requête semblable à celle-ci : 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Si vos données sont partitionnées par date (par exemple, si vous créez une nouvelle partition chaque jour ou chaque semaine), envisagez l’approche suivante : 
    - Utilisez une requête sous la forme : `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Surveillez la progression de l’indexeur avec [l’API Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) et mettez régulièrement à jour la condition `<TimeStamp>` de la requête sur la base de la dernière valeur de limite supérieure réussie. 
    - Avec cette approche, si vous avez besoin de déclencher une réindexation complète, vous devez réinitialiser la requête de source de données en plus de la réinitialisation de l’indexeur. 


## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer Azure Search
Si vous souhaitez nous soumettre des demandes d’ajout de fonctionnalités ou des idées d’amélioration, n’hésitez pas les proposer sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

