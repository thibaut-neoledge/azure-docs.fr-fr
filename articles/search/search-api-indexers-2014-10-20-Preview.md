<properties pageTitle="Opérations de l'indexeur (API REST du service Azure Search : 2014-10-20-Preview)" description="Opérations de l'indexeur (API REST du service Azure Search : 2014-10-20-Preview)" services="search" documentationCenter="" authors="HeidiSteen" manager="mblythe" />

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="07/08/2015" ms.author="heidist" />

#Opérations de l'indexeur (API REST du service Azure Search : 2014-10-20-Preview)

> [AZURE.NOTE]Cet article décrit un prototype de nouvelles fonctionnalités qui ne sont pas dans la version publiée de l'API. Pour plus d'informations sur les versions et la capacité de prise en charge, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx) sur MSDN. Pour plus d'informations sur les autres fonctionnalités dans cette API en version préliminaire, consultez [API REST du service Azure Search : version 2014-10-20-Preview](search-api-2014-10-20-preview.md).

## Vue d’ensemble

Azure Search peut s'intégrer directement à des sources de données courantes, ce qui évite d'avoir à écrire du code pour indexer vos données. Pour cela, vous pouvez appeler l'API Azure Search pour créer et gérer des **indexeurs** et des **sources de données**.

Une **source de données** définit les données à indexer, les informations d'identification pour accéder aux données et les indications pour permettre à Azure Search d'identifier efficacement les modifications au niveau des données (par exemple, des lignes modifiées ou supprimées dans une table de base de données).

Un **indexeur** décrit le flux de données de votre source de données dans un index de recherche. Un indexeur peut :

- effectuer une copie unique des données ;
- synchroniser un index Azure Search avec les modifications apportées à la source de données selon une planification ;
- être appelé à la demande à tout moment pour effectuer la copie.

Les sources de données suivantes sont actuellement prises en charge :

- Base de données SQL Azure
- Base de données de documents 

