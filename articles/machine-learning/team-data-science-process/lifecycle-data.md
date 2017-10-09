---
title: "Phase d’acquisition de données et de compréhension du cycle de vie du processus TDSP (Team Data Science Process) - Azure | Microsoft Docs"
description: "Décrit les objectifs, les tâches et les livrables associés à la phase d’acquisition des données et de compréhension de vos projets de science des données."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="data-acquisition-and-understanding"></a>Acquisition de données et compréhension

Cette rubrique présente les objectifs, les tâches et les livrables associés à la **phase d’acquisition de données et de compréhension** du processus TDSP (Team Data Science Process). Ce processus indique un cycle de vie recommandé que vous pouvez utiliser pour structurer vos projets de science des données. Le cycle de vie présente les étapes majeures que les projets exécutent généralement, souvent de manière itérative :

* **Présentation de l’entreprise**
* **Acquisition de données et compréhension**
* **Modélisation**
* **Déploiement**
* **Acceptation du client**

Voici une représentation visuelle du **cycle de vie du processus TDSP (Team Data Science Process)**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objectifs
* Jeu de données propre, de grande qualité et prêt pour modélisation, dont les relations avec les variables cibles qui se trouvent dans l’environnement d’analytique approprié sont comprises.
* Développement d’une architecture de solution du pipeline de données pour actualiser et évaluer les données régulièrement.

## <a name="how-to-do-it"></a>Marche à suivre
Trois tâches principales sont traitées dans cette phase :

* **Ingérer les données** dans l’environnement d’analyse cible.
* **Explorer les données** pour déterminer si la qualité des données est suffisante pour répondre à la question. 
* **Configurer un pipeline de données** pour évaluer les données nouvelles ou régulièrement actualisées.

### <a name="21-ingest-the-data"></a>2.1 Ingérer les données
Configurez le processus permettant de déplacer les données des emplacements sources vers les emplacements cibles où doivent être exécutées les opérations d’analytique telles que l’apprentissage et les prédictions. Pour connaître les détails techniques et les options disponibles pour effectuer cette opération avec plusieurs services de données Azure, consultez [Charger des données dans des environnements de stockage à des fins d’analyse](ingest-data.md). 

### <a name="22-explore-the-data"></a>2.2 Explorer les données
Avant de former vos modèles, vous devez développer une parfaite compréhension des données. Les jeux de données réels sont souvent parasités, ne contiennent pas certaines valeurs ou comportent une multitude d’autres anomalies. La visualisation et la synthèse des données permettent d’auditer la qualité de vos données et de fournir les informations nécessaires pour traiter les données en vue de leur modélisation. Ce processus est souvent itératif.

Le processus TDSP fournit un utilitaire automatisé appelé [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), qui aide à visualiser les données et à préparer des rapports de synthèse de données. Nous vous recommandons de commencer avec IDEAR pour explorer les données afin de développer une compréhension initiale des données de manière interactive sans codage, puis d’écrire du code personnalisé pour l’exploration et la visualisation des données. Pour obtenir de l’aide sur le nettoyage des données, consultez [Tâches de préparation des données pour l’apprentissage automatique amélioré](prepare-data.md).  

Une fois que vous êtes satisfait de la qualité des données nettoyées, l’étape suivante consiste à mieux comprendre les modèles inhérents aux données grâce auxquels vous pouvez choisir et développer un modèle de prédiction approprié pour votre cible. Recherchez des éléments de preuve sur la qualité de la connexion des données à la cible et indiquant s’il existe suffisamment de données pour passer aux étapes de modélisation suivantes. Là encore, ce processus est souvent itératif. Vous devrez peut-être rechercher de nouvelles sources de données avec des données plus précises ou plus appropriées pour compléter le jeu de données identifié au cours de la phase précédente.  

### <a name="23-set-up-a-data-pipeline"></a>2.3 Configurer un pipeline de données
Outre l’ingestion et le nettoyage initiaux des données, vous devez généralement définir un processus pour noter les nouvelles données ou actualiser les données régulièrement dans le cadre d’un processus de formation continue. Cette opération est possible grâce à la configuration d’un pipeline de données ou d’un flux de travail. Voici un [exemple](move-sql-azure-adf.md) de configuration d’un pipeline avec [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Une architecture de solution du pipeline de données est développée dans cette phase. Le pipeline est également développé parallèlement aux phases suivantes du projet de science des données. Le pipeline peut être traité par lot ou en streaming/temps réel, ou encore de façon hybride, suivant les besoins de votre entreprise et les contraintes de vos systèmes existants auxquels cette solution est en cours d’intégration. 

## <a name="artifacts"></a>Artefacts
Voici les livrables de cette phase.

* [**Rapport de qualité des données**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md) : ce rapport contient des résumés de données, les relations entre chaque attribut et cible, le classement des variables, etc. L’outil [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) fourni dans le cadre du processus TDSP permet de générer rapidement ce rapport sur n’importe quel jeu de données tabulaire tel qu’un fichier CSV ou une table relationnelle. 
* **Architecture de la solution** : il peut s’agir d’un diagramme ou d’une description de votre pipeline de données utilisé pour exécuter une évaluation ou des prédictions sur de nouvelles données une fois que vous avez créé un modèle. Elle contient également le pipeline avec lequel former le modèle en fonction des nouvelles données. Le document est stocké dans le répertoire [Project](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quand vous utilisez le modèle de structure de répertoire TDSP.
* **Décision de point de contrôle** : avant de procéder à l’ingénierie et à la modélisation complètes, vous pouvez réévaluer le projet afin de déterminer si cela vaut la peine de le poursuivre. Vous pouvez, par exemple, être en mesure de continuer, avoir besoin de collecter des données supplémentaires ou abandonner le projet s’il n’existe pas de données permettant de répondre à la question.

## <a name="next-steps"></a>Étapes suivantes

Voici des liens vers chaque étape du cycle de vie du processus TDSP :

* [1. Présentation de l’entreprise](lifecycle-business-understanding.md)
* [2. Acquisition de données et compréhension](lifecycle-data.md)
* [3. Modélisation](lifecycle-modeling.md)
* [4. Déploiement](lifecycle-deployment.md)
* [5. Acceptation du client](lifecycle-acceptance.md)

Des procédures pas à pas complètes illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. La rubrique [Exemples de procédures pas à pas](walkthroughs.md) les répertorie et les décrit brièvement, en les accompagnant de liens. Elles illustrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente.  

Par obtenir des exemples d’exécution des étapes du processus TDSP qui utilisent Azure Machine Learning Studio, consultez le parcours d’apprentissage [Avec Azure ML](http://aka.ms/datascienceprocess).
