---
title: Guides HDInsight Spark avec PySpark et Scala sur Azure | Microsoft Docs
description: "Exemples du processus TDSP (Team Data Science Process) qui montrent comment utiliser PySpark et Scala sur Azure HDInsight Spark pour effectuer des analyses prédictives."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 27065c3437c4617ed035623b48aa1a1a31a7094f
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---


# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Guides de la science des données HDInsight Spark avec PySpark et Scala sur Azure

Ces guides utilisent PySpark et Scala sur un cluster Azure Spark pour effectuer des analyses prédictives. Ils suivent les étapes décrites dans le processus TDSP (Team Data Science Process). Vous trouverez une vue d’ensemble du processus TDSP sur la page [Processus de science des données](data-science-process-overview.md) Vous trouverez une vue d’ensemble de Spark sur HDInsight sur la page [Présentation de Spark sur HDInsight](../hdinsight/hdinsight-apache-spark-overview.md).

D’autres guides de la science des données qui appliquent le processus TDSP sont regroupés en fonction de la **plateforme** qu’ils utilisent : 

[!INCLUDE [tdsp-walkthroughs-by-platform](../../includes/tdsp-walkthroughs-by-platform.md)]

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Prédire les pourboires laissés aux taxis avec PySpark sur Azure Spark

Le guide [Utiliser Spark sur Azure HDInsight](machine-learning-data-science-spark-overview.md) utilise des données sur les taxis de New York pour prédire si un pourboire sera laissé, ainsi que la distribution attendue des montants. Il utilise le processus TDSP (Team Data Science Process) dans un scénario qui s’appuie sur un [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) pour stocker et explorer des données, et effectuer du feature engineering à partir du jeu de données public de courses et de tarifs des taxis new-yorkais. Cette vue d’ensemble fournit un cluster HDInsight Spark et les Notebooks Jupyter PySpark utilisés dans la suite de la procédure. Ceux-ci indiquent comment explorer les données, puis créer et utiliser des modèles. Le bloc-notes d’exploration et de modélisation avancées des données montre comment inclure une validation croisée, un balayage hyperparamétrique et une évaluation du modèle.

### <a name="data-exploration-and-modeling-with-spark"></a>Exploration et modélisation des données avec Spark 
explorez le jeu de données et créez, notez et évaluez les modèles de Machine Learning, en procédant de la manière décrite dans la rubrique [Créer des modèles de classification binaire et de régression pour des données avec la boîte à outils Spark MLlib](machine-learning-data-science-spark-data-exploration-modeling.md).

### <a name="model-consumption"></a>Utilisation des modèles
pour découvrir comment évaluer les modèles de classification et de régression créés dans cette rubrique, consultez la page [Noter et évaluer des modèles de Machine Learning créés avec Spark](machine-learning-data-science-spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Validation croisée et balayage hyperparamétrique
consultez la page [Exploration et modélisation avancées des données avec Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) pour savoir comment entraîner les modèles à l’aide de la validation croisée et du balayage hyperparamétrique.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Prédire les pourboires laissés aux taxis avec Scala sur Azure Spark

Le guide [Utiliser Scala avec Spark sur Azure](machine-learning-data-science-process-scala-walkthrough.md) utilise des données sur les taxis de New York pour prédire si un pourboire sera laissé, ainsi que la distribution attendue des montants. Il montre comment utiliser Scala pour les tâches de Machine Learning supervisé avec la bibliothèque de Machine Learning (MLlib) Spark et les packages SparkML sur un cluster Azure HDInsight Spark. Elle vous guide à travers les tâches qui constituent le [processus de science des données](http://aka.ms/datascienceprocess): ingestion et exploration des données, visualisation, conception de fonctionnalités et consommation de modèles. Les modèles conçus incluent la régression logistique, la régression linéaire, les forêts aléatoires et les arbres GBT (Gradient Boosted Tree).


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les composants clés qui constituent le processus TDSP (Team Data Science Process), consultez la page [Vue d’ensemble du processus TDSP (Team Data Science Process)](data-science-process-overview.md).

Pour en savoir plus sur le cycle de vie du processus TDSP (Team Data Science Process), qui permet de structurer les projets de science des données, consultez la page [Cycle de vie du processus TDSP (Team Data Science Process)](data-science-process-lifecycle.md). Le cycle de vie présente les étapes, du début à la fin, que les projets suivent généralement quand ils sont exécutés. 


