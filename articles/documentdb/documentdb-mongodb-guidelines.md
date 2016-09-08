<properties 
	pageTitle="Directives de développement pour les comptes DocumentDB prenant en charge le protocole MongoDB (version préliminaire) | Microsoft Azure" 
	description="Consultez les directives de développement pour les comptes DocumentDB prenant en charge le protocole MongoDB, actuellement disponible en version préliminaire" 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="stbaro"/>

# Directives de développement pour les comptes DocumentDB prenant en charge le protocole MongoDB (version préliminaire)

Vous pouvez communiquer avec Azure DocumentDB par le biais de l’un des [pilotes](https://docs.mongodb.org/ecosystem/drivers/) du client MongoDB open source. La prise en charge du protocole pour MongoDB suppose que les pilotes du client MongoDB communiquent avec un point de terminaison serveur MongoDB 2.6 ou version ultérieure. DocumentDB prend en charge cette communication en adhérant au [protocole filaire](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol/) MongoDB version 2.6 (notez que le protocole filaire version 3.2 est presque entièrement pris en charge, mais que certaines expériences client telles que les sessions shell MongoDB 3.2 peuvent indiquer une « dégradation en mode ’hérité’ » »).

DocumentDB prend en charge les principales fonctions d’API MongoDB pour créer, lire, mettre à jour et supprimer (CRUD, Create, Read, Update, Delete) des données et interroger la base de données. Les fonctionnalités mises en œuvre ont été classées par priorité en fonction des besoins des plates-formes, infrastructures, outils et modèles d’application courants.

## Collections

> [AZURE.IMPORTANT] DocumentDB utilise un débit réservé au niveau de la collection pour garantir des performances prévisibles. Les collections contenues dans DocumentDB sont, par conséquent, des entités facturables.

Les réservations de performances sont appliquées au niveau de la collection afin que les applications puissent ajuster les performances au niveau le plus bas des conteneurs de données du système. Le coût d’une collection est donc déterminé par le débit défini pour la collection, mesuré en unités de demande par seconde, avec le stockage total consommé en gigaoctets. Le débit prévu peut être ajusté tout au long de la durée de vie d’une collection pour s’adapter aux besoins de traitement et aux modèles d’accès changeants de votre application. Pour plus d’informations, consultez la rubrique [Niveaux de performances dans DocumentDB](documentdb-performance-levels.md).

Les collections DocumentDB prenant en charge le protocole MongoDB sont, par défaut, créées au niveau de tarification Standard avec 1 000 unités de demande par seconde de débit approvisionné. Vous pouvez ajuster le débit approvisionné de chacune de vos collections, comme décrit dans la rubrique [Modification des niveaux de performances à l’aide du portail Azure](documentdb-performance-levels.md#changing-performance-levels-using-the-azure-portal).

## Opérations CRUD

Les opérations d’insertion, de lecture, de mise à jour, de remplacement et de suppression (CRUD) sont entièrement prises en charge avec MongoDB. Ceci inclut la prise en charge des mises à jour de champ, de tableau, de bit et d’isolation indiquées dans la [spécification des opérateurs de mise à jour](https://docs.mongodb.org/manual/reference/operator/update/) de MongoDB. Pour les opérateurs de mise à jour nécessitant plusieurs manipulations de document, DocumentDB fournit une sémantique ACID complète avec une isolation des instantanés.

## Opérations de requête

DocumentDB prend en charge la totalité de la syntaxe de requête MongoDB à quelques exceptions près. Les requêtes exécutées sur l’ensemble compatible JSON de [types BSON](https://docs.mongodb.org/manual/reference/bson-types/) sont prises en charge en plus du format date/heure MongoDB. Pour les requêtes nécessitant des opérateurs spécifiques de type non JSON, DocumentDB prend en charge les types de données GUID. Le tableau suivant indique les aspects pris en charge et non pris en charge de la syntaxe de requête MongoDB.

## Agrégation

DocumentDB ne prend en charge ni le pipeline d’agrégation MongoDB ni les opérations MapReduce. Le pipeline d’agrégation est généralement utilisé pour traiter les documents via un ensemble de filtres à plusieurs étapes et des transformations telles que le filtrage et le regroupement des résultats. DocumentDB prend en charge en mode natif les fonctions JavaScript définies par l’utilisateur et la procédure stockée. En outre, DocumentDB peut facilement servir de source et de récepteur pour les tâches Hive, Pig et MapReduce à l’aide d’Azure HDInsight via le [connecteur Hadoop](documentdb-run-hadoop-with-hdinsight.md) de DocumentDB.

## Utilisation du portail
L’utilisation du portail Azure pour les comptes compatibles avec le protocole MongoDB est légèrement différente de l’utilisation du portail pour les comptes DocumentDB standard. Nous cherchons actuellement des moyens d’améliorer cette expérience, mais nous avons besoin de vos [commentaires](mailto:askdocdb@microsoft.com?subject=DocumentDB%20Protocol%20Support%20for%20MongoDB%20Preview%20Portal%20Experience) concernant les fonctionnalités du portail qui vous seraient les plus utiles.

## Matrice de prise en charge


### Opérations CRUD et de requête

Fonctionnalité|Pris en charge|Sera pris en charge|Non pris en charge 
---|---|---|---
Insérer|InsertOne| | 
 |InsertMany| | 
 |Insérer| | 
Mettre à jour|UpdateOne| | 
 |UpdateMany| | 
 |Mettre à jour| | 
Mise à jour du champ|$inc, $mul, $rename, $set, $unset, $min, $max|$currentDate| 
Mise à jour du tableau| |-all-| 
Opérateurs au niveau du bit| |-all-| 
Isolement| |-all-| 
Replace|ReplaceOne| |
Supprimer|DeleteOne | |
 |DeleteMany| | 
 |Supprimer| | 
BulkWrite| |bulkWrite()| 
Opérateurs de comparaison|-all-| | 
Opérateurs logiques|-all-| | 
Requête d’élément| |-all-| 
Évaluation|$mod, $regex |$text, $where| 
GeoSpatial|2dsphere, 2d, polygon|Tout le reste| 
Array|$all, $size, $elemMatch|| 
Opérateurs au niveau du bit| |-all-| 
Commentaire|-all-| | 
Projection| |-all-| 


### Commandes de base de données

Fonctionnalité|Pris en charge|Sera pris en charge|Non pris en charge 
---|---|---|---
Agrégation|Nombre| |aggregate, distinct, group, mapreduce
GeoSpatial| |-all-| 
Requête & écriture|find, insert, update, delete, getLastError, getMore, findAndModify| |Eval, parallelCollectionScan, getPrevError, resetError
Cache QueryPlan| | |-all-
Authentification|getnonce, logout, authenticate| |Copydbgetnone, authschemaUpgrade
Gestion des utilisateurs| | |-all-
Gestion des rôles| | |-all-
Réplication| | |-all-
Administration|createIndex, listIndexes, dropIndexes, connectionStatus, reIndex| |Autres commandes. Pour les index, aucune prise en charge des opérateurs Unique, expireAfterSeconds, storageEngine, weights, default\_language, textIndexVersion, min, max, bucketSize
Diagnostic|listDatabases, collStats, dbStats| |Tout le reste

## Étapes suivantes

- Apprenez à [utiliser MongoChef](documentdb-mongodb-mongochef.md) avec un compte DocumentDB prenant en charge le protocole MongoDB.
- Découvrez des [exemples](documentdb-mongodb-samples.md) de prise en charge du protocole MongoDB dans DocumentDB.

 

<!---HONumber=AcomDC_0824_2016-->