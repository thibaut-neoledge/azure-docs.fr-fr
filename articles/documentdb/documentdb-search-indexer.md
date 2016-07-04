<properties
    pageTitle="Connexion de DocumentDB à Azure Search à l'aide d'indexeurs | Microsoft Azure"
    description="Cet article vous indique comment utiliser l'indexeur Azure Search avec DocumentDB en tant que source de données."
    services="documentdb"
    documentationCenter=""
    authors="AndrewHoh"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="rest-api"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="06/20/2016"
    ms.author="anhoh"/>

#Connexion de DocumentDB à Azure Search à l'aide d'indexeurs

Si vous souhaitez améliorer les recherches au niveau de vos données DocumentDB, utilisez l'indexeur Azure Search pour DocumentDB. Dans cet article, nous allons vous montrer comment intégrer Azure Search à Azure DocumentDB sans avoir à écrire le moindre code pour mettre l'infrastructure d'indexation à jour.

Pour ce faire, vous devez [configurer un compte Azure Search](../search/search-create-service-portal.md) (vous n’avez pas besoin de procéder à la mise à niveau vers la recherche standard), puis appeler l’[API REST Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour créer une **source de données** DocumentDB et un **indexeur** pour cette dernière.

Pour envoyer des demandes afin d’interagir avec les API REST, vous pouvez utiliser [Postman](https://www.getpostman.com/), [Fiddler](http://www.telerik.com/fiddler), ou tout autre outil de votre choix.


##<a id="Concepts"></a>Concepts d’indexeur Azure Search

Azure Search prend en charge la création et la gestion de sources de données (dont DocumentDB) et d'indexeurs qui fonctionnent en s'appuyant sur ces dernières.

Une **source de données** définit les données à indexer, les informations d’identification pour accéder aux données et les stratégies pour permettre à Azure Search d’identifier efficacement les modifications au niveau des données (des documents modifiés ou supprimés dans votre collection, par exemple). La source de données est définie en tant que ressource indépendante de manière à pouvoir être utilisée par plusieurs indexeurs.

Un **indexeur** décrit le flux de données de votre source de données vers un index de recherche cible. Vous devez prévoir de créer un indexeur pour chaque association source de données/index cible. Plusieurs indexeurs peuvent écrire au niveau du même index, un indexeur ne peut cependant écrire que dans un seul index. Les indexeurs sont utilisés pour :

- effectuer une copie unique des données pour remplir un index ;
- synchroniser un index avec les modifications apportées à la source de données selon une planification. La planification fait partie de la définition de l'indexeur ;
- Appeler des mises à jour d'un index à la demande en fonction des besoins.

##<a id="CreateDataSource"></a>Étape 1 : Création d’une source de données

Émettez une demande HTTP POST pour créer une source de données dans votre service Azure Search avec les en-têtes de requête suivants.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Le paramètre `api-version` est obligatoire. Les valeurs valables incluent `2015-02-28` ou une version ultérieure. Consultez [Versions d’API dans Azure Search](../search/search-api-versions.md) pour voir toutes les versions d’API prises en charge dans Search.

Le corps de la requête contient la définition de la source de données, qui doit inclure les champs suivants :

- **nom** : choisissez un nom pour représenter votre base de données DocumentDB.

- **type** : utilisez `documentdb`.

- **credentials** :

    - **connectionString** : obligatoire. Indiquez les informations de connexion à votre base de données Azure DocumentDB au format suivant : `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **container** :

    - **name** : obligatoire. Spécifiez l’ID de la collection DocumentDB à indexer.

    - **query** : facultatif. Vous pouvez spécifier une requête pour obtenir un schéma plat à partir d'un document JSON arbitraire de manière à ce qu'Azure Search puisse procéder à l'indexation.

- **dataChangeDetectionPolicy** : facultatif. Consultez la section [Stratégie de détection des changements de données](#DataChangeDetectionPolicy) ci-dessous.

- **dataDeletionDetectionPolicy** : facultatif. Consultez la section [Stratégie de détection des suppressions de données](#DataDeletionDetectionPolicy) ci-dessous.

Voir ci-dessous pour un [exemple de corps de la demande](#CreateDataSourceExample).

###<a id="DataChangeDetectionPolicy"></a>Capture des documents modifiés

L'objectif d'une stratégie de détection des changements de données est d'identifier efficacement les données modifiées. La seule stratégie actuellement prise en charge est la stratégie `High Water Mark` qui utilise la propriété d’horodatage des dernières modifications `_ts` fournie par DocumentDB, spécifiée comme suit :

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Vous devez également ajouter `_ts` à la projection et à la clause `WHERE` de votre requête. Par exemple :

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Capture des documents supprimés

Lorsque des lignes sont supprimées de la table source, vous devez également supprimer ces lignes de l'index de recherche. L'objectif d'une stratégie de détection des suppressions de données est d'identifier efficacement les éléments de données supprimés. La seule stratégie actuellement prise en charge est la stratégie `Soft Delete` (où la suppression est signalée par un indicateur quelconque), spécifiée comme suit :

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [AZURE.NOTE] Vous devez inclure la propriété softDeleteColumnName à votre clause SELECT si vous utilisez une projection personnalisée.

###<a id="CreateDataSourceExample"></a>Exemple de corps de requête

L'exemple suivant crée une source de données avec des conseils pour une stratégie et une requête personnalisées :

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
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

###Réponse

Vous recevrez une réponse HTTP 201 Créé si la source de données a été créée avec succès.

##<a id="CreateIndex"></a>Étape 2 : Création d’un index

Créez un index Azure Search cible si vous n'en possédez pas déjà un. Pour ce faire, vous pouvez utiliser l’[interface utilisateur du portail Azure](../search/search-create-index-portal.md) ou l’[API Création d’index](https://msdn.microsoft.com/library/azure/dn798941.aspx).

	POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
	Content-Type: application/json
	api-key: [Search service admin key]


Assurez-vous que le schéma de votre index cible est compatible avec le schéma des documents JSON source ou la sortie de votre projection de requête personnalisée.

###Figure A : Mappage entre les types de données JSON et les types de données Azure Search

| TYPE DE DONNÉES JSON|	TYPES DE CHAMPS D’INDEX CIBLE COMPATIBLES|
|---|---|
|Bool|Edm.Boolean, Edm.String|
|Nombres qui ressemblent à des nombres entiers|Edm.Int32, Edm.Int64, Edm.String|
|Nombres qui ressemblent à des nombres avec points flottants|Edm.Double, Edm.String|
|String|Edm.String|
|Tableaux de types primitifs, par exemple « a », « b », « c » |Collection(Edm.String)|
|Chaînes qui ressemblent à des dates| Edm.DateTimeOffset, Edm.String|
|Objets GeoJSON, par exemple { "type": "Point", "coordinates": [ long, lat ] } | Edm.GeographyPoint |
|Autres objets JSON|N/A|

###<a id="CreateIndexExample"></a>Exemple de corps de requête

L'exemple suivant crée un index avec un champ ID et un champ Description :

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

###Réponse

Vous recevrez une réponse HTTP 201 Créé si l'index a été créé avec succès.

##<a id="CreateIndexer"></a>Étape 3 : Création d’un indexeur

Vous pouvez créer un indexeur au sein d'un service Azure Search en utilisant une requête HTTP POST avec les en-têtes suivants.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Le corps de la requête contient la définition de l'indexeur, qui doit inclure les champs suivants :

- **name** : obligatoire. Nom de l'indexeur.

- **dataSourceName** : obligatoire. Nom d'une source de données existante.

- **targetIndexName** : obligatoire. Nom d'un index existant.

- **schedule** : facultatif. Consultez la section [Planification d’indexation](#IndexingSchedule) ci-dessous.

###<a id="IndexingSchedule"></a>Exécution d’indexeurs selon une planification

Un indexeur peut éventuellement spécifier une planification. Si une planification est présente, l'indexeur sera exécuté périodiquement, conformément à la planification. La planification dispose des attributs suivants :

- **interval** : obligatoire. Valeur de durée qui spécifie un intervalle ou une période d'exécution pour l'indexeur. L'intervalle minimal autorisé est de 5 minutes, l'intervalle maximal autorisé est d'une journée. Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Le modèle est le suivant : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` pour toutes les 15 minutes, `PT2H` pour toutes les 2 heures.

- **startTime** : obligatoire. Heure UTC (temps universel coordonné) qui spécifie l'heure à laquelle l'exécution de l'indexeur doit commencer.

###<a id="CreateIndexerExample"></a>Exemple de corps de requête

L’exemple suivant crée un indexeur qui copie les données de la collection référencée par la source de données `myDocDbDataSource` vers l’index `mySearchIndex` selon une planification qui commence le 1er janvier 2015 UTC et qui est exécutée toutes les heures.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###Réponse

Vous recevrez une réponse HTTP 201 Créé si l'indexeur a été créé avec succès.

##<a id="RunIndexer"></a>Étape 4 : Exécution d’un indexeur

En plus de l'exécution périodique selon une planification, un indexeur peut être appelé à la demande en émettant la requête HTTP POST suivante :

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###Réponse

Vous recevrez une réponse HTTP 202 Accepté si l'indexeur a été appelé avec succès.

##<a name="GetIndexerStatus"></a>Étape 5 : Obtention du statut de l’indexation

Vous pouvez émettre une requête HTTP GET pour récupérer l'historique d'état et d'exécution d'un indexeur :

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###Réponse

Vous verrez une réponse HTTP 200 OK renvoyée avec un corps de réponse contenant des informations sur l'état d'intégrité global de l'indexeur, le dernier appel de l'indexeur, ainsi que l'historique des appels récents de l'indexeur (le cas échéant).

La réponse doit être semblable à ce qui suit :

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

##<a name="NextSteps"></a>Étapes suivantes

Félicitations ! Vous venez d'apprendre comment intégrer Azure Search à Azure DocumentDB à l'aide de l'indexeur pour DocumentDB.

 - Pour en savoir plus sur Azure DocumentDB, consultez la [page du service DocumentDB](https://azure.microsoft.com/services/documentdb/).

 - Pour en savoir plus sur Azure Search, consultez la [page du service Search](https://azure.microsoft.com/services/search/).

<!---HONumber=AcomDC_0622_2016-->