Nous envisageons d'ajouter la prise en charge de sources de données supplémentaires à l'avenir. Pour nous aider à classer ces décisions par ordre de priorité, indiquez vos commentaires sur le [forum des commentaires Azure Search](http://feedback.azure.com/forums/263029-azure-search).

**REMARQUE** Les fonctionnalités décrites ici sont prises en charge depuis la version de l'API Azure Search`2014-10-20-Preview`.

## Flux d'utilisation classique

Les étapes classiques pour configurer l'indexation automatique sont les suivantes :

1. Identifier la source de données qui contient les données qui doivent être indexées. Gardez à l'esprit qu'il est possible qu'Azure Search ne prenne pas en charge tous les types de données présents dans votre source de données

2. Créer un index Azure Search dont le schéma est compatible avec votre source de données
  
3. Créer une source de données Azure Search comme décrit dans la section Opérations de source de données ci-dessous
  
4. Créer un indexeur Azure Search et surveiller son exécution, comme décrit dans la section Opérations de l'indexeur ci-dessous

## Règles d'affectation des noms

- Un nom de source de données doit uniquement contenir des lettres minuscules, des chiffres ou des tirets, ne peut pas commencer ni se terminer par des tirets et est limité à 128 caractères.
- Un nom d'indexeur doit uniquement contenir des lettres minuscules, des chiffres ou des tirets, ne peut pas commencer ni se terminer par des tirets et est limité à 128 caractères.

## Limites et contraintes

Pour plus d'informations, consultez la page [Limites et contraintes](http://msdn.microsoft.com/library/azure/dn798934.aspx).

## Opérations de source de données
Vous pouvez créer et gérer des sources de données dans le service Azure Search via de simples requêtes HTTP (POST, GET, PUT, DELETE) sur une ressource de source de données indiquée.

### Créer une source de données

Vous pouvez créer une source de données au sein d'un service Azure Search à l'aide d'une requête HTTP POST.
	
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Vous pouvez également utiliser une requête PUT en spécifiant le nom de source de données sur l'URI. Si la source de données n'existe pas, elle est créée.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**Remarque** : le nombre maximal de sources de données que vous pouvez créer varie en fonction du niveau de tarification. Le service gratuit autorise jusqu'à 3 sources de données. Le service standard autorise 50 sources de données.

**Requête**

Le protocole HTTPS est requis pour toutes les requêtes de service. La requête **Create Data Source** peut être construite à l'aide d'une méthode POST ou PUT. Si vous utilisez une méthode POST, fournissez un nom de source de données dans le corps de la requête avec la définition de source de données. Avec la méthode PUT, le nom fait partie de l'URL. Si la source de données n’existe pas, elle est créée. Si elle existe déjà, elle est mise à jour en fonction de la nouvelle définition.

Le nom de source de données doit être en minuscules, commencer par une lettre ou un chiffre, ne contenir ni barres obliques ni points, et comprendre moins de 128 caractères. Après la lettre ou le chiffre du début, le nom de source de données peut comprendre des lettres, des chiffres et des tirets (non consécutifs).

`api-version` est obligatoire. Les valeurs valables incluent `2014-10-20-Preview` ou une version ultérieure.

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `Content-Type` : obligatoire. À définir avec la valeur `application/json`
- `api-key` : obligatoire. L'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de type chaîne de caractères, unique pour votre service. La requête **Create Data Source** doit inclure un en-tête `api-key` défini avec la valeur de votre clé d'administration (par opposition à une clé de requête). 
 
Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure en version préliminaire. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

<a name="CreateDataSourceRequestSyntax"></a> **Syntaxe du corps de la requête**

Le corps de la requête contient une définition de source de données, qui inclut le type de la source de données, des informations d'identification pour lire les données, ainsi que des stratégies facultatives de détection des modifications et suppressions de données, qui permettent d'identifier efficacement les données modifiées ou supprimées dans la source de données quand celle-ci est utilisée avec un indexeur planifié à intervalles réguliers.


La syntaxe de structuration de la charge utile de la requête est la suivante. Un exemple de requête est fourni plus loin dans cette rubrique.

    { 
		"name" : "Required for POST, optional for PUT. The name of the data source",
    	"description" : "Optional. Anything you want, or nothing at all",
    	"type" : "Required. Must be 'azuresql' or 'docdb'",
    	"credentials" : { "connectionString" : "Required. Connection string for your Azure SQL database" },
    	"container" : { "name" : "Required. The name of the table or collection you wish to index" },
    	"dataChangeDetectionPolicy" : { Optional. See below for details }, 
    	"dataDeletionDetectionPolicy" : { Optional. See below for details }
	}

La requête peut contenir les propriétés suivantes :

- `name` : obligatoire. Nom de la source de données. Un nom de source de données doit uniquement contenir des lettres minuscules, des chiffres ou des tirets, ne peut pas commencer ni se terminer par des tirets et est limité à 128 caractères.
- `description` : une description facultative. 
- `type` : obligatoire. Utilisez `azuresql` pour une source de données SQL Azure, `docdb` pour une source de données DocumentDB.
- `container` : 
	- La propriété `name` obligatoire spécifie la table ou vue (pour une source de données SQL Azure), ou la collection (pour une source de données DocumentDB) à indexer. 
	- Les sources de données DocumentDB prennent également en charge une propriété `query` facultative permettant de spécifier une requête qui aplanit une disposition de document JSON arbitraire dans un schéma plat qu'Azure Search peut indexer.   
- Les stratégies facultatives `dataChangeDetectionPolicy` et `dataDeletionDetectionPolicy` sont décrites ci-dessous.

<a name="DataChangeDetectionPolicies"></a> **Stratégies de détection des modifications de données**

L'objectif d'une stratégie de détection des changements de données est d'identifier efficacement les données modifiées. Les stratégies prises en charge varient selon le type de source de données. Les sections ci-dessous décrivent chaque stratégie.

***Stratégie de détection de modification de limite supérieure***

Utilisez cette stratégie lorsque votre source de données contient une colonne ou une propriété qui répond aux critères suivants :
 
- Toutes les insertions spécifient une valeur pour la colonne. 
- Toutes les mises à jour d'un élément modifient également la valeur de la colonne. 
- La valeur de cette colonne augmente à chaque modification.
- Les requêtes qui utilisent une clause de filtre similaire à la clause `WHERE [High Water Mark Column] > [Current High Water Mark Value]` suivante peuvent être exécutées efficacement.

Par exemple, en cas d'utilisation de sources de données Azure SQL, une colonne `rowversion` indexée est parfaitement indiquée pour une utilisation avec la stratégie de limite supérieure.

En cas d'utilisation de sources de données DocumentDB, vous devez utiliser la propriété `_ts` fournie par DocumentDB.
 
Cette stratégie peut être spécifiée comme suit :

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
		"highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	} 

***Stratégie SQL de détection des modifications intégrée***

Si votre base de données SQL prend en charge le [suivi intégré des modifications SQL](http://technet.microsoft.com/library/cc280462.aspx), nous vous recommandons d’utiliser la stratégie intégrée SQL de suivi des modifications. Cette stratégie assure le suivi des modifications le plus efficace et permet à Azure Search d'identifier les lignes supprimées sans que le schéma doive contenir une colonne « suppression réversible » explicite.

Le suivi intégré des modifications SQL est pris en charge à partir des versions de base de données SQL suivantes : - SQL Server 2008 R2, si vous utilisez des machines virtuelles SQL IaaS ; - Base de données SQL Azure V12, si vous utilisez SQL Azure.

**REMARQUE :** en cas d'utilisation d'une stratégie intégrée SQL de suivi des modifications, ne spécifiez pas de stratégie distincte de détection des suppressions de données. Cette stratégie intègre la prise en charge de l'identification des lignes supprimées.

**REMARQUE :** cette stratégie peut être utilisée uniquement avec des tables, pas avec des vues. Avant d'appliquer cette stratégie, vous devez activer le suivi des modifications pour la table que vous utilisez.
 
La stratégie intégrée SQL de suivi des modifications peut être spécifiée comme suit :

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	}

<a name="DataDeletionDetectionPolicies"></a> **Stratégies de détection des suppressions de données**

L'objectif d'une stratégie de détection des suppressions de données est d'identifier efficacement les données supprimées. Actuellement, la seule stratégie prise en charge est la stratégie `Soft Delete`, qui est spécifiée comme suit :

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
		"softDeleteColumnName" : "the column that specifies whether a row was deleted", 
		"softDeleteMarkerValue" : "the value that identifies a row as deleted" 
	}

