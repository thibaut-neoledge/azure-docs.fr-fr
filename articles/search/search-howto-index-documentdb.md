---
title: "Indexation d’une source de données Cosmos DB pour Recherche Azure | Microsoft Docs"
description: "Cet article vous indique comment créer un indexeur Recherche Azure avec Cosmos DB en tant que source de données."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 08/10/2017
ms.author: eugenesh
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 2f1791393b1e59721cc5a1030927cd00d74a5f13
ms.contentlocale: fr-fr
ms.lasthandoff: 08/10/2017

---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Connexion de Cosmos DB à Recherche Azure à l’aide d’indexeurs

Si vous souhaitez mettre en place une excellente expérience de recherche sur vos données Cosmos DB, vous pouvez utiliser un indexeur Recherche Azure pour extraire les données et les placer dans un index Recherche Azure. Dans cet article, nous allons vous montrer comment intégrer Recherche Azure dans Azure Cosmos DB sans avoir à écrire le moindre code pour mettre l’infrastructure d’indexation à jour.

Pour configurer un indexeur Cosmos DB, vous avez besoin d’un [Service Recherche Azure](search-create-service-portal.md) et vous devez créer un index, une source de données et enfin l’indexeur. Vous pouvez créer ces objets à l’aide du [portail](search-import-data-portal.md), du [Kit de développement logiciel (SDK) .NET](/dotnet/api/microsoft.azure.search) ou de [l’API REST](/rest/api/searchservice/) pour tous les langages autres que .NET. 

Si vous optez pour le portail, [l’Assistant Importation de données](search-import-data-portal.md) vous guide dans la création de toutes ces ressources.

> [!NOTE]
> Cosmos DB est la nouvelle génération de DocumentDB. Bien que le nom du produit ait été modifié, la syntaxe reste identique. Continuez à spécifier `documentdb` comme indiqué dans cet article sur l’indexeur. 

> [!TIP]
> Vous pouvez lancer l’Assistant **Importation de données** sur le tableau de bord Cosmos DB afin de simplifier l’indexation de cette source de données. Dans la navigation de gauche, accédez à **Collections** > **Ajouter la Recherche Azure** pour commencer.

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Concepts d’indexeur Azure Search
Recherche Azure prend en charge la création et la gestion de sources de données (dont Cosmos DB) et d’indexeurs qui fonctionnent en s’appuyant sur ces dernières.

Une **source de données** spécifie les données à indexer, les informations d’identification et les stratégies pour identifier les modifications des données (par exemple, les documents modifiés ou supprimés dans votre collection). La source de données est définie en tant que ressource indépendante de manière à pouvoir être utilisée par plusieurs indexeurs.

Un **indexeur** décrit le flux de données de votre source de données vers un index de recherche cible. Un indexeur peut servir à :

* effectuer une copie unique des données pour remplir un index ;
* synchroniser un index avec les modifications apportées à la source de données selon une planification. La planification fait partie de la définition de l'indexeur ;
* Appeler des mises à jour d'un index à la demande en fonction des besoins.

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Étape 1 : Création d’une source de données
Pour créer une source de données, effectuez un POST :

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Le corps de la requête contient la définition de la source de données, qui doit inclure les champs suivants :

