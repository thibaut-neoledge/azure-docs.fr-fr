---
title: "Opérations de l’indexeur (API REST du service Recherche Azure : 2015-02-28-Preview) | Microsoft Docs"
description: "Opérations de l'indexeur (API REST du service Azure Search : 2015-02-28-Preview)"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 42b5f85c-8304-4bc7-8e1e-a9c76b8ca25a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: eugenesh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 356ceb98106d080d8c24dedc3547bee33750156e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>Opérations de l'indexeur (API REST du service Azure Search : 2015-02-28-Preview)
> [!NOTE]
> Cet article décrit les indexeurs dans la version [API REST 2015-02-28-Preview](search-api-2015-02-28-preview.md). Cette version de l'API ajoute des versions préliminaires d’un indexeur de stockage d’objets blob Azure avec extraction des documents et un indexeur de stockage de tables Azure, ainsi que d’autres améliorations. L’API prend également en charge les indexeurs en disponibilité générale (GA), y compris les indexeurs pour base de données SQL Azure, SQL Server sur des machines virtuelles Azure et Azure Cosmos DB.
> 
> 

## <a name="overview"></a>Vue d'ensemble
Azure Search peut s'intégrer directement à des sources de données courantes, ce qui évite d'avoir à écrire du code pour indexer vos données. Pour configurer cela, vous pouvez appeler l’API Recherche Azure pour créer et gérer des **indexeurs** et des **sources de données**. 

Un **indexeur** est une ressource qui connecte des sources de données à des index de recherche cibles. Un indexeur est utilisé pour : 

* effectuer une copie unique des données pour remplir un index ;
* synchroniser un index avec les modifications apportées à la source de données selon une planification. La planification fait partie de la définition de l'indexeur ;
* appeler à la demande pour mettre à jour un index en fonction des besoins. 