<a name="CreateDataSourceRequestExamples"></a> **Exemples de corps de requête**

Si vous prévoyez d'utiliser la source de données avec un indexeur qui s'exécute selon une planification, cet exemple montre comment spécifier des indicateurs de stratégie :

    { 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : true }
	}

Si vous souhaitez utiliser la source de données uniquement pour une copie ponctuelle des données, vous pouvez omettre les indicateurs de stratégie :

    { 
		"name" : "asqldatasource",
    	"description" : "anything you want, or nothing at all",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" }
	} 

**Réponse**

Pour une requête réussie : « 201 Créé ».

### Mise à jour de source de données

Vous pouvez mettre à jour une source de données existante à l'aide d'une requête HTTP PUT. Vous spécifiez le nom de la source de données à mettre à jour sur l'URI de requête :

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Requête** La syntaxe du corps de la requête est la même que pour les [requêtes Create Data Source](#CreateDataSourceRequestSyntax).

**Réponse** Pour une requête réussie : 201 Créé est renvoyé si une source de données a été créée, et 204 Pas de contenu si une source de données existante a été mise à jour.

**REMARQUE :** certaines propriétés ne peuvent pas être mises à jour dans une source de données existante. Par exemple, vous ne pouvez pas modifier le type d'une source de données existante.

### List Data Sources

L'opération **List Data Sources** retourne une liste des sources de données dans votre service Azure Search.

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

**Réponse**

Pour une requête réussie : « 200 OK ».

Voici un exemple de corps de réponse :

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
		  ... other data source properties
        }]
    }

Notez que vous pouvez filtrer la réponse de manière à afficher uniquement les propriétés qui vous intéressent. Par exemple, si vous voulez uniquement une liste des noms de sources de données, utilisez l'option de requête OData `$select` :

    GET /datasources?api-version=2014-10-20-Preview&$select=name

Dans ce cas, la réponse de l'exemple ci-dessus est affichée comme suit :

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Cette technique est utile pour économiser de la bande passante si votre service de recherche contient un grand nombre de sources de données.

### Get Data Source

L'opération **Get Data Source** obtient la définition de source de données d'Azure Search.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

**Réponse**

Code d'état : 200 OK est renvoyé en cas de réponse correcte.

La réponse est similaire aux exemples dans [Exemple de requêtes Create Data Source](#CreateDataSourceRequestExamples) :

	{ 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
			"highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
			"softDeleteColumnName" : "IsDeleted", 
			"softDeleteMarkerValue" : true }
	}

**REMARQUE** Lors de l'appel de cette API, ne définissez pas l'en-tête de requête `Accept` sur `application/json;odata.metadata=none`, car cela entraînerait l'omission de l'attribut `@odata.type` dans la réponse, et vous ne pourriez pas faire la différence entre les différentes stratégies de détection de modification et de suppression de données.

