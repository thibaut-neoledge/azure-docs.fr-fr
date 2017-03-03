---
title: "Connexion d’Azure SQL Database à la Recherche Azure à l’aide d’indexeurs | Microsoft Docs"
description: "Découvrez comment extraire des données d’Azure SQL Database à un index Azure à l&quot;aide d&quot;indexeurs."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: e9bbf352-dfff-4872-9b17-b1351aae519f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 0841744b806f3dba38dddee21fb7fe881e07134f
ms.openlocfilehash: 51c9d9afb6c2ed460abd4c47a6afbc404b97a85e
ms.lasthandoff: 02/16/2017

---

# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Connexion d'Azure SQL Database à Azure Search à l'aide d'indexeurs
Le service Azure Search est un service de recherche cloud hébergé qui facilite l'offre d'une expérience de recherche exceptionnelle. Avant de pouvoir exécuter une recherche, vous devez remplir un index Azure Search avec vos données. Si les données se trouvent dans une base de données Azure SQL Database, le nouvel **indexeur Recherche Azure pour Azure SQL Database** (ou **indexeur Azure SQL**) peut automatiser le processus d’indexation. En d’autres termes, vous avez moins de code à écrire et la maintenance de l’infrastructure est moins lourde.

Cet article décrit l’utilisation des indexeurs, mais aussi les fonctionnalités propres aux bases de données Azure SQL Database (par exemple, le suivi intégré des modifications). La Recherche Azure prend également en charge d’autres sources de données, comme Azure DocumentDB, le Stockage Blob et le Stockage Table. Si vous voulez voir comment d’autres sources de données sont prises en charge, indiquez vos souhaits sur le [forum de la Recherche Azure](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexeurs et sources de données
Vous pouvez installer et configurer un indexeur SQL Azure avec les outils suivants :

* Assistant Importation de données sur le [Portail Azure](https://portal.azure.com)
* [Kit de développement logiciel .NET (SDK)](https://msdn.microsoft.com/library/azure/dn951165.aspx) de la Recherche Azure
* [API REST](http://go.microsoft.com/fwlink/p/?LinkID=528173) de la Recherche Azure

Dans cet article, nous allons utiliser l’API REST pour vous montrer comment créer et gérer des **indexeurs** et des **sources de données**.

Une **source de données** spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données et les stratégies qui identifient efficacement les changements dans les données (telles que des lignes modifiées ou supprimées). Elle est définie comme une ressource indépendante utilisable par plusieurs indexeurs.

Un **indexeur** est une ressource qui connecte des sources de données à des index de recherche cibles. Un indexeur est utilisé pour :

* effectuer une copie unique des données pour remplir un index ;
* Synchroniser un index avec les modifications apportées à la source de données selon une planification donnée.
* S’exécuter à la demande afin de mettre à jour un index en fonction des besoins.

## <a name="when-to-use-azure-sql-indexer"></a>Quand utiliser l’indexeur Azure SQL 
Selon plusieurs facteurs relatifs à vos données, l'utilisation de l'indexeur Azure SQL peut être ou ne pas être appropriée. Si vos données répondent aux conditions suivantes, vous pouvez utiliser l'indexeur Azure SQL :

* Toutes les données proviennent d'une seule table ou d’une seule vue.
  * Si les données sont disséminées entre plusieurs tables, vous pouvez créer une vue et l'utiliser avec l'indexeur. Toutefois, si vous utilisez une vue, vous ne pourrez plus utiliser la fonction intégrée de détection des modifications de SQL Server. Pour plus d’informations, consultez [cette section](#CaptureChangedRows).
* L’indexeur prend en charge les types de données utilisés dans la source de données. Mais certains types SQL ne sont pas pris en charge. Pour plus d'informations, consultez la section [Mappage des types de données dans Azure Search](http://go.microsoft.com/fwlink/p/?LinkID=528105).
* Il n’est pas utile de mettre à jour l’index en quasi-temps réel, lorsqu'une ligne est modifiée.
  * L'indexeur peut réindexer votre table toutes les 5 minutes au plus. Si vos données changent fréquemment et si les modifications doivent être intégrées dans l'index en quelques secondes ou minutes, nous vous recommandons d'utiliser l’ [API d’index Azure Search](https://msdn.microsoft.com/library/azure/dn798930.aspx) directement.
* Si vous avez un jeu de données important et que vous comptez exécuter l'indexeur selon une planification, votre schéma nous permet d'identifier efficacement les lignes modifiées (et éventuellement supprimées). Pour plus d’informations, consultez la section « Capture des lignes modifiées et supprimées ».
* La taille des champs indexés sur une ligne ne dépasse pas la taille maximale d'une requête d’indexation Azure Search, soit 16 Mo.

## <a name="create-and-use-an-azure-sql-indexer"></a>Créer et utiliser un indexeur Azure SQL
Commencez par créer la source de données :

    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


Vous pouvez obtenir la chaîne de connexion auprès du [portail Azure Classic](https://portal.azure.com). Utilisez l’option `ADO.NET connection string`.

Ensuite, créez l’index Azure Search cible si vous n’en avez pas un. Pour créer un index, utilisez [l’interface utilisateur du portail](https://portal.azure.com) ou [l’API de création d’index](https://msdn.microsoft.com/library/azure/dn798941.aspx). Vérifiez que le schéma de votre index cible est compatible avec le schéma de la table source. Consultez [Mappage entre les types de données SQL et les types de données de Recherche Azure](#TypeMapping).

Enfin, créez l'indexeur en lui attribuant un nom et en référençant les sources de données source et cible :

    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Un indexeur créé de cette façon n’a pas de planification. Il s’exécute automatiquement une fois créé. Vous pouvez le réexécuter à tout moment à l'aide d’une requête **run indexer** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

Vous pouvez personnaliser différents aspects du comportement des indexeurs, notamment la taille du lot et le nombre de documents pouvant être ignorés avant que l’exécution d’un indexeur n’échoue. Pour plus d’informations, consultez [Créer une API d’indexeur](https://msdn.microsoft.com/library/azure/dn946899.aspx).

Il se peut que vous deviez autoriser des services Azure pour vous connecter à votre base de données. Pour plus d’informations sur la marche à suivre, consultez la section [Connexion à partir de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) .

Pour surveiller l’état et l’historique d’exécution de l'indexeur (nombre d’éléments indexés, échecs, etc.), utilisez une requête **indexer status** :

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

La réponse doit être semblable à ce qui suit :

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

L'historique d'exécution contient jusqu’à 50 exécutions les plus récentes, classées par ordre antichronologique (la dernière exécution apparaît en premier dans la réponse).
Vous trouverez des informations supplémentaires sur la réponse dans [Obtenir l’état de l’indexeur](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Exécuter des indexeurs selon une planification
Vous pouvez également configurer l'indexeur pour qu’il s’exécute à intervalles périodiques. Pour ce faire, ajoutez la propriété **schedule** lors de la création ou de la mise à jour de l’indexeur. L'exemple ci-dessous montre une requête PUT mettant à jour l'indexeur :

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Le paramètre **interval** est obligatoire. Il correspond à la durée entre le début de deux exécutions consécutives de l’indexeur. L'intervalle minimal autorisé est de 5 minutes, l'intervalle maximal autorisé est d'une journée. Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle est le suivant : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` toutes les 15 minutes, `PT2H` toutes les deux heures.

Le paramètre **startTime** facultatif indique quand les exécutions planifiées doivent commencer. S’il est omis, l’heure UTC actuelle est utilisée. Cette heure peut être passée, auquel cas la première exécution est planifiée comme si l’indexeur s’exécutait en continu depuis l’heure de début.  

L’indexeur ne peut s’exécuter qu’une seule fois simultanément. Si un indexeur est en cours d’exécution au moment de son exécution planifiée, celle-ci est différée jusqu’à la prochaine date planifiée.

Pour être plus clair, prenons un exemple. Supposons que nous avons configuré la planification suivante :

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Voici ce qui se passe :

1. La première exécution de l'indexeur commence à ou autour du 1er mars 2015 à 0 heure UTC.
2. Supposons que cette exécution prend 20 minutes (ou en tout cas, moins de 1 heure).
3. La deuxième exécution commence à ou autour du 1er mars 2015 à 1 heure.
4. Supposons maintenant que cette exécution dure plus d’une heure, par exemple, 70 minutes, et se termine à environ 2 h 10.
5. Il est maintenant 2 h 00, heure du début de la troisième exécution. Cependant, comme la deuxième exécution démarrée à 1 heure est toujours en cours d’exécution, la troisième exécution est ignorée. Elle commence à 3 h 00.

Vous pouvez ajouter, modifier ou supprimer une planification d’indexeur en utilisant une requête **PUT indexer** .

<a name="CaptureChangedRows"></a>

## <a name="capturing-new-changed-and-deleted-rows"></a>Capture de lignes nouvelles, modifiées et supprimées
Si votre table contient de nombreuses lignes, vous devez utiliser une stratégie de détection des modifications de données. La détection des modifications permet une récupération efficace des lignes nouvelles ou modifiées uniquement, sans qu’il soit nécessaire de réindexer l’ensemble de la table.

### <a name="sql-integrated-change-tracking-policy"></a>Stratégie de suivi intégré des modifications SQL
Si votre base de données SQL prend en charge le [suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx), nous recommandons d'utiliser la **stratégie de suivi intégré des modifications SQL**. Il s’agit de la stratégie la plus efficace. De plus, elle permet à la Recherche Azure d’identifier les lignes supprimées, sans avoir à ajouter une colonne « suppression réversible » explicite à votre table.

Le suivi intégré des modifications est pris en charge à partir des versions de base de données SQL Server suivantes :

* SQL Server 2008 R2 et versions ultérieures, si vous utilisez SQL Server on Azure VMs.
* Azure SQL Database V12, si vous utilisez Azure SQL Database.

Si vous utilisez le suivi intégré des modifications SQL, ne spécifiez pas une stratégie de détection des lignes supprimées. Elle intègre la prise en charge de l'identification des lignes supprimées.

Elle n’est utilisable que sur des tables, non sur des vues. Pour pouvoir appliquer cette stratégie, vous devez activer le suivi des modifications sur la table. Pour plus d’informations, consultez la section [Activer et désactiver le suivi des modifications](https://msdn.microsoft.com/library/bb964713.aspx) .

Pour utiliser cette stratégie, créez ou mettez à jour votre source de données comme suit :

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Stratégie de détection des modifications de limite supérieure
Si la stratégie de suivi intégré des modifications SQL est recommandée, elle n’est utilisable qu’avec des tables, et non avec des vues. Si vous utilisez une vue, essayez la stratégie de limite supérieure. Celle-ci est applicable si votre table ou votre vue contient une colonne qui répond aux critères suivants :

* Toutes les insertions spécifient une valeur pour la colonne.
* Toutes les mises à jour d'un élément modifient également la valeur de la colonne.
* La valeur de cette colonne augmente à chaque insertion ou mise à jour.
* Les requêtes utilisant les clauses WHERE et ORDER BY suivantes peuvent être exécutées efficacement : `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`.

> [!IMPORTANT] 
> Nous vous recommandons fortement d’utiliser une colonne **rowversion** pour le suivi des modifications. Si un autre type de données est utilisé, il n’est pas garanti que le suivi des modifications capture toutes les modifications en présence de transactions qui s’exécutent en même temps qu’une requête de l’indexeur.

Pour utiliser une stratégie de limite supérieure, créez ou mettez à jour votre source de données comme suit :

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Si la table source n’a pas d’index dans la colonne de limite supérieure, les requêtes utilisées par l’indexeur SQL risquent d’expirer. En particulier, la clause `ORDER BY [High Water Mark Column]` a besoin d’un index pour pouvoir s’exécuter efficacement lorsque la table contient de nombreuses lignes.
>
>

Si vous rencontrez des erreurs de temporisation, vous pouvez utiliser le paramètre de configuration d’indexeur `queryTimeout` pour donner une valeur plus élevée que les 5 minutes par défaut au délai d’expiration de la requête. Par exemple, pour fixer un délai d’expiration de 10 minutes, créez ou mettez à jour l’indexeur avec la configuration suivante :

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Vous pouvez également désactiver la clause `ORDER BY [High Water Mark Column]`. Toutefois, cette action est déconseillée car, si l’exécution de l’indexeur est interrompue par une erreur, l’indexeur doit traiter à nouveau toutes les lignes quand son exécution reprend, même s’il avait déjà traité la quasi-totalité des lignes au moment de l’interruption. Pour désactiver la clause `ORDER BY`, utilisez le paramètre `disableOrderByHighWaterMarkColumn` dans la définition de l’indexeur :  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Stratégie de détection des colonnes à suppression réversible
Lorsque des lignes sont supprimées de la table source, vous devez également supprimer ces lignes de l'index de recherche. Si vous utilisez la stratégie de suivi intégré des modifications SQL, cette opération est prise en charge à votre place. Mais la stratégie de suivi des modifications de limite supérieure ne vous est d’aucune aide pour les lignes supprimées. Que faire, alors ?

Si des lignes sont physiquement supprimées de la table, la Recherche Azure n’a aucun moyen de déduire la présence d’enregistrements qui n’existent plus.  Toutefois, vous pouvez utiliser la technique de la « suppression réversible » pour supprimer des lignes logiquement sans les supprimer de la table. Ajoutez une colonne à votre table ou votre vue et marquez les lignes comme supprimées à l’aide de cette colonne.

Lorsque vous utilisez la technique de suppression réversible, vous pouvez spécifier cette stratégie réversible comme suit lors de la création ou de la mise à jour de la source de données :

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**softDeleteMarkerValue** doit être une chaîne. Utilisez la représentation au format chaîne de votre valeur. Par exemple, si vous avez une colonne d’entiers dans laquelle les lignes supprimées sont marquées avec la valeur 1, utilisez `"1"`. Si vous avez une colonne BIT dans laquelle les lignes supprimées sont marquées avec la valeur booléenne True, utilisez `"True"`.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mappage entre les types de données SQL et les types de données Azure Search
| Type de données SQL | Types de champs d'index cible autorisés | Remarques |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, money decimal numeric |Edm.String |Azure Search ne prend pas en charge la conversion de types décimaux en Edm.Double, car elle entraîne une perte de précision |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Une chaîne SQL peut être utilisée pour remplir un champ Collection(Edm.String) si la chaîne représente un tableau JSON de chaînes : `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Geography |Edm.GeographyPoint |Seules les instances Geography de type POINT avec SRID 4326 (valeur par défaut) sont prises en charge |
| rowversion |N/A |Les colonnes de version de ligne ne peuvent pas être stockées dans l'index de recherche, mais peuvent être utilisées pour le suivi des modifications |
| time, timespan, binary, varbinary, image, xml, geometry, types CLR |N/A |Non pris en charge |

## <a name="configuration-settings"></a>Paramètres de configuration
L’indexeur SQL expose plusieurs paramètres de configuration :

| Paramètre | Type de données | Objectif | Valeur par défaut |
| --- | --- | --- | --- |
| queryTimeout |string |Définit le délai d’expiration de l’exécution de la requête SQL |5 minutes ("00:05:00") |
| disableOrderByHighWaterMarkColumn |valeur booléenne |Indique que la requête SQL utilisée par la stratégie de limite supérieure doit omettre la clause ORDER BY. Consultez [Stratégie de limite supérieure](#HighWaterMarkPolicy) |false |

Ces paramètres sont utilisés dans l’objet `parameters.configuration`, dans la définition de l’indexeur. Par exemple, pour fixer un délai d’expiration de la requête de 10 minutes, créez ou mettez à jour l’indexeur avec la configuration suivante :

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>Forum Aux Questions
**Q. :** Puis-je utiliser l’indexeur SQL Azure avec des bases de données SQL exécutées sur des machines virtuelles IaaS dans Azure ?

R. : Oui. Toutefois, vous devez autoriser votre service de recherche à se connecter à votre base de données. Pour plus d’informations, consultez l’article [Configurer une connexion d’un indexeur de Recherche Azure à SQL Server sur une machine virtuelle Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**Q. :** Puis-je utiliser l’indexeur Azure SQL avec des bases de données SQL exécutées localement ?

R. : Cette opération n’est pas prise en charge, ni recommandée, car elle vous oblige à ouvrir vos bases de données au trafic Internet.

**Q. :** Puis-je utiliser l’indexeur Azure SQL avec des bases de données autres que SQL Server exécutées en IaaS sur Azure ?

R. : Ce cas de figure n’est pas pris en charge, car nous n'avons pas testé l'indexeur avec des bases de données autres que SQL Server.  

**Q. :** Puis-je créer plusieurs indexeurs qui s’exécutent selon une planification ?

R. : Oui. Cependant, seul un indexeur peut s'exécuter sur un nœud à la fois. Si vous avez besoin d’exécuter plusieurs indexeurs simultanément, envisagez d’ajouter d’autres unités de recherche à votre service de recherche.

**Q. :** L’exécution d’un indexeur affecte-t-il la charge de travail de mes requêtes ?

R. : Oui. L’indexeur s'exécute sur un des nœuds de votre service de recherche, et les ressources de ce nœud sont partagées entre l'indexation et le traitement du trafic de requêtes d’une part, et d’autres requêtes d’API d’autre part. Si vous exécutez des charges de travail intensives d'indexation et de requête et que vous rencontrez un taux élevé d'erreurs 503 ou des délais de réponse croissants, redimensionnez votre service de recherche.

