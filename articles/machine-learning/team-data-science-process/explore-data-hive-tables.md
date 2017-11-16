---
title: "Explorer les données dans des tables Hive avec des requêtes Hive | Microsoft Docs"
description: "Explorer les données dans des tables Hive à l’aide de requêtes Hive."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 0d46cea5-2b4c-4384-9bfa-fa20f6f75148
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: 9cf205abcf9782ceac4d9ac5a920e136b69c57b6
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorer les données dans des tables Hive avec des requêtes Hive
Ce document fournit des exemples de scripts Hive qui permettent d’explorer des données dans des tables Hive dans un cluster Hadoop HDInsight.

Le **menu** suivant pointe vers des rubriques qui expliquent comment utiliser des outils pour explorer des données dans différents environnements de stockage.

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Composants requis
Cet article suppose que vous avez :

* Créé un compte Azure Storage. Pour des instructions, voir [Créer un compte Stockage Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Approvisionné un cluster Hadoop personnalisé avec le service HDInsight. Si vous avez besoin d'aide, consultez [Personnaliser des clusters Hadoop Azure HDInsight pour l'analyse avancée](customize-hadoop-cluster.md).
* Chargé les données dans les tables Hive de clusters Hadoop Azure HDInsight. Si tel n’est pas le cas, commencez par suivre la procédure décrite sous [Créer et charger des données dans les tables Hive](move-hive-tables.md) .
* Activé l’accès à distance au cluster. Si vous avez besoin d’aide, consultez [Accéder au nœud principal du cluster Hadoop](customize-hadoop-cluster.md).
* Si vous avez besoin d’aide sur l’envoi de requêtes Hive, consultez [Comment envoyer des requêtes Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Exemples de scripts de requête Hive pour l’exploration de données
1. Obtenir le nombre d’observations par partition  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Obtenir le nombre d'observations par jour  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Obtenir les niveaux dans une colonne catégorielle   
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Obtenir le nombre de niveaux en combinant deux colonnes catégorielles  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Obtenir la distribution relative aux colonnes numériques   
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts de requête supplémentaires pour les scénarios de données de courses de taxi
Des exemples de requêtes propres aux scénarios mettant en œuvre le jeu de [données NYC Taxi Trip](http://chriswhong.com/open-data/foil_nyc_taxi/) sont également disponibles dans le [référentiel Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Le schéma de données de ces requêtes est déjà spécifié et elles sont exécutables en l’état.