### Delete Data Source

L'opération de suppression de sources de données (**Delete Data Source**) supprime une source de données de votre service Azure Search.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

**REMARQUE** Si des indexeurs font référence à la source de données que vous supprimez, l'opération de suppression continue. Toutefois, ces indexeurs passeront à un état d'erreur lors de leur prochaine exécution.

**Réponse**

Code d'état : 204 Pas de contenu est retourné pour une réponse correcte.

## Opérations d'indexeur

Un indexeur est la ressource qui connecte des sources de données à des index de recherche cibles. Vous devez prévoir de créer un indexeur pour chaque association source de données/index cible. Vous pouvez avoir plusieurs indexeurs qui écrivent dans le même index. Toutefois, un indexeur peut uniquement écrire dans un index unique.

Vous pouvez créer et gérer des indexeurs via de simples requêtes HTTP (POST, GET, PUT, DELETE) sur une ressource d'indexeur donnée.

Après avoir créé un indexeur, vous pouvez récupérer son état d'exécution à l'aide de l'opération [Get Indexer Status](#GetIndexerStatus). Vous pouvez également exécuter un indexeur à tout moment (au lieu ou en plus de son exécution périodique planifiée) à l'aide de l'opération [Run Indexer](#RunIndexer).

### Create Indexer

Vous pouvez créer un indexeur dans un service Azure Search à l'aide d'une requête HTTP POST.
	
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Vous pouvez également utiliser une requête PUT en spécifiant le nom de source de données sur l'URI. Si la source de données n'existe pas, elle est créée.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

**Remarque** : le nombre maximal d'indexeurs que vous pouvez créer varie en fonction du niveau de tarification. Le service gratuit autorise jusqu'à 3 indexeurs. Le service standard autorise 50 indexeurs.

<a name="CreateIndexerRequestSyntax"></a> **Syntaxe du corps de la requête**

Le corps de la requête contient une définition de l'indexeur, qui spécifie la source de données et l'index cible pour l'indexation, ainsi qu'une planification d'indexation et des paramètres facultatifs.


Vous trouverez ci-dessous la syntaxe de structuration de la charge utile de la requête. Un exemple de requête est fourni plus loin dans cette rubrique.

    { 
		"name" : "Required for POST, optional for PUT. The name of the indexer",
    	"description" : "Optional. Anything you want, or null",
    	"dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. }
	}

**Planification d'indexation**

Un indexeur peut éventuellement spécifier une planification. Si une planification est présente, l'indexeur sera exécuté périodiquement, conformément à la planification. La planification dispose des attributs suivants :

- `interval` : obligatoire. Valeur de durée qui spécifie un intervalle ou une période d'exécution pour l'indexeur. L'intervalle minimal autorisé est de 5 minutes, l'intervalle maximal autorisé est d'une journée. Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Le modèle est le suivant : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` pour toutes les 15 minutes, `PT2H` pour toutes les 2 heures. 

- `startTime` : obligatoire. Heure UTC (temps universel coordonné) à laquelle l'exécution de l'indexeur doit commencer.

**Paramètres d'indexation**

Un indexeur peut éventuellement spécifier plusieurs paramètres qui affectent son comportement. Tous les paramètres sont facultatifs.

- `maxFailedItems` : nombre d'éléments dont l'indexation peut échouer avant que l'exécution de l'indexeur soit considérée comme un échec. La valeur par défaut est `0`. Des informations sur les éléments qui ont échoué sont renvoyées par l'opération [Get Indexer Status](#GetIndexerStatus). 

- `maxFailedItemsPerBatch` : nombre d'éléments dont l'indexation peut échouer dans chaque lot avant que l'exécution de l'indexeur soit considérée comme un échec. La valeur par défaut est `0`.

- `base64EncodeKeys` : spécifie si les clés de document doivent être codées en base 64. Azure Search impose des restrictions relatives aux caractères qui peuvent être présents dans une clé de document. Toutefois, les valeurs dans vos données source peuvent contenir des caractères non valides. S'il est nécessaire d'indexer ces valeurs en tant que clés de document, cet indicateur peut être défini sur true. La valeur par défaut est `false`.

<a name="CreateIndexerRequestExamples"></a> **Exemples de corps de requête**

L'exemple suivant crée un indexeur qui copie les données de la table référencée par la source de données `ordersds` vers l'index `orders` selon une planification qui commence le 1er janvier 2015 UTC et s'exécute toutes les heures. Chaque appel de l'indexeur est réussi si l'indexation n'échoue pas pour plus de 5 éléments par lot, et pour plus de 10 éléments au total.

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

