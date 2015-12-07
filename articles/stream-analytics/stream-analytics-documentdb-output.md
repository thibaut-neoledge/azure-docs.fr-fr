<properties 
	pageTitle="Détails d’une sortie DocumentDB | Microsoft Azure" 
	description="Utilisation de DocumentDB comme sortie Azure Stream Analytics"
	keywords="sortie documentdb, sortie docdb, stream analytics et documentdb"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="11/23/2015" 
	ms.author="jeffstok"/>

# Présentation de DocumentDB comme sortie Azure Stream Analytics

Azure Stream Analytics prend désormais en charge [Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) comme sortie, ce qui permet d’archiver des données et d’exécuter des requêtes à faible latence sur les données JSON non structurées. Ce document décrit comment implémenter au mieux cette intégration. Pour ceux qui ne sont pas familiarisés avec DocumentDB, commençons par suivre le [parcours d'apprentissage de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/).

La sortie Azure DocumentDB dans Stream Analytics vous permet d’écrire les résultats du traitement de votre flux dans vos collections DocumentDB. Stream Analytics ne crée pas collections dans votre base de données, mais vous oblige à les créer dès le départ. Les coûts de facturation des collections DocumentDB vous apparaissent ainsi de façon totalement transparente. Vous pouvez en outre optimiser les performances, la cohérence et la capacité de vos collections directement à l’aide des [API DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). Nous vous recommandons d’utiliser une seule base de données DocumentDB par tâche de streaming afin de séparer logiquement vos collections pour une tâche de streaming.

Vous trouverez ci-dessous le détail de certaines options de collection DocumentDB.

## Cohérence

Pour respecter les exigences de votre application, DocumentDB vous permet d’ajuster la base de données et les collections et de faire les meilleurs compromis entre cohérence, disponibilité et latence. Selon les niveaux de cohérence de lecture qui s’appliquent à votre scénario compte tenu de la latence de lecture et d’écriture, vous pouvez choisir un niveau de cohérence sur votre compte de base de données. Par défaut, DocumentDB permet également une indexation synchrone sur chaque opération CRUD exécutée sur votre collection. Cette option est également utile pour contrôler les performances de lecture/écriture dans DocumentDB. Pour plus d’informations à ce sujet, consultez l’article [Modification du niveau de cohérence des bases de données et des requêtes](../articles/documentdb-consistency-levels.md).

## Performances

Vous pouvez créer des collections DocumentDB sur 3 différents niveaux de performances (S1, S2 ou S3) qui déterminent le débit de CRUD pour cette collection. Les performances sont affectées par les niveaux d’indexation/de cohérence sur votre collection. Pour comprendre en détail ces niveaux de performance, veuillez vous reporter à [cet article](../articles/documentdb-performance-levels.md).

## Upserts

L’intégration de Stream Analytics avec DocumentDB vous permet d’insérer ou mettre à jour des enregistrements dans votre collection DocumentDB à partir d’une colonne d’identification de document donnée. Il s’agit de ce que l’on appelle un *Upsert*.

Stream Analytics utilise une approche optimiste d’Upsert, c’est-à-dire que les mises à jour ne sont effectuées qu’en cas d’échec de l’insertion en raison d’un conflit d’ID de document. Cette mise à jour est effectuée par Stream Analytics sous la forme d’un correctif, de sorte que le document peut être partiellement mis à jour. Autrement dit, l’ajout de nouvelles propriétés ou le remplacement d’une propriété existante est effectué de façon incrémentielle. Notez que les modifications apportées aux valeurs des propriétés du tableau dans votre document JSON ont pour effet d’écraser l’intégralité du tableau. Les valeurs du tableau ne sont donc pas fusionnées.

## Partitionnement

Les collections DocumentDB vous permettent de partitionner vos données selon les modèles de requête et les besoins de performances de votre application. Chaque collection peut contenir jusqu’à 10 Go de données (maximum) et il n’existe actuellement aucun moyen de mettre à l’échelle une collection (ou de dépasser sa capacité). Pour une montée en puissance parallèle, Stream Analytics vous permet d’écrire dans plusieurs collections avec un préfixe donné (voir les détails d’utilisation ci-dessous). Stream Analytics utilise la stratégie de [résolveur de partition par hachage](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) en s’appuyant sur la colonne PartitionKey fournie par l’utilisateur pour partitionner ses enregistrements de sortie. Le nombre de collections avec le préfixe spécifié lors du démarrage de la tâche de streaming équivaut au nombre de partitions de sortie sur lesquelles la tâche écrit en parallèle (collections DocumentDB = partitions de sortie). Pour une collection S3 unique utilisant une méthode d’indexation différée axée uniquement sur les insertions, vous pouvez vous attendre à obtenir un débit d’écriture de 0,4 Mo/s. L’utilisation de plusieurs collections peut vous permettre d’atteindre un débit supérieur et des capacités accrues.

Si vous envisagez d’augmenter le nombre de partitions, vous devrez peut-être arrêter votre tâche et repartitionner les données de vos collections existantes en nouvelles collections, avant de redémarrer la tâche Streaming Analytics. Nous publierons prochainement un article sur l’utilisation de PartitionResolver et sur le repartitionnement, avec des exemples de code. L’article [Partitionnement dans DocumentDB](../articles/documentdb-partition-data.md#developing-a-partitioned-application) fournit également des détails à ce sujet.

## Paramètres DocumentDB

Lorsque vous créez une sortie DocumentDB dans Stream Analytics, vous devez fournir des informations supplémentaires, comme indiqué ci-dessous. Cette section fournit une explication de la définition des propriétés.
  
![écran de sortie documentdb stream analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)

-   **Output Alias** : alias faisant référence à cette sortie dans votre requête ASA  
-   **Account Name** : nom ou URI du point de terminaison du compte DocumentDB.  
-   **Account Key** : clé d’accès partagé du compte DocumentDB.  
-   **Database** : nom de la base de données DocumentDB.  
-   **Collection Name Pattern** : modèle de nom de collection des collections à utiliser. Le format de nom de collection peut être construit à l’aide du jeton facultatif {partition}, où les partitions commencent à 0. Voici des exemples d’entrées valides : 1) MyCollection : il doit exister une collection nommée « MyCollection ». (2) MyCollection{partition} : vous devez créer les collections « MyCollection0 », « MyCollection1 », « MyCollection2 », etc.  
-   **Partition Key** : nom du champ dans les événements de sortie utilisé pour spécifier la clé de partitionnement de sortie sur les collections. Pour une sortie de collection unique, une colonne de sortie arbitraire peut être utilisée (par exemple, PartitionId).  
-   **Document ID** : facultatif. Nom du champ dans les événements de sortie utilisé pour spécifier la clé primaire sur laquelle sont basées les opérations d’insertion ou de mise à jour.  

<!---HONumber=AcomDC_1125_2015-->