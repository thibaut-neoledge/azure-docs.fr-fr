---
title: "Procédures pas à pas du processus Team Data Science Process | Microsoft Docs"
description: "Les procédures pas à pas montrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aa63d5a5-25ee-4c4b-9a4c-7553b98d7f6e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: bradsev
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 6df3fc533b0c375cd85743a971c778071629bb7e
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017


---
# <a name="team-data-science-process-walkthroughs"></a>Procédures pas à pas du processus TDSP (Team Data Science Process)
Chacune des **procédures pas à pas** détaillées ici décrit les étapes du processus Team Data Science Process pour des **scénarios spécifiques**. Elles illustrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une **application intelligente**. Ces procédures sont regroupées par **plateforme** utilisée : Spark, HDInsight (Hadoop), Azure Data Lake et SQL Server.


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>HDInsight Spark avec PySpark et Scala

### <a name="data-science-using-python-with-spark-on-azure"></a>Science des données à l’aide de Python avec Spark sur Azure
La procédure pas à pas [Vue d’ensemble de la science des données à l’aide de Spark sur Azure HDInsight](machine-learning-data-science-spark-overview.md) utilise le processus Team Data Science Process dans un scénario et un [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) pour effectuer des opérations sur le jeu de données des courses et tarifs de taxi à New York disponible publiquement, telles que le stockage, l’exploration et la conception de fonctionnalités. 

### <a name="data-science-using-scala-with-spark-on-azure"></a>Science des données à l’aide de Scala avec Spark sur Azure
La procédure pas à pas [Science des données à l’aide de Scala avec Spark sur Azure](machine-learning-data-science-process-scala-walkthrough.md) montre comment utiliser Scala pour les tâches d’apprentissage automatique supervisées avec la bibliothèque d’apprentissage automatique (MLlib) Spark et les packages SparkML sur un cluster Azure HDInsight Spark. Elle vous guide à travers les tâches qui constituent le [processus de science des données](http://aka.ms/datascienceprocess): ingestion et exploration des données, visualisation, conception de fonctionnalités et consommation de modèles. Les modèles conçus incluent la régression logistique, la régression linéaire, les forêts aléatoires et les arbres GBT (Gradient Boosted Tree).


## <a name="hdinsight-hadoop"></a>HDInsight Hadoop 

### <a name="use-hdinsight-hadoop-clusters"></a>Utiliser des clusters HDInsight Hadoop
La procédure pas à pas [Processus Team Data Science Process en action : utilisation de clusters Hadoop HDInsight](machine-learning-data-science-process-hive-walkthrough.md) utilise un [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) pour effectuer des opérations sur un jeu de données des courses et tarifs de taxi à New York disponible publiquement, telles que le stockage, l’exploration et la conception de fonctionnalités.

### <a name="use-azure-hdinsight-hadoop-clusters-on-a-1-tb-dataset"></a>Utiliser des clusters Azure HDInsight Hadoop sur un jeu de données de 1 To
La procédure pas à pas [Processus Team Data Science Process en action : utilisation de clusters Azure HDInsight Hadoop sur un jeu de données de 1 To](machine-learning-data-science-process-hive-criteo-walkthrough.md) présente un scénario qui utilise un [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) pour effectuer des opérations sur un jeu de données [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/), disponible publiquement, telles que le stockage, l’exploration, la conception de fonctionnalités et la réduction d’échantillon.


## <a name="azure-data-lake"></a>Azure Data Lake

### <a name="use-azure-data-lake-store-and-analytics"></a>Utiliser Azure Data Lake Store et Analytics
La procédure pas à pas [Science des données évolutive dans Azure Data Lake : une procédure de bout en bout](machine-learning-data-science-process-data-lake-walkthrough.md) montre comment utiliser Azure Data Lake pour effectuer des tâches d’exploration de données et de classification binaire sur un échantillon de jeu de données des taxis à New York afin de prédire si le client paye un pourboire. 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server et SQL Data Warehouse 

### <a name="use-sql-data-warehouse"></a>Utiliser SQL Data Warehouse
La procédure pas à pas [Processus TDSP (Team Data Science Process) en action : utilisation de SQL Data Warehouse](machine-learning-data-science-process-sqldw-walkthrough.md) vous explique comment créer et déployer des modèles de régression et de classification d’apprentissage de machine à l’aide de SQL Data Warehouse (SQL DW) pour un jeu de données des courses et tarifs de taxi à New York disponible publiquement.

### <a name="use-sql-server"></a>Utiliser SQL Server
La procédure pas à pas [Processus TDSP (Team Data Science Process) en action : utilisation de SQL Server](machine-learning-data-science-process-sql-walkthrough.md) vous explique comment créer et déployer des modèles de régression et de classification d’apprentissage de machine à l’aide de SQL Data Server et d’un jeu de données des courses et tarifs de taxi à New York disponible publiquement.

### <a name="use-r-with-sql-server-r-services"></a>Utiliser R avec les services SQL Server R
La procédure pas à pas [Data Science End-to-End Walkthrough using SQL Server R Services](https://msdn.microsoft.com/library/mt612857.aspx) (Procédure de bout en bout de science des données à l’aide de services SQL Server R) fournit aux scientifiques des données une combinaison de fonctions SQL personnalisées, de données SQL Server et de code R pour créer et déployer un modèle R sur SQL Server.

### <a name="use-t-sql-with-sql-server-r-services"></a>Utiliser T-SQL avec les services SQL Server R
La procédure pas à pas [In-Database Advanced Analytics for SQL Developers](https://msdn.microsoft.com/library/mt683480.aspx) (Analyses avancées des bases de données pour les développeurs SQL) fournit aux programmeurs SQL une expérience pour créer une solution d’analyse avancée avec Transact-SQL à l’aide des services SQL Server R pour faire fonctionner une solution R.


### <a name="use-t-sql-with-sql-server-python-services"></a>Utiliser T-SQL avec les services SQL Server Python
La procédure pas à pas [In-Database Python Analytics for SQL Developers ](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) (Analyse Python des bases de données pour les développeurs SQL) permet aux programmeurs SQL d’expérimenter la création d’une solution Machine Learning dans SQL Server. Elle montre comment intégrer Python dans une application en ajoutant un code Python dans des procédures stockées.

## <a name="whats-next"></a>Et ensuite ?
Pour une vue d’ensemble des rubriques qui vous guident à travers les tâches qui constituent le processus de science des données dans Azure, consultez [Processus de sciences des données](http://aka.ms/datascienceprocess). 