**Réponse**

Pour une requête réussie : « 201 Créé ».

### Mise à jour d'indexeur

Vous pouvez mettre à jour un indexeur existant à l'aide d'une requête HTTP PUT. Vous spécifiez le nom de l'indexeur à mettre à jour sur l'URI de la requête :

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Requête**

La syntaxe du corps de la requête est la même que pour les [requêtes Create Indexer](#CreateIndexerRequestSyntax).

**Réponse**

Pour une requête réussie : 201 Créé est renvoyé si un indexeur a été créé, et 204 Pas de contenu si un indexeur existant a été mis à jour.

### List Indexers

L'opération **List Indexers** retourne la liste des indexeurs utilisés dans votre service Azure Search.

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]

**Réponse**

Pour une requête réussie : « 200 OK ».

Voici un exemple de corps de réponse :

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
	  }]
    }

Notez que vous pouvez filtrer la réponse de manière à afficher uniquement les propriétés qui vous intéressent. Par exemple, si vous voulez uniquement une liste de noms d'indexeurs, utilisez l'option de requête OData `$select` :

    GET /indexers?api-version=2014-10-20-Preview&$select=name

Dans ce cas, la réponse de l'exemple ci-dessus est affichée comme suit :

    {
      "value" : [ { "name": "myindexer" } ]
    }

Cette technique est utile pour économiser de la bande passante si votre service de recherche contient un grand nombre d'indexeurs.

### Get Indexer

L'opération **Get Indexer** obtient la définition d'indexeur auprès d'Azure Search.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

**Réponse**

Code d'état : 200 OK est renvoyé en cas de réponse correcte.

La réponse est similaire aux exemples dans l'[exemple de requêtes Create Indexer](#CreateIndexerRequestExamples) :

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

### Delete Indexer

L'opération de suppression d'un indexeur (**Delete Indexer**) supprime un indexeur de votre service Azure Search.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Quand un indexeur est supprimé, les exécutions d'indexeur en cours sont exécutées complètement, mais aucune autre exécution n'est planifiée. Les tentatives d'utilisation d'un indexeur inexistant génèrent le code d'état HTTP 404 Introuvable.
 
**Réponse**

Code d'état : 204 Pas de contenu est renvoyé en cas de réponse correcte.

<a name="RunIndexer"></a>
### Exécution d'un indexeur

En plus de l'exécution périodique selon une planification, un indexeur peut aussi être appelé à la requête via l'opération **Run Indexer** :

	POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

**Réponse**

Code d'état : 202 Accepté est retourné en cas de réponse correcte.

<a name="GetIndexerStatus"></a>
### Obtention de l'état de l'indexeur

L'opération **Get Indexer Status** récupère l'état actuel et l'historique d'exécution d'un indexeur :

	GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]

**Réponse**

Code d'état : 200 OK en cas de réponse correcte.

Le corps de la réponse contient des informations sur l'état d'intégrité global de l'indexeur, le dernier appel de l'indexeur, ainsi que l'historique des appels récents de l'indexeur (le cas échéant).

Voici un exemple de corps de réponse :

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

**État de l'indexeur**

Les valeurs possibles pour l'état de l'indexeur sont les suivantes :

- `running` Indique que l'indexeur s'exécute normalement. Notez que, comme certaines exécutions de l'indexeur peuvent encore échouer, nous recommandons de vérifier également la propriété `lastResult`. 

- `error` Indique que l'indexeur a rencontré une erreur qui ne peut pas être corrigée sans intervention humaine. Par exemple, il se peut que les informations d'identification de la source de données aient expiré, ou que le schéma de la source de données ou de l'index cible ait subitement changé.

**Résultat d'exécution de l'indexeur**

Un résultat d'exécution de l'indexeur contient des informations sur une seule exécution de l'indexeur. Le dernier résultat est présenté comme la propriété `lastResult` de l'état de l'indexeur. Les autres résultats récents éventuels sont renvoyés en tant que propriété `executionHistory` de l'état de l'indexeur.

Le résultat d'exécution de l'indexeur contient les propriétés suivantes :

- `status` : état de cette exécution. Pour plus d'informations, consultez [État d'exécution de l'indexeur](#IndexerExecutionStatus) ci-dessous. 

- `errorMessage` : message d'erreur pour un échec d'exécution.

- `startTime` : heure UTC à laquelle cette exécution a commencé.

