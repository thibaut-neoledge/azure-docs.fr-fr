---
title: "Guides de la science des données HDInsight Hadoop avec Hive sur Azure | Microsoft Docs"
description: "Exemples du processus TDSP (Team Data Science Process) qui montrent comment utiliser Hive sur Azure HDInsight Hadoop pour effectuer des analyses prédictives."
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
ms.date: 09/04/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5dbfd043f1809f407baf6aa4ca5c5272af425012
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Guides de la science des données HDInsight Hadoop avec Hive sur Azure 

Ces guides utilisent Hive avec un cluster HDInsight Hadoop pour effectuer des analyses prédictives. Ils suivent les étapes décrites dans le processus TDSP (Team Data Science Process). Vous trouverez une vue d’ensemble du processus TDSP sur la page [Processus de science des données](overview.md) Vous trouverez une présentation d’Azure HDInsight sur la page [Présentation d’Azure HDInsight, la pile de technologies Hadoop, et des clusters Hadoop](../../hdinsight/hdinsight-hadoop-introduction.md).

D’autres guides de la science des données qui appliquent le processus TDSP sont regroupés en fonction de la **plateforme** qu’ils utilisent. Consultez [Procédures pas à pas du processus TDSP (Team Data Science Process)](walkthroughs.md) pour connaître les détails de ces exemples.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Prédire les pourboires laissés aux taxis avec Hive et HDInsight Hadoop

Le guide [Utiliser les clusters HDInsight Hadoop](hive-walkthrough.md) utilise des données sur les taxis de New York pour prédire : 

- si un pourboire sera laissé ; 
- la distribution du montant des pourboires.

Le scénario est implémenté avec Hive et un [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Vous découvrirez comment stocker et explorer des données, et effectuer du feature engineering à partir d’un jeu de données public de courses et de tarifs des taxis new-yorkais. Vous utiliserez également Azure Machine Learning pour générer et déployer les modèles.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Prédire les clics publicitaires avec Hive et HDInsight Hadoop

Le guide [Utiliser des clusters Azure HDInsight Hadoop sur un jeu de données de 1 To](hive-criteo-walkthrough.md) utilise un jeu de données de clics [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) public pour prédire si un pourboire sera laissé, ainsi que la distribution attendue des montants. Le scénario est implémenté avec Hive et un [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) pour stocker, explorer et sous-échantillonner des données, ainsi qu’effectuer du feature engineering. Il utilise Azure Machine Learning pour générer, entraîner et évaluer un modèle de classification binaire qui prédit si un utilisateur cliquera sur une publicité. Le guide se conclut en expliquant comment publier un de ces modèles en tant que service web.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les composants clés qui constituent le processus TDSP (Team Data Science Process), consultez la page [Vue d’ensemble du processus TDSP (Team Data Science Process)](overview.md).

Pour en savoir plus sur le cycle de vie du processus TDSP (Team Data Science Process), qui permet de structurer les projets de science des données, consultez la page [Cycle de vie du processus TDSP (Team Data Science Process)](lifecycle.md). Le cycle de vie présente les étapes, du début à la fin, que les projets suivent généralement quand ils sont exécutés. 


