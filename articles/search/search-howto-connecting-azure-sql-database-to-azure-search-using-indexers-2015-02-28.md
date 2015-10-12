<properties 
	pageTitle="Connexion d’Azure SQL Database à Azure Search à l’aide d’indexeurs" 
	description="Découvrez comment extraire des données d’Azure SQL Database à un index Azure à l'aide d'indexeurs." 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/29/2015" 
	ms.author="eugenesh"/>

#Connexion d’Azure SQL Database à Azure Search à l’aide d’indexeurs

Le service Azure Search permet d’obtenir facilement une expérience de recherche exceptionnelle, mais pour pouvoir effectuer des recherches, vous devez créer un index Azure Search avec vos données. Si les données résident dans une base de données SQL Azure, la nouvelle fonctionnalité d’**indexeur Azure Search pour Azure SQL Database** (ou **indexeur SQL Azure**) dans Azure Search peut automatiser le processus d'indexation. En d’autres termes, vous avez moins de code à écrire et la maintenance de l’infrastructure est moins lourde.

Actuellement, les indexeurs ne fonctionnent qu’avec Azure SQL Database, SQL Server on Azure VMs et [Azure DocumentDB](../documentdb/documentdb-search-indexer.md). Dans cet article, nous allons nous concentrer sur les indexeurs qui fonctionnent avec Azure SQL Database. Si vous voulez voir comment d’autres sources de données sont prises en charge, indiquez vos souhaits sur le [forum Azure Search](http://feedback.azure.com/forums/263029-azure-search).

Cet article décrit l’utilisation des indexeurs, mais nous allons également examiner les fonctionnalités et comportements propres aux bases de données SQL (par exemple, le suivi intégré des modifications).

## Indexeurs et sources de données ##

Pour installer et configurer l’indexeur Azure SQL, vous pouvez appeler l'[API REST d’Azure Search](http://go.microsoft.com/fwlink/p/?LinkID=528173) afin de créer et de gérer des **indexeurs** et des **sources de données**.

Vous pouvez également utiliser la [classe Indexeur](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) du [SDK .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx), ou l'Assistant d’importation des données du [portail Azure](https://portal.azure.com) pour créer et planifier un indexeur.

Une **source de données** spécifie les données à indexer, les informations d'identification nécessaires pour accéder aux données et les stratégies qui permettent à Azure Search d'identifier efficacement les changements dans les données (telles que des lignes modifiées ou supprimées). Elle est définie comme une ressource indépendante utilisable par plusieurs indexeurs.

Un **indexeur** est une ressource qui connecte des sources de données à des index de recherche cibles. Un indexeur est utilisé pour :
 
- Effectuer une copie unique des données pour remplir un index.
- Synchroniser un index avec les modifications apportées à la source de données selon une planification donnée.
- S’exécuter à la demande afin de mettre à jour un index en fonction des besoins. 

## Quand utiliser l’indexeur Azure SQL ##

Selon plusieurs facteurs relatifs à vos données, l'utilisation de l'indexeur Azure SQL peut être ou ne pas être appropriée. Si vos données répondent aux conditions suivantes, vous pouvez utiliser l'indexeur Azure SQL :

- Toutes les données proviennent d'une seule table ou d’une seule vue.
	- Si les données sont disséminées entre plusieurs tables, vous pouvez créer une vue et l'utiliser avec l'indexeur. Toutefois, sachez que si vous utilisez une vue, vous ne pourrez plus utiliser la fonction intégrée de détection des modifications de SQL Server. Pour plus d’informations, consultez cette section. 
- L’indexeur prend en charge les types de données utilisés dans la source de données. Mais certains types SQL ne sont pas pris en charge. Pour plus d'informations, consultez la section [Mappage des types de données dans Azure Search](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Il n’est pas utile de mettre à jour l’index en quasi-temps réel, lorsqu'une ligne est modifiée. 
	- L'indexeur peut réindexer votre table toutes les 5 minutes au plus. Si vos données changent fréquemment et si les modifications doivent être intégrées dans l'index en quelques secondes ou minutes, nous vous recommandons d'utiliser l’[API d’index Azure Search](https://msdn.microsoft.com/library/azure/dn798930.aspx) directement. 
- Si vous avez un jeu de données important et que vous comptez exécuter l'indexeur selon une planification, votre schéma nous permet d’identifier efficacement les lignes modifiées (et éventuellement supprimées). Pour plus d’informations, consultez la section « Capture des lignes modifiées et supprimées ». 
- La taille des champs indexés sur une ligne ne dépasse pas la taille maximale d'une requête d’indexation Azure Search, soit 16 Mo. 

## Créer et utiliser un indexeur Azure SQL ##

Commencez par créer la source de données :

	POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
	    "container" : { "name" : "name of the table or view that you want to index" }
	}


