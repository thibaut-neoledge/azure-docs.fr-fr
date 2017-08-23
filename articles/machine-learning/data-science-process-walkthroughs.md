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
ms.date: 07/25/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: c0f0ff599909234b2b43f2b8512d77f9053b8a09
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---
# <a name="team-data-science-process-walkthroughs"></a>Procédures pas à pas du processus TDSP (Team Data Science Process)
Chacune des **procédures pas à pas** détaillées ici décrit les étapes du processus Team Data Science Process pour des **scénarios spécifiques**. Elles illustrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une **application intelligente**. Les procédures pas à pas sont regroupées selon la **plateforme** qu’elles utilisent : 

- Spark avec PySpark et Scala
- HDInsight (Hadoop)
- Azure Data Lake 
- SQL Server
- SQL Data Warehouse 


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>HDInsight Spark avec PySpark et Scala

- La procédure pas à pas [Utiliser Spark sur Azure HDInsight](machine-learning-data-science-spark-overview.md) utilise le processus TDSP (Team Data Science Process) dans un scénario à l’aide d’un [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) pour enregistrer, explorer et concevoir les fonctionnalités des données extraites du jeu de données disponible publiquement excursion en taxi à New York et tarifs.

- La procédure pas à pas [Utiliser Scala avec Spark sur Azure](machine-learning-data-science-process-scala-walkthrough.md) montre comment utiliser Scala pour les tâches machine learning supervisées avec la bibliothèque machine learning (MLlib) Spark et les packages SparkML sur un cluster Azure HDInsight Spark. Elle vous guide à travers les tâches qui constituent le [processus de science des données](http://aka.ms/datascienceprocess): ingestion et exploration des données, visualisation, conception de fonctionnalités et consommation de modèles. Les modèles conçus incluent la régression logistique, la régression linéaire, les forêts aléatoires et les arbres GBT (Gradient Boosted Tree).


## <a name="hdinsight-hadoop"></a>HDInsight Hadoop 

- La procédure pas à pas [Utiliser des clusters Hadoop HDInsight](machine-learning-data-science-process-hive-walkthrough.md) utilise un [cluster Hadoop Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) pour enregistrer, explorer et concevoir les fonctionnalités des données extraites du jeu de données excursion en taxi à New York et tarifs disponible publiquement.

- La procédure pas à pas [Utiliser des clusters Hadoop Azure HDInsight  sur un jeu de données de 1 To](machine-learning-data-science-process-hive-criteo-walkthrough.md) présente un scénario qui utilise un [cluster Hadoop Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) pour enregistrer, explorer, concevoir les fonctionnalités et réduire l’échantillon des données issues d’un jeu de données [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponible publiquement.


## <a name="azure-data-lake"></a>Azure Data Lake

- La procédure pas à pas [Utiliser Azure Data Lake pour la science des données](machine-learning-data-science-process-data-lake-walkthrough.md) montre comment utiliser Azure Data Lake pour faire des tâches d’exploration de données et de classification binaire sur un échantillon du jeu de données taxi à New York afin de prédire si un pourboire est payé par un client. 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server et SQL Data Warehouse 

- La procédure pas à pas [Utiliser SQL Data Warehouse](machine-learning-data-science-process-sqldw-walkthrough.md) explique comment générer et déployer des modèles de régression et de classification machine learning à l’aide de SQL Data Warehouse (SQL DW) pour un jeu de données des courses et tarifs de taxi à New York disponible publiquement.

- La procédure pas à pas [Utiliser SQL Server](machine-learning-data-science-process-sql-walkthrough.md) explique comment générer et déployer des modèles de régression et de classification machine learning à l’aide de SQL Server et d’un jeu de données des courses et tarifs de taxi à New York disponible publiquement.

- La procédure pas à pas [Utiliser SQL Server R Services](https://msdn.microsoft.com/library/mt612857.aspx) fournit aux chercheurs de données une combinaison de codes R, de données SQL Server et de fonctions SQL personnalisées pour générer et déployer un modèle R sur SQL Server.

- La procédure pas à pas [Utiliser T-SQL Server R Services](https://msdn.microsoft.com/library/mt683480.aspx) fournit aux programmeurs SQL une expérience leur permettant de générer une solution d’analyse avancée avec Transact-SQL à l’aide de SQL Server R Services pour utiliser une solution R.

- La procédure pas à pas [Utiliser T-SQL with SQL Server Python Services ](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) fournit aux programmeurs SQL une expérience leur permettant de générer une solution machine learning dans SQL Server. Elle montre comment intégrer Python dans une application en ajoutant un code Python dans des procédures stockées.

## <a name="whats-next"></a>Et ensuite ?
Pour une vue d’ensemble des rubriques qui vous guident à travers les tâches qui constituent le processus de science des données dans Azure, consultez [Processus de sciences des données](http://aka.ms/datascienceprocess). 