- `endTime` : heure UTC à laquelle cette exécution s'est achevée. Cette valeur n'est pas définie si l'exécution est encore en cours.

- `errors` : liste d'éventuelles erreurs au niveau des éléments.

- `itemsProcessed` : nombre d'éléments de source de données (par exemple, lignes de table) que l'indexeur a tenté d'indexer durant cette exécution.

- `itemsFailed` : nombre d'éléments dont l'exécution a échoué au cours de cette opération.
 
- `initialTrackingState` : toujours `null` pour la première exécution de l'indexeur, ou si la stratégie de suivi des modifications des données n'est pas activée dans la source de données utilisée. Si une telle stratégie est activée, cette valeur est, lors des exécutions suivantes, la première valeur (la plus basse) de suivi des modifications traitée au cours cette exécution.

- `finalTrackingState` : toujours `null` si la stratégie de suivi des modifications des données n'est pas activée dans la source de données utilisée. Sinon, indique la dernière valeur (la plus élevée) de suivi des modifications correctement traitée par cette exécution.

<a name="IndexerExecutionStatus"></a> **État d'exécution de l'indexeur**

L'état d'exécution de l'indexeur reflète l'état d'une seule exécution. Il peut avoir les valeurs suivantes :

- `success` indique que l'exécution de l'indexeur s'est terminée correctement.

- `inProgress` Indique que l'exécution de l'indexeur est en cours.

- `transientFailure` Indique que l'appel de l'indexeur a échoué, mais l'échec peut être temporaire. Les appels de l'indexeur continuent conformément à la planification, si celle-ci est définie.

- `persistentFailure` Indique que l'indexeur a échoué d'une manière telle qu'une intervention humaine est probablement nécessaire (par exemple, en raison d'une incompatibilité de schéma entre la source de données et l'index cible). Les exécutions planifiées de l'indexeur s'arrêtent ; une action de l'utilisateur est requise pour résoudre le problème (décrit dans la propriété `errorMessage`) et redémarrer l'exécution de l'indexeur.

- `reset` Indique que l'indexeur a été réinitialisé par un appel à l'API Reset Indexer (voir ci-dessous).

<a name="ResetIndexer"></a>
### Réinitialisation de l'indexeur

L'opération de réinitialisation de l'indexeur (**Reset Indexer**) réinitialise l'état de suivi des modifications associé à l'indexeur. Cela vous permet de déclencher une réindexation complète (par exemple, si votre schéma de source de données a changé) ou de modifier la stratégie de détection des modifications de données pour une source de données associée à l'indexeur.

	POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

**Réponse**

Code d'état : 204 Pas de contenu en cas de réponse correcte.

## Mappage entre les types de données SQL et les types de données Azure Search

<table style="font-size:12">
<tr>
<td>Type de données SQL</td>	
<td>Types de champs d'index cible autorisés</td>
<td>Remarques</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>real, float</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, money<br>décimal<br>numérique
</td>
<td>Edm.String</td>
<td>Azure Search ne prend pas en charge la conversion de types décimaux en Edm.Double, car elle entraîne une perte de précision
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>rowversion</td>
<td>N/A</td>
<td>Les colonnes de version de ligne ne peuvent pas être stockées dans l'index de recherche, mais peuvent être utilisées pour le suivi des modifications</td>
</tr>
<tr>
<td>time, timespan<br>binary, varbinary, image<br>xml<br>geometry<br> geography<br>Types CLR</td>
<td>N/A</td>
<td>Non pris en charge</td>
</tr>
</table>

## Mappage entre les types de données JSON et les types de données Azure Search

<table style="font-size:12">
<tr>
<td>Type de données JSON</td>	
<td>Types de champs d'index cible autorisés</td>
<td>Remarques</td>
</tr>
<tr>
<td>valeur booléenne</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Nombres entiers</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td>JSON ne disposant pas d'entiers typés, nous devons envisager le plus grand nombre, des entiers 64&#160;bits</td>
</tr>
<tr>
<td>Nombres à virgule flottante</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>string</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>Tableaux de types primitifs, par exemple [ «&#160;a&#160;», «&#160;b&#160;», «&#160;c&#160;» ]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>Chaînes qui ressemblent à des dates</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Objets JSON</td>
<td>N/A</td>
<td>Non pris en charge&#160;; Azure Search prend actuellement en charge uniquement les types primitifs et les collections de chaînes</td>
</tr>
</table>

<!---HONumber=August15_HO6-->