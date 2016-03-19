<properties
	pageTitle="Explorer les données dans des tables Hive avec des requêtes Hive | Microsoft Azure"
	description="Explorer les données dans des tables Hive à l’aide de requêtes Hive."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="hangzh;bradsev" />

# Explorer les données dans des tables Hive avec des requêtes Hive 

## Introduction

Ce document fournit des exemples de scripts Hive qui vous permettent d’explorer les données dans des tables Hive.

Le **menu** ci-dessous pointe vers des rubriques qui expliquent comment utiliser des outils pour explorer des données dans différents environnements de stockage. Cette tâche est une étape du processus d’analyse Cortana (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## Configuration requise
Cet article suppose que vous avez :

* Créé un compte Azure Storage. Si vous avez besoin d'aide, consultez [Créer un compte Azure Storage](../hdinsight-get-started.md#storage).
* Approvisionné un cluster Hadoop personnalisé avec le service HDInsight. Si vous avez besoin d'aide, consultez [Personnaliser des clusters Hadoop Azure HDInsight pour l'analyse avancée](machine-learning-data-science-customize-hadoop-cluster.md).
* Chargé les données dans les tables Hive de clusters Hadoop Azure HDInsight. Si tel n'est pas le cas, commencez par suivre la procédure [Créer et charger des données dans les tables Hive](machine-learning-data-science-move-hive-tables.md).
* Activé l’accès à distance au cluster. Si vous avez besoin d’aide, consultez [Accéder au nœud principal du cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Si vous avez besoin d’aide sur l’envoi de requêtes Hive, consultez [Comment envoyer des requêtes Hive](machine-learning-data-science-move-hive-tables.md#submit).

## Exemples de scripts de requête Hive pour l’exploration de données

1. Obtenir le nombre d’observations par partition `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obtenir le nombre d'observations par jour `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Obtenir les niveaux dans une colonne catégorielle `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obtenir le nombre de niveaux en combinant deux colonnes catégorielles `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obtenir la distribution relative aux colonnes numériques `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extraire les enregistrements à partir de la jointure des deux tables

	    SELECT
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>,
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>,
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## Scripts de requête supplémentaires pour les scénarios de données de courses de taxi

Des exemples de requêtes propres aux scénarios mettant en œuvre le [jeu de données NYC Taxi Trip](http://chriswhong.com/open-data/foil_nyc_taxi/) sont également disponibles dans le [référentiel Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Le schéma de données de ces requêtes est déjà spécifié et elles sont exécutables en l’état.

 

<!---HONumber=AcomDC_0211_2016-->