* **nom** : choisissez un nom pour représenter votre base de données Cosmos DB.
* **type** : doit être `documentdb`.
* **credentials**:
  
  * **connectionString**: obligatoire. Indiquez les informations de connexion à votre base de données Azure Cosmos DB au format suivant : `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **container**:
  
  * **name**: obligatoire. Spécifiez l’ID de la collection Cosmos DB à indexer.
  * **query**: facultatif. Vous pouvez spécifier une requête pour obtenir un schéma plat à partir d'un document JSON arbitraire de manière à ce qu'Azure Search puisse procéder à l'indexation.
* **dataChangeDetectionPolicy** : recommandé. Consultez la section [Indexation des documents modifiés](#DataChangeDetectionPolicy).
* **dataDeletionDetectionPolicy**: facultatif. Consultez la section [Indexation des documents supprimés](#DataDeletionDetectionPolicy).

### <a name="using-queries-to-shape-indexed-data"></a>Utilisation de requêtes pour formater les données indexées
Vous pouvez spécifier une requête Cosmos DB pour aplatir les propriétés imbriquées ou tableaux, projeter des propriétés JSON et filtrer les données à indexer. 

Exemple de document :

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Requête de filtre :

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Requête d’aplatissage :

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Requête de projection :

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Requête d’aplatissage de tableau :

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Étape 2 : Création d’un index
Créez un index Azure Search cible si vous n'en possédez pas déjà un. Vous pouvez créer un index avec [l’interface utilisateur du portail Azure](search-create-index-portal.md), [l’API Création d’index](/rest/api/searchservice/create-index) ou la [classe Index](/dotnet/api/microsoft.azure.search.models.index).

L'exemple suivant crée un index avec un champ ID et un champ Description :

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Assurez-vous que le schéma de votre index cible est compatible avec le schéma des documents JSON source ou la sortie de votre projection de requête personnalisée.

> [!NOTE]
> Pour les collections partitionnées, la clé de document par défaut est la propriété `_rid` de Cosmos DB, qui est renommée en `rid` dans Recherche Azure. De même, les valeurs `_rid` de Cosmos DB contiennent des caractères qui ne sont pas valides dans les clés de Recherche Azure. Par conséquent, les valeurs `_rid` sont codées en Base64.
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mappage entre les types de données JSON et les types de données Azure Search
| TYPE DE DONNÉES JSON | TYPES DE CHAMPS D’INDEX CIBLE COMPATIBLES |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Nombres qui ressemblent à des nombres entiers |Edm.Int32, Edm.Int64, Edm.String |
| Nombres qui ressemblent à des nombres avec points flottants |Edm.Double, Edm.String |
| String |Edm.String |
| Tableaux de types primitifs, par exemple ["a", "b", "c"] |Collection(Edm.String) |
| Chaînes qui ressemblent à des dates |Edm.DateTimeOffset, Edm.String |
| Objets GeoJSON, par exemple { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Autres objets JSON |N/A |

<a name="CreateIndexer"></a>
## <a name="step-3-create-an-indexer"></a>Étape 3 : Création d’un indexeur

Une fois l'index et la source de données créés, vous êtes prêt à créer l’indexeur :

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Cet indexeur s’exécute toutes les deux heures (intervalle de planification défini sur « PT2H »). Pour exécuter un indexeur toutes les 30 minutes, définissez l’intervalle sur « PT30M ». Le plus court intervalle pris en charge est de 5 minutes. La planification est facultative : en cas d’omission, un indexeur ne s’exécute qu’une seule fois lorsqu’il est créé. Toutefois, vous pouvez à tout moment exécuter un indexeur à la demande.   

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Exécution de l’indexeur à la demande
En plus de l'exécution périodique planifiée, un indexeur peut également être appelé à la demande :

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> Lors de l’API s’exécute avec succès, l’appel de l’indexeur a été planifié, mais le traitement réel se produit de façon asynchrone. 

Vous pouvez surveiller l’état de l’indexeur dans le portail ou à l’aide de l’API Get Indexer Status, que nous décrivons par la suite. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Obtention de l’état de l’indexeur
Vous pouvez récupérer l'historique d'état et d'exécution d'un indexeur :

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

La réponse contient l'état d'intégrité global de l'indexeur, le dernier appel de l'indexeur (ou celui en cours), ainsi que l'historique des appels récents de l'indexeur.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

L'historique d'exécution contient les 50 exécutions les plus récentes, classées par ordre chronologique inverse (la dernière exécution est répertoriée en premier dans la réponse).

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Indexation des documents modifiés
L'objectif d'une stratégie de détection des changements de données est d'identifier efficacement les données modifiées. La seule stratégie actuellement prise en charge est la stratégie `High Water Mark` qui utilise la propriété `_ts` (horodatage) fournie par Cosmos DB, spécifiée comme suit :

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Cette stratégie est vivement recommandée pour garantir de bonnes performances pour l’indexeur. 

Si vous utilisez une requête personnalisée, assurez-vous que la propriété `_ts` est projetée par la requête.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Progression incrémentielle et requêtes personnalisées
Dans le cas où l’exécution de l’indexeur est interrompue par des échecs passagers ou un dépassement du délai d’exécution, une progression incrémentielle pendant une indexation veille à ce que l’indexeur puisse reprendre là il en était lors de sa dernière exécution, afin de ne pas avoir à tout réindexer depuis le début. Ceci est particulièrement important lors de l’indexation de grandes collections. 

Pour activer la progression incrémentielle lors de l’utilisation d’une requête personnalisée, assurez-vous que votre requête classe les résultats par la colonne `_ts`. Ceci permet de créer des points de contrôle périodiques dont Azure Search se sert pour proposer la progression incrémentielle en cas d’erreurs.   

Dans certains cas, il se peut qu’Azure Search ne déduise pas que la requête est ordonnée par `_ts`, même si elle contient une clause `ORDER BY [collection alias]._ts`. Vous pouvez indiquer à Azure Search que les résultats sont triés à l’aide de la propriété de configuration `assumeOrderByHighWaterMarkColumn`. Pour ce faire, créez ou mettez à jour l’indexeur comme suit : 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Indexation des documents supprimés
Lorsque des lignes sont supprimées de la collection, vous devez normalement supprimer ces lignes de l'index de recherche. L'objectif d'une stratégie de détection des suppressions de données est d'identifier efficacement les éléments de données supprimés. La seule stratégie actuellement prise en charge est la stratégie `Soft Delete` (où la suppression est signalée par un indicateur quelconque), spécifiée comme suit :

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Si vous utilisez une requête personnalisée, assurez-vous que la propriété référencée par `softDeleteColumnName` est projetée par la requête.

L'exemple suivant crée une source de données avec des conseils pour une stratégie de suppression en douceur :

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Étapes suivantes
Félicitations ! Vous avez appris à intégrer Recherche Azure dans Azure Cosmos DB à l’aide de l’indexeur pour Cosmos DB.

* Pour en savoir plus sur Azure Cosmos DB, consultez la [page du service Cosmos DB](https://azure.microsoft.com/services/documentdb/).
* Pour en savoir plus sur Azure Search, consultez la [page du service Search](https://azure.microsoft.com/services/search/).