Récupérez la chaîne de connexion sur le [portail Azure](https://portal.azure.com). Utilisez l’option `ADO.NET connection string`.

Ensuite, créez l’index Azure Search cible si vous n’en avez pas un. Pour ce faire, utilisez l'[interface utilisateur du portail](https://portal.azure.com) ou l’[API de création d’index](https://msdn.microsoft.com/library/azure/dn798941.aspx). Assurez-vous que le schéma de votre index cible est compatible avec celui de la table source. Consultez le tableau suivant pour le mappage entre les types de données SQL et Azure Search.

**Mappage entre les types de données SQL et les types de données Azure Search**

|Type de données SQL | Types de champs d'index cible autorisés |Remarques 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, money decimal numeric | Edm.String| Azure Search ne prend pas en charge la conversion de types décimaux en Edm.Double, car elle entraîne une perte de précision |
| char, nchar, varchar, nvarchar | Edm.String<br/>Collection(Edm.String)|La transformation d’une colonne de chaîne en Collection(Edm.String) requiert l’utilisation d’une API d’aperçu version 2015-02-28-Preview. Consultez [cet article](search-api-indexers-2015-02-28-Preview.md#create-indexer) pour plus de détails.| 
|smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|Geography | Edm.GeographyPoint | Seules les instances Geography de type POINT avec SRID 4326 (valeur par défaut) sont prises en charge | | 
|rowversion| N/A |Les colonnes de version de ligne ne peuvent pas être stockées dans l'index de recherche, mais peuvent être utilisées pour le suivi des modifications | |
| time, timespan, binary, varbinary, image, xml, geometry, types CLR | N/A |Non pris en charge |

Enfin, créez l'indexeur en lui attribuant un nom et en référençant les sources de données source et cible :

	POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myindexer",
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name"
	}

Un indexeur créé de cette façon n’a pas de planification. Il s'exécute automatiquement une fois dès qu'il est créé. Vous pouvez le réexécuter à tout moment à l'aide d’une requête **run indexer** :

	POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
	api-key: admin-key
 
Il se peut que vous deviez autoriser des services Azure pour vous connecter à votre base de données. Pour plus d’informations sur la marche à suivre, consultez la section [Connexion à partir de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure).

Pour surveiller l’état et l’historique d’exécution de l'indexeur (nombre d’éléments indexés, échecs, etc.), utilisez une requête **indexer status** :

	GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
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

L'historique d'exécution contient jusqu’à 50 exécutions les plus récentes, classées par ordre antichronologique (la dernière exécution apparaît en premier dans la réponse). Vous trouverez des informations supplémentaires sur la réponse dans [Obtenir l’état de l’indexeur](http://go.microsoft.com/fwlink/p/?LinkId=528198).

## Exécuter des indexeurs selon une planification ##

Vous pouvez également configurer l'indexeur pour qu’il s’exécute à intervalles périodiques. Pour ce faire, il suffit d'ajouter la propriété **schedule** lors de la création ou de la mise à jour de l'indexeur. L'exemple ci-dessous montre une requête PUT mettant à jour l'indexeur :

	PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key 

	{ 
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name",
	    "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
	}

Le paramètre **interval** est obligatoire. Il correspond à la durée entre le début de deux exécutions consécutives de l’indexeur. L'intervalle minimal autorisé est de 5 minutes, l'intervalle maximal autorisé est d'une journée. Il doit être formaté en tant que valeur « dayTimeDuration » XSD (un sous-ensemble limité d'une valeur de [durée ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Le modèle est le suivant : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` toutes les 15 minutes, `PT2H` toutes les deux heures.

Le paramètre **startTime** facultatif indique quand les exécutions planifiées doivent commencer ; s'il est omis, l'heure UTC actuelle est utilisée. Cette heure peut être passée, auquel cas la première exécution est planifiée comme si l'indexeur s’exécutait en continu depuis l'heure de début.

L’indexeur ne peut s'exécuter qu’une seule fois simultanément. Si un indexeur est en cours d’exécution lorsque le suivant est censé démarrer, l'exécution est ignorée et reprend à l'intervalle suivant, en supposant qu'aucun autre indexeur est en cours d'exécution.

Pour être plus clair, prenons un exemple. Supposons que nous avons configuré la planification suivante :

	"schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Voici ce qui se passe :

1. La première exécution de l'indexeur commence à ou autour du 1er mars 2015 à 0 heure UTC.
1. Supposons que cette exécution prend 20 minutes (ou en tout cas, moins de 1 heure).
1. La deuxième exécution commence à ou autour du 1er mars 2015 à 1 heure. 
1. Supposons maintenant que cette exécution dure plus d'une heure (ce qui nécessiterait un nombre important de documents, mais passons...) – par exemple, 70 minutes – de sorte qu'elle se termine à environ 2 h 10.
1. Il est maintenant 2 h 00, heure du début de la troisième exécution. Mais, comme la deuxième exécution débutée à 1 h est toujours en cours, la troisième est ignorée. Elle commence à 3 h 00.

Vous pouvez ajouter, modifier ou supprimer une planification d’indexeur en utilisant une requête **PUT indexer**.

## Capture des lignes nouvelles, modifiées et supprimées ##

Si vous utilisez une planification et que votre table contient un nombre non négligeable de lignes, vous devez utiliser une stratégie de détection des données modifiées, afin que l'indexeur ne récupère que les lignes nouvelles ou modifiées sans avoir à réindexer la table entière.

### Stratégie de suivi intégré des modifications SQL ###

Si votre base de données SQL prend en charge le [suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx), nous recommandons d'utiliser la **stratégie de suivi intégré des modifications SQL**. Cette stratégie assure le suivi des modifications le plus efficace et permet à Azure Search d'identifier les lignes supprimées, sans avoir à ajouter une colonne explicite à « suppression réversible » à votre table.

Le suivi intégré des modifications est pris en charge à partir des versions de base de données SQL Server suivantes :
 
- SQL Server 2008 R2 et versions ultérieures, si vous utilisez SQL Server on Azure VMs. 
- Azure SQL Database V12, si vous utilisez Azure SQL Database.

Si vous utilisez le suivi intégré des modifications SQL, ne spécifiez pas une stratégie de détection des lignes supprimées. Elle intègre la prise en charge de l'identification des lignes supprimées.

Elle n’est utilisable que sur des tables, non sur des vues. Pour pouvoir appliquer cette stratégie, vous devez activer le suivi des modifications sur la table. Pour plus d’informations, consultez la section [Activer et désactiver le suivi des modifications](https://msdn.microsoft.com/library/bb964713.aspx).

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

### Stratégie de détection des modifications de limite supérieure ###

Lorsque la stratégie de suivi intégré des modifications SQL est recommandée, vous ne pourrez pas l'utiliser si vos données sont dans une vue ou si vous utilisez une version antérieure de la base de données SQL Azure. Dans ce cas, essayez la stratégie de limite supérieure. Celle-ci est applicable si votre table contient une colonne qui répond aux critères suivants :

- Toutes les insertions spécifient une valeur pour la colonne. 
- Toutes les mises à jour d'un élément modifient également la valeur de la colonne. 
- La valeur de cette colonne augmente à chaque modification.
- Les requêtes qui utilisent une clause `WHERE` similaire à la clause `WHERE [High Water Mark Column] > [Current High Water Mark Value]` peuvent s’exécuter efficacement.

Par exemple, une colonne **rowversion** indexée est un candidat idéal pour la colonne de limite supérieure. Pour utiliser cette stratégie, créez ou mettez à jour votre source de données comme suit :

	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "connection string" },
	    "container" : { "name" : "table or view name" }, 
	    "dataChangeDetectionPolicy" : {
	       "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
	       "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	  }
	}

### Stratégie de détection des colonnes à suppression réversible ###

Lorsque des lignes sont supprimées de la table source, vous devez également supprimer ces lignes de l'index de recherche. Si vous utilisez la stratégie de suivi intégré des modifications SQL, cette opération est prise en charge à votre place. Mais la stratégie de suivi des modifications de limite supérieure ne vous est d’aucune aide pour les lignes supprimées. Que faire, alors ?

Si des lignes sont physiquement supprimées de la table, il n'existe aucun moyen de déduire la présence d'enregistrements qui n'existent plus. Cependant, vous pouvez utiliser la technique de « suppression réversible » pour marquer des lignes comme supprimées logiquement sans les supprimer de la table, en ajoutant une colonne et en marquant les lignes comme supprimées à l'aide d'une valeur de marqueur de cette colonne.

Lorsque vous utilisez la technique de suppression réversible, vous pouvez spécifier cette stratégie réversible comme suit lors de la création ou de la mise à jour de la source de données :

	{ 
	    …, 
	    "dataDeletionDetectionPolicy" : { 
	       "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
	       "softDeleteColumnName" : "[a column name]", 
	       "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
	    }
	}

Notez que **softDeleteMarkerValue** doit être une chaîne. Utilisez la représentation au format chaîne de votre valeur. Par exemple, si vous avez une colonne d'entiers dans laquelle les lignes supprimées sont marquées avec la valeur 1, utilisez `"1"`. Si vous avez une colonne au format bit dans laquelle les lignes supprimées sont marquées avec la valeur booléenne true, utilisez `"True"`.

## Personnaliser l’indexeur Azure SQL ##
 
Vous pouvez personnaliser certains aspects du comportement des indexeurs (par exemple, taille de lot, nombre de documents pouvant être ignorés avant que l'exécution d’un indexeur n’échoue, etc.). Pour plus de détails, consultez [Personnalisation de l'indexeur Azure Search](search-indexers-customization.md).

## Forum Aux Questions (FAQ) ##

**Q. :** Puis-je utiliser l’indexeur SQL Azure avec des bases de données SQL exécutées sur des machines virtuelles IaaS dans Azure ?

R. : Oui, tant que vous autorisez des services Azure à se connecter à votre base de données en ouvrant les ports appropriés.

**Q. :** Puis-je utiliser l’indexeur Azure SQL avec des bases de données SQL exécutées localement ?

R. : Cette opération n’est pas prise en charge, ni recommandée, car elle vous oblige à ouvrir vos bases de données au trafic Internet.

**Q. :** Puis-je utiliser l’indexeur Azure SQL avec des bases de données autres que SQL Server exécutées en IaaS sur Azure ?

R. : Ce cas de figure n’est pas pris en charge, car nous n'avons pas testé l'indexeur avec des bases de données autres que SQL Server.

**Q. :** Puis-je créer plusieurs indexeurs qui s’exécutent selon une planification ?

R. : Oui. Cependant, seul un indexeur peut s'exécuter sur un nœud à la fois. Si vous avez besoin d’exécuter plusieurs indexeurs simultanément, envisagez d’ajouter d’autres unités de recherche à votre service de recherche.

**Q. :** L’exécution d’un indexeur affecte-t-il la charge de travail de mes requêtes ?

R. : Oui. L’indexeur s'exécute sur un des nœuds de votre service de recherche, et les ressources de ce nœud sont partagées entre l'indexation et le traitement du trafic de requêtes d’une part, et d’autres requêtes d’API d’autre part. Si vous exécutez des charges de travail intensives d'indexation et de requête et que vous rencontrez un taux élevé d'erreurs 503 ou des délais de réponse croissants, redimensionnez votre service de recherche.



 

<!---HONumber=Oct15_HO1-->