Un **indexeur** est utile lorsque vous souhaitez mettre un index régulièrement à jour. Vous pouvez configurer une planification incluse dans le cadre d'une définition d'indexeur, ou l'exécuter à la demande à l'aide de la commande [Exécuter l'indexeur](#RunIndexer) 

Une **source de données** spécifie les données à indexer, les informations d'identification nécessaires pour accéder aux données et les stratégies pour permettre à Azure Search d'identifier correctement les modifications de données (telles que des lignes modifiées ou supprimées dans une table de base de données). Elle est définie en tant que ressource indépendante utilisable par plusieurs indexeurs.

Les sources de données actuellement prises en charge sont les suivantes :

* **Base de données Azure SQL** et **SQL Server sur les machines virtuelles Azure**. Pour obtenir une procédure pas à pas ciblée, consultez [cet article](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 
* **Azure Cosmos DB**. Pour obtenir une procédure pas à pas ciblée, consultez [cet article](search-howto-index-documentdb.md). 
* **Stockage d'objets blob azure**, notamment les formats de document suivants : Microsoft Office (DOCX/DOC, XSLX/XLS, PPTX/PPT, MSG), HTML, XML, ZIP et fichiers texte brut (y compris JSON). Pour obtenir une procédure pas à pas ciblée, consultez [cet article](search-howto-indexing-azure-blob-storage.md).
* **Stockage Table Azure**. Pour obtenir une procédure pas à pas ciblée, consultez [cet article](search-howto-indexing-azure-tables.md).

Nous envisageons d'ajouter une prise en charge de sources de données supplémentaires à l'avenir. Pour nous aider à classer ces décisions par ordre de priorité, indiquez vos commentaires sur le [forum des commentaires Azure Search](http://feedback.azure.com/forums/263029-azure-search).

Consultez les [Limites du service](search-limits-quotas-capacity.md) pour les limites maximales liées à l’indexeur et aux sources de données.

## <a name="typical-usage-flow"></a>Flux d'utilisation typique
Vous pouvez créer et gérer des index dans le service Azure Search par le biais de simples requêtes HTTP (POST, GET, PUT, DELETE) sur une ressource `data source` ou `indexer` spécifique.

La configuration de l'indexation automatique est généralement un processus en quatre étapes :

1. Identifiez la source de données contenant les données à indexer. N'oubliez pas qu'Azure Search ne prend peut-être pas en charge tous les types de données présents dans votre source de données. Pour obtenir la liste, consultez [Types de données pris en charge](https://msdn.microsoft.com/library/azure/dn798938.aspx) .
2. Créez un index Azure Search dont le schéma est compatible avec votre source de données.
3. Créez une source de données Azure Search comme décrit dans [Création d'une source de données](#CreateDataSource).
4. Créez un indexeur Azure Search comme décrit dans [Création d'indexeur](#CreateIndexer).

Vous devez prévoir de créer un indexeur pour chaque association source de données/index cible. Vous pouvez avoir plusieurs indexeurs écrivant dans le même index et réutiliser la même source de données pour plusieurs indexeurs. Toutefois, un indexeur ne peut consommer qu'une source de données à la fois, et ne peut écrire que dans un seul index. 

Après avoir créé un indexeur, vous pouvez récupérer son état d'exécution à l'aide de l'opération [Get Indexer Status](#GetIndexerStatus) . Vous pouvez également exécuter un indexeur à tout moment (au lieu de ou en plus de son exécution périodique planifiée) à l'aide de l'opération [Run Indexer](#RunIndexer) .

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Création d'une source de données
Dans Azure Search, une source de données est utilisée avec les indexeurs afin de fournir les informations de connexion pour l'actualisation ad hoc ou planifiée des données d'un index cible. Vous pouvez créer une source de données au sein d'un service Azure Search à l'aide d'une requête HTTP POST.

    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Vous pouvez également utiliser une requête PUT en spécifiant le nom de source de données sur l'URI. Si la source de données n'existe pas, elle est créée.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

> [!NOTE]
> Le nombre maximal de sources de données que vous pouvez créer varie en fonction du niveau de tarification. Le service gratuit autorise jusqu'à 3 sources de données. Le service standard autorise 50 sources de données. Pour plus d’informations, consultez [Limites de service](search-limits-quotas-capacity.md) .
> 
> 

**Requête**

Le protocole HTTPS est requis pour toutes les requêtes de service. La requête **Create Data Source** peut être construite à l'aide d'une méthode POST ou PUT. Si vous utilisez une méthode POST, fournissez un nom de source de données dans le corps de la requête avec la définition de source de données. Avec la méthode PUT, le nom fait partie de l'URL. Si la source de données n’existe pas, elle est créée. Si elle existe déjà, elle est mise à jour en fonction de la nouvelle définition. 

Le nom de source de données doit être en minuscules, commencer par une lettre ou un chiffre, ne contenir ni barres obliques ni points, et comprendre moins de 128 caractères. Après la lettre ou le chiffre du début, le nom de source de données peut comprendre des lettres ou chiffres quelconques, ainsi que des tirets (non consécutifs). Pour en savoir plus, consultez [Règles d'affectation des noms](https://msdn.microsoft.com/library/azure/dn857353.aspx) .

Le paramètre `api-version` est obligatoire. La version actuelle est `2015-02-28`.

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs. 

* `Content-Type`: requis. À définir avec la valeur `application/json`
* `api-key`: requis. L'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de type chaîne de caractères, unique pour votre service. La requête **Create Data Source** doit inclure un en-tête `api-key` défini avec la valeur de votre clé d’administration (par opposition à une clé de requête). 

Vous avez également besoin du nom du service pour construire l'URL de la requête. Vous pouvez obtenir le nom du service et l’en-tête `api-key` sur votre tableau de bord de service du [Portail Azure](https://portal.azure.com/). Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Search dans le portail](search-create-service-portal.md) .

<a name="CreateDataSourceRequestSyntax"></a>
**Syntaxe du corps de la requête**

Le corps de la requête contient une définition de source de données, qui inclut le type de la source de données, des informations d'identification pour lire les données, ainsi que des stratégies facultatives de détection des modifications et suppressions de données, qui permettent d'identifier efficacement les données modifiées ou supprimées dans la source de données quand celle-ci est utilisée avec un indexeur planifié à intervalles réguliers. 

La syntaxe de structuration de la charge utile de la requête est la suivante. Vous trouverez un exemple de requête dans cette rubrique.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

La requête peut contenir les propriétés suivantes : 

* `name`: requis. Nom de la source de données. Un nom de source de données doit uniquement contenir des lettres minuscules, des chiffres ou des tirets, ne peut pas commencer ni se terminer par des tirets et est limité à 128 caractères.
* `description`: une description facultative. 
* `type`: requis. Doit être de l'un des types de sources de données pris en charge :
  * `azuresql` - Base de données Azure SQL ou SQL Server sur les machines virtuelles Azure
  * `documentdb` - Azure Cosmos DB
  * `azureblob` - Stockage Blob Azure
  * `azuretable` - Stockage Table Azure
* `credentials`:
  * La propriété `connectionString` obligatoire spécifie la chaîne de connexion pour la source de données. Le format de la chaîne de connexion dépend du type de source de données : 
    * Pour Azure SQL, il s'agit de la chaîne de connexion SQL Server habituelle. Si vous utilisez le Portail Azure pour obtenir la chaîne de connexion, sélectionnez l’option `ADO.NET connection string` .
    * Pour Azure Cosmos DB, la chaîne de connexion doit avoir le format suivant : `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Toutes les valeurs sont obligatoires. Elles sont disponibles sur le [Portail Azure](https://portal.azure.com/).  
    * Pour le Stockage Blob et Table Azure, il s’agit de la chaîne de connexion du compte de stockage. Ce format est décrit [ici](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). Un protocole de point de terminaison HTTPS est obligatoire.  
* `container`, obligatoire : spécifie les données à indexer en utilisant les propriétés `name` et `query` : 
  * `name` (obligatoire) :
    * Azure SQL : spécifie la table ou la vue. Vous pouvez utiliser des noms qualifiés par schéma, tels que `[dbo].[mytable]`.
    * DocumentDB : spécifie la collection. 
    * Stockage Blob Azure : spécifie le conteneur de stockage.
    * Stockage Table Azure : spécifie le nom de la table. 
  * `query`(facultatif) :
    * DocumentDB : vous permet permettant de spécifier une requête qui aplanit une disposition de document JSON arbitraire dans un schéma plat qu'Azure Search peut indexer.  
    * Stockage d'objets blob Azure : vous permet de spécifier un dossier virtuel dans le conteneur d'objets blob. Par exemple, pour le chemin d’accès aux objets blob `mycontainer/documents/blob.pdf`, `documents` peut être utilisé comme dossier virtuel.
    * Stockage Table Azure : vous permet de spécifier une requête qui filtre l’ensemble de lignes à importer.
    * Azure SQL : requête non prise en charge. Si vous avez besoin de cette fonctionnalité, veuillez voter pour [cette suggestion](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
* Les propriétés facultatives `dataChangeDetectionPolicy` et `dataDeletionDetectionPolicy` sont décrites ci-dessous.

<a name="DataChangeDetectionPolicies"></a>
**Stratégies de détection des modifications de données**

L'objectif d'une stratégie de détection des changements de données est d'identifier efficacement les données modifiées. Les stratégies prises en charge varient selon le type de source de données. Les sections ci-dessous décrivent chaque stratégie. 

***Stratégie de détection des modifications de limite supérieure*** 

Utilisez cette stratégie lorsque votre source de données contient une colonne ou une propriété qui répond aux critères suivants :

* Toutes les insertions spécifient une valeur pour la colonne. 
* Toutes les mises à jour d'un élément modifient également la valeur de la colonne. 
* La valeur de cette colonne augmente à chaque modification.
* Les requêtes qui utilisent une clause de filtre similaire à la clause `WHERE [High Water Mark Column] > [Current High Water Mark Value]` suivante peuvent être exécutées efficacement.

Par exemple, en cas d'utilisation de sources de données Azure SQL, une colonne `rowversion` indexée est parfaitement indiquée pour une utilisation avec la stratégie de limite supérieure. 

Cette stratégie peut être spécifiée comme suit :

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

En cas d'utilisation de sources de données Azure Cosmos DB, vous devez utiliser la propriété `_ts` fournie par Azure Cosmos DB. 

Lors de l'utilisation de sources de données d'objets blob Azure, Azure Search utilise automatiquement utilise une stratégie de détection de modification de limite supérieure basée sur l’horodatage de la dernière modification d’un objet blob ; vous n'avez pas besoin de spécifier une telle stratégie vous-même.   

***Stratégie SQL de détection des modifications intégrée***

Si votre base de données SQL prend en charge le [suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx), nous recommandons d'utiliser la stratégie SQL de suivi des modifications intégrée. Cette stratégie assure le suivi des modifications le plus efficace et permet à Azure Search d'identifier les lignes supprimées sans que le schéma doive contenir une colonne « suppression réversible » explicite.

Le suivi intégré des modifications est pris en charge à partir des versions de base de données SQL Server suivantes : 

* SQL Server 2008 R2, si vous utilisez SQL Server on Azure VMs.
* Azure SQL Database V12, si vous utilisez Azure SQL Database.  

En cas d'utilisation d'une stratégie SQL de suivi des modifications intégrée, ne spécifiez pas de stratégie de détection des suppressions de données distincte. Cette stratégie intègre la prise en charge de l'identification des lignes supprimées. 

Elle peut être utilisée uniquement avec des tables, non avec des vues. Pour pouvoir appliquer cette stratégie, vous devez activer le suivi des modifications sur la table. Consultez [Activer et désactiver le suivi des modifications](https://msdn.microsoft.com/library/bb964713.aspx) pour obtenir des instructions.    

Lors de la structuration de la requête **Create Data Source** , vous pouvez spécifier une stratégie SQL de suivi des modifications intégrée comme suit :

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Stratégies de détection des suppressions de données**

L'objectif d'une stratégie de détection des suppressions de données est d'identifier efficacement les données supprimées. Actuellement, la seule stratégie de prise en charge est la stratégie `Soft Delete`, qui permet d'identifier les éléments supprimés selon la valeur d’une colonne ou propriété `soft delete` dans la source de données : Cette stratégie peut être spécifiée comme suit :

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

> [!NOTE]
> Seules les colonnes contenant des valeurs de type chaîne de caractères, entier ou booléennes sont prises en charge. La valeur utilisée en tant que `softDeleteMarkerValue` doit être une chaîne de caractère, même si la colonne correspondante contient des entiers ou des valeurs booléennes. Par exemple, si la valeur figurant dans votre source de données est 1, utilisez `"1"` comme `softDeleteMarkerValue`.    
> 
> 

<a name="CreateDataSourceRequestExamples"></a>
**Exemples de corps de requête**

Si vous prévoyez d'utiliser la source de données avec un indexeur exécuté selon une planification, cet exemple montre comment spécifier des stratégies de détection des modifications et des suppressions : 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Si vous souhaitez utiliser la source de données uniquement pour une copie ponctuelle des données, vous pouvez omettre les stratégies :

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Réponse**

Pour une requête réussie : « 201 Créé ». 

<a name="UpdateDataSource"></a>

## <a name="update-data-source"></a>Mise à jour d'une source de données
Vous pouvez mettre à jour une source de données existante à l'aide d'une requête HTTP PUT. Vous spécifiez le nom de la source de données à mettre à jour dans l'URI de la requête :

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Le paramètre `api-version` est obligatoire. La version actuelle est `2015-02-28`. Pour plus d’informations, notamment sur d’autres versions, consultez [Versions d’API Recherche Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx).

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

**Requête**

La syntaxe du corps de la requête est la même que celle des [requêtes Create Data Source](#CreateDataSourceRequestSyntax).

Certaines propriétés ne peuvent pas être mises à jour dans une source de données existante. Par exemple, vous ne pouvez pas modifier le type d'une source de données existante.  

Si vous ne souhaitez pas modifier la chaîne de connexion d’une source de données existante, vous pouvez spécifier la chaîne littérale `<unchanged>` comme chaîne de connexion. Cette méthode est utile lorsque vous devez mettre à jour une données source mais que vous n'avez pas facilement accès à la chaîne de connexion car il s’agit de données de sécurité sensibles.

**Réponse**

Pour une requête réussie : 201 Créé est renvoyé si une source de données a été créée, et 204 Pas de contenu si une source de données existante a été mise à jour.

<a name="ListDataSource"></a>

## <a name="list-data-sources"></a>Liste des sources de données
L'opération **List Data Sources** renvoie une liste des sources de données dans votre service Azure Search. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

Le paramètre `api-version` est obligatoire. La version actuelle est `2015-02-28`. 

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

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

Notez que vous pouvez filtrer la réponse de manière à afficher uniquement les propriétés qui vous intéressent. Par exemple, si vous voulez uniquement une liste des noms de sources de données, utilisez l'option de requête OData `$select` :

    GET /datasources?api-version=205-02-28&$select=name

Dans ce cas, la réponse de l'exemple ci-dessus apparaît comme suit : 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Cette technique est utile pour économiser de la bande passante si votre service Search contient un nombre important de sources de données.

<a name="GetDataSource"></a>

## <a name="get-data-source"></a>Obtention de source de données
L'opération d'obtention de source de données ( **Get Data Source** ) renvoie la définition de source de données d'Azure Search.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

Le paramètre `api-version` est obligatoire. La version actuelle est `2015-02-28`. 

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

**Réponse**

Code d'état : 200 OK est retourné pour une réponse correcte.

La réponse est similaire aux exemples dans [Exemple de requêtes Create Data Source](#CreateDataSourceRequestExamples): 

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
            "softDeleteMarkerValue" : "true" }
    }

> [!NOTE]
> Lors de l’appel de cette API, ne définissez pas l’en-tête de requête `Accept` sur `application/json;odata.metadata=none`. L’attribut `@odata.type` serait omis dans la réponse, et vous ne pourriez pas faire la différence entre les types de stratégies de détection de modification et de suppression de données. 
> 
> 

<a name="DeleteDataSource"></a>

## <a name="delete-data-source"></a>Suppression de sources de données 
L'opération de suppression de sources de données ( **Delete Data Source** ) supprime une source de données de votre service Azure Search.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> Si des indexeurs font référence à la source de données que vous supprimez, l'opération de suppression continue. Toutefois, ces indexeurs passeront à un état d'erreur lors de leur prochaine exécution.  
> 
> 

Le paramètre `api-version` est obligatoire. La version actuelle est `2015-02-28`. 

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

**Réponse**

Code d'état : 204 Pas de contenu est renvoyé en cas de réponse correcte.

<a name="CreateIndexer"></a>

## <a name="create-indexer"></a>Création d'indexeur
Vous pouvez créer un indexeur dans un service Azure Search à l'aide d'une requête HTTP POST.

    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Vous pouvez également utiliser une requête PUT en spécifiant le nom de source de données sur l'URI. Si la source de données n'existe pas, elle est créée.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [!NOTE]
> Le nombre maximal d'indexeurs que vous pouvez créer varie en fonction du niveau de tarification. Le service gratuit autorise jusqu'à 3 indexeurs. Le service standard autorise 50 indexeurs. Pour plus d’informations, consultez [Limites de service](search-limits-quotas-capacity.md) .
> 
> 

Le paramètre `api-version` est obligatoire. La version actuelle est `2015-02-28`. 

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

<a name="CreateIndexerRequestSyntax"></a>
**Syntaxe du corps de la requête**

Le corps de la requête contient une définition de l'indexeur, qui spécifie la source de données et l'index cible pour l'indexation, ainsi qu'une planification d'indexation et des paramètres facultatifs. 

La syntaxe de structuration de la charge utile de la requête est la suivante. Vous trouverez un exemple de requête dans cette rubrique.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Planification de l'indexeur**

Un indexeur peut éventuellement spécifier une planification. Si une planification est présente, l'indexeur sera exécuté périodiquement, conformément à la planification. La planification dispose des attributs suivants :

* `interval`: requis. Valeur de durée qui spécifie un intervalle ou une période d'exécution pour l'indexeur. L'intervalle minimal autorisé est de 5 minutes, l'intervalle maximal autorisé est d'une journée. Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle est le suivant : `"P[nD][T[nH][nM]]"`. Exemples : `PT15M` toutes les 15 minutes, `PT2H` toutes les deux heures. 
* `startTime`: requis. Date/heure UTC (temps universel coordonné) à laquelle l'exécution de l'indexeur doit commencer. 

**Paramètres d'indexeur**

Un indexeur peut éventuellement spécifier plusieurs paramètres qui affectent son comportement. Tous les paramètres sont facultatifs.  

* `maxFailedItems` : nombre d'éléments dont l'indexation peut échouer avant que l'exécution de l'indexeur soit considérée comme un échec. La valeur par défaut est 0. Des informations sur les éléments qui ont échoué sont renvoyées par l'opération [Get Indexer Status](#GetIndexerStatus) . 
* `maxFailedItemsPerBatch` : nombre d'éléments dont l'indexation peut échouer dans chaque lot avant que l'exécution de l'indexeur soit considérée comme un échec. La valeur par défaut est 0.
* `base64EncodeKeys`: spécifie si les clés de document doivent être codées en base 64. Azure Search impose des restrictions relatives aux caractères qui peuvent être présents dans une clé de document. Toutefois, les valeurs dans vos données source peuvent contenir des caractères non valides. S'il est nécessaire d'indexer ces valeurs en tant que clés de document, cet indicateur peut être défini sur true. La valeur par défaut est `false`.
* `batchSize`: spécifie le nombre d’éléments lus à partir de la source de données et indexés comme un lot unique afin d’améliorer les performances. La valeur par défaut varie selon le type de source de données : 1 000 pour Azure SQL et Azure Cosmos DB, et 10 pour le Stockage Blob Azure.

**Mappages de champs**

Vous pouvez utiliser des mappages de champs pour mapper un nom de champ dans la source de données sur un autre nom de champ dans l'index cible. Par exemple, considérez une table source avec un champ `_id`. Azure Search n'autorise pas un nom de champ commençant par un trait de soulignement. Le champ doit être renommé. Pour cela, utilisez la propriété `fieldMappings` de l'indexeur comme suit : 

    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

Vous pouvez spécifier plusieurs mappages de champs. 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

Les noms de champs sources et cibles sont sensibles à la casse.

<a name="FieldMappingFunctions"></a>
***Fonctions de mappage de champs***

Les mappages de champs peuvent également être utilisés pour transformer des valeurs de champs source à l'aide de *fonctions de mappage*.

Seule une de ces fonctions est actuellement prise en charge : `jsonArrayToStringCollection`. Elle analyse un champ qui contient une chaîne formatée sous forme de tableau JSON dans un champ Collection(Edm.String) dans l'index cible. Elle est conçue pour une utilisation avec l'indexeur SQL Azure en particulier, car SQL ne dispose pas d'un type de données de collection natif. Elle peut être utilisée comme suit : 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Par exemple, si le champ source contient la chaîne de caractères `["red", "white", "blue"]`, le champ cible de type `Collection(Edm.String)` sera rempli avec les valeurs `"red"`, `"white"` et `"blue"`.

Notez que la propriété `targetFieldName` est facultative. Si elle n'est pas définie, la valeur `sourceFieldName` est utilisée. 

<a name="CreateIndexerRequestExamples"></a>
**Exemples de corps de requête**

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

Pour une requête réussie : « 201 Créé ».

<a name="UpdateIndexer"></a>

## <a name="update-indexer"></a>Mise à jour d'un indexeur
Vous pouvez mettre à jour un indexeur existant à l'aide d'une requête HTTP PUT. Vous spécifiez le nom de l'indexeur à mettre à jour dans l'URI de la requête :

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Le paramètre `api-version` est obligatoire. La version actuelle est `2015-02-28`. 

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

**Requête**

La syntaxe du corps de la requête est la même que pour les [requêtes Create Indexer](#CreateIndexerRequestSyntax).

**Réponse**

Pour une requête réussie : 201 Créé si un indexeur a été créé, et 204 Pas de contenu si un indexeur existant a été mis à jour.

<a name="ListIndexers"></a>

## <a name="list-indexers"></a>Liste des indexeurs
L'opération **List Indexers** renvoie la liste des indexeurs utilisés dans votre service Azure Search. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


Le paramètre `api-version` est obligatoire. La version préliminaire est `2015-02-28-Preview`. [Contrôle de version Azure Search](https://msdn.microsoft.com/library/azure/dn864560.aspx) .

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

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

Notez que vous pouvez filtrer la réponse de manière à afficher uniquement les propriétés qui vous intéressent. Par exemple, si vous voulez uniquement une liste de noms d'indexeurs, utilisez l'option de requête OData `$select` :

    GET /indexers?api-version=2014-10-20-Preview&$select=name

Dans ce cas, la réponse de l'exemple ci-dessus apparaît comme suit : 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Cette technique est utile pour économiser de la bande passante si votre service Search contient un grand nombre d'indexeurs.

<a name="GetIndexer"></a>

## <a name="get-indexer"></a>Obtention d'indexeur
L'opération d'obtention d'indexeur **Get Indexer** obtient la définition d'indexeur auprès d'Azure Search.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Le paramètre `api-version` est obligatoire. La version préliminaire est `2015-02-28-Preview`. 

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

**Réponse**

Code d'état : 200 OK est retourné pour une réponse correcte.

La réponse est similaire aux exemples dans [Exemple de requêtes Create Indexer](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>

## <a name="delete-indexer"></a>Suppression d'indexeur
L'opération de suppression d'un indexeur ( **Delete Indexer** ) supprime un indexeur de votre service Azure Search.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Quand un indexeur est supprimé, les exécutions d'indexeur en cours sont exécutées complètement, mais aucune autre exécution n'est planifiée. Les tentatives d'utilisation d'un indexeur inexistant génèrent le code d'état HTTP 404 Introuvable. 

Le paramètre `api-version` est obligatoire. La version préliminaire est `2015-02-28-Preview`. 

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

**Réponse**

Code d'état : 204 Pas de contenu est renvoyé en cas de réponse correcte.

<a name="RunIndexer"></a>

## <a name="run-indexer"></a>Exécuter l'indexeur
En plus de l'exécution périodique planifiée, un indexeur peut également être appelé à la demande via l'opération **Run Indexer** : 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

Le paramètre `api-version` est obligatoire. La version préliminaire est `2015-02-28-Preview`. 

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

**Réponse**

Code d'état : 202 Accepté est retourné en cas de réponse correcte.

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Get Indexer Status
L'opération d'obtention de l'état de l'indexeur ( **Get Indexer Status** ) récupère l'état actuel et l'historique d'exécution d'un indexeur : 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


Le paramètre `api-version` est obligatoire. La version préliminaire est `2015-02-28-Preview`. 

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

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

Les valeurs possibles pour l’état de l’indexeur sont les suivantes :

* `running` Indique que l'indexeur s'exécute normalement. Notez que, comme certaines exécutions de l'indexeur peuvent encore échouer, nous recommandons de vérifier également la propriété `lastResult` . 
* `error` Indique que l'indexeur a rencontré une erreur qui ne peut pas être corrigée sans intervention humaine. Par exemple, il se peut que les informations d'identification de la source de données aient expiré, ou que le schéma de la source de données ou de l'index cible ait subitement changé. 

**Résultat d'exécution de l'indexeur**

Un résultat d'exécution de l'indexeur contient des informations sur une seule exécution de l'indexeur. Le dernier résultat est présenté comme la propriété `lastResult` de l'état de l'indexeur. Les autres résultats récents éventuels sont renvoyés en tant que propriété `executionHistory` de l'état de l'indexeur. 

Le résultat d'exécution de l'indexeur contient les propriétés suivantes : 

* `status`: état d'une exécution. Pour plus d'informations, consultez [État d'exécution de l'indexeur](#IndexerExecutionStatus) ci-dessous. 
* `errorMessage`: message d'erreur pour un échec d'exécution. 
* `startTime`: heure UTC à laquelle cette exécution a commencé.
* `endTime`: heure UTC à laquelle cette exécution s'est achevée. Cette valeur n'est pas définie si l'exécution est encore en cours.
* `errors`: tableau d’éventuelles erreurs au niveau des éléments. Chaque entrée contient une clé de document (`key` propriété) et un message d'erreur (`errorMessage` propriété). 
* `itemsProcessed`: nombre d'éléments de source de données (par exemple, lignes de table) que l'indexeur a tenté d'indexer durant cette exécution. 
* `itemsFailed` : nombre d'éléments dont l'exécution a échoué au cours de cette opération.  
* `initialTrackingState` : toujours `null` pour la première exécution de l'indexeur, ou si la stratégie de suivi des modifications des données n'est pas activée dans la source de données utilisée. Si une telle stratégie est activée, cette valeur est, lors des exécutions suivantes, la première valeur (la plus basse) de suivi des modifications traitée au cours cette exécution. 
* `finalTrackingState` : toujours `null` si la stratégie de suivi des modifications des données n'est pas activée dans la source de données utilisée. Sinon, indique la dernière valeur (la plus élevée) de suivi des modifications correctement traitée par cette exécution. 

<a name="IndexerExecutionStatus"></a>
**État d’exécution de l’indexeur**

L'état d'exécution de l'indexeur reflète l'état d'une seule exécution. Il peut avoir les valeurs suivantes :

* `success` indique que l'exécution de l'indexeur s'est terminée correctement.
* `inProgress` indique que l'exécution de l'indexeur est en cours. 
* `transientFailure` indique qu'une exécution de l'indexeur a échoué. Pour plus d'informations, consultez la propriété `errorMessage`. Il se peut de l'échec nécessite une intervention humaine pour le corriger. Par exemple, la correction d'une incompatibilité de schéma entre la source de données et l'index cible requiert une action de l'utilisateur, contrairement à un temps d'arrêt temporaire de la source de données. Les appels de l'indexeur continuent conformément à la planification, si celle-ci est définie. 
* `persistentFailure` indique un échec de l'indexeur nécessitant probablement une intervention humaine. Les exécutions planifiées de l'indexeur s'arrêtent. Après avoir corrigé le problème, utilisez l'API Reset Indexer pour redémarrer les exécutions planifiées. 
* `reset` indique que l'indexeur a été réinitialisé par un appel à l'API Reset Indexer (voir ci-dessous). 

<a name="ResetIndexer"></a>

## <a name="reset-indexer"></a>Réinitialisation de l'indexeur
L'opération de réinitialisation de l'indexeur ( **Reset Indexer** ) réinitialise l'état de suivi des modifications associé à l'indexeur. Cela vous permet de déclencher une réindexation complète (par exemple, si votre schéma de source de données a changé) ou de modifier la stratégie de détection des modifications de données pour une source de données associée à l'indexeur.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

Le paramètre `api-version` est obligatoire. La version préliminaire est `2015-02-28-Preview`. 

La clé `api-key` doit être une clé d'administration (par opposition à une clé de requête). Pour plus d'informations sur les clés, consultez la section relative à l'authentification dans [API REST de service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) . [Création d'un service Search dans le portail](search-create-service-portal.md) (en anglais) indique comment obtenir l'URL du service et les propriétés de clé utilisées dans la requête.

**Réponse**

Code d'état : 204 Pas de contenu en cas de réponse correcte.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mappage entre les types de données SQL et les types de données Azure Search
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
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>Consultez [Fonctions de mappage de champs](#FieldMappingFunctions) dans le présent document pour plus de détails sur la transformation d’une colonne au format chaîne en Collection(Edm.String)</td>
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
<td>Geography</td>
<td>Edm.GeographyPoint</td>
<td>Seules les instances Geography de type POINT avec SRID 4326 (valeur par défaut) sont prises en charge</td>
</tr>
<tr>
<td>rowversion</td>
<td>N/A</td>
<td>Les colonnes de version de ligne ne peuvent pas être stockées dans l'index de recherche, mais peuvent être utilisées pour le suivi des modifications</td>
</tr>
<tr>
<td>time, timespan<br>binary, varbinary, image<br>xml, geometry, types CLR</td>
<td>N/A</td>
<td>Non pris en charge</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mappage entre les types de données JSON et les types de données Azure Search
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
<td></td>
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
<td>Tableaux de types primitifs, par exemple [ « a », « b », « c » ]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>Chaînes qui ressemblent à des dates</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Objets point GeoJSON</td>
<td>Edm.GeographyPoint</td>
<td>Les points GeoJSON sont des objets JSON au format suivant : { "type" : "Point", "coordonnées" : [long, lat] } </td>
</tr>
<tr>
<td>Autres objets JSON</td>
<td>N/A</td>
<td>Non pris en charge. Azure Search prend actuellement en charge uniquement les types primitifs et les collections de chaînes de caractères</td>
</tr>
</table>

