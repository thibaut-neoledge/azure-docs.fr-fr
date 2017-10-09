---
title: "Phase de déploiement du cycle de vie du processus TDSP (Team Data Science Process) - Azure | Microsoft Docs"
description: "Décrit les objectifs, les tâches et les livrables associés à la phase de déploiement de vos projets de science des données."
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
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="deployment"></a>Déploiement

Cette rubrique présente les objectifs, les tâches et les livrables associés au **déploiement** du processus TDSP (Team Data Science Process). Ce processus indique un cycle de vie recommandé que vous pouvez utiliser pour structurer vos projets de science des données. Le cycle de vie présente les étapes majeures que les projets exécutent généralement, souvent de manière itérative :

* **Présentation de l’entreprise**
* **Acquisition de données et compréhension**
* **Modélisation**
* **Déploiement**
* **Acceptation du client**

Voici une représentation visuelle du **cycle de vie du processus TDSP (Team Data Science Process)**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objectif
* Des modèles comportant un pipeline de données sont déployés dans un environnement de production ou similaire en vue de leur acceptation par l’utilisateur final. 

## <a name="how-to-do-it"></a>Marche à suivre
Une tâche principale est traitée dans cette phase :

* **Faire fonctionner le modèle** : déployez le modèle et le pipeline sur un environnement de production ou similaire en vue de l’utilisation de l’application.

### <a name="41-operationalize-a-model"></a>4.1 Faire fonctionner un modèle
Une fois que votre ensemble de modèles fonctionne correctement, il peut être mis en œuvre et utilisé par d’autres applications. En fonction des exigences pour l’entreprise, les prédictions sont faites en temps réel ou par lot. Pour être déployés, les modèles doivent être exposés avec une interface d’API ouverte. L’interface permet au modèle d’être facilement utilisable à partir de diverses applications telles que des sites web en ligne, des feuilles de calcul, des tableaux de bord ou des applications métier et backend. Pour obtenir des exemples de mise en œuvre de modèle avec un service web Azure Machine Learning, consultez [Déploiement d’un service web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). Il est également recommandé de générer des données de télémétrie et de surveillance dans le modèle de production et le pipeline de données qui sont déployés. Cette pratique facilite la création de rapports sur l’état subséquent du système et le dépannage de ce dernier.  

## <a name="artifacts"></a>Artefacts
* Tableau de bord d’état de mesures clés et d’intégrité du système.
* Rapport de modélisation final avec les détails du déploiement.
* Document final de l’architecture de la solution.


## <a name="next-steps"></a>Étapes suivantes

Voici des liens vers chaque étape du cycle de vie du processus TDSP :

* [1. Compréhension de l’activité](lifecycle-business-understanding.md)
* [2. Acquisition et compréhension des données](lifecycle-data.md)
* [3. Modélisation](lifecycle-modeling.md)
* [4. Déploiement](lifecycle-deployment.md)
* [5. Acceptation du client](lifecycle-acceptance.md)

Des procédures pas à pas complètes illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. La rubrique [Exemples de procédures pas à pas](walkthroughs.md) les répertorie et les décrit brièvement, en les accompagnant de liens. Les procédures illustrent comment combiner les outils et services dans le cloud et localement dans un flux de travail ou un pipeline pour créer une application intelligente. 

Pour obtenir des exemples d’exécution des étapes du processus TDSP qui utilisent Azure Machine Learning Studio, consultez le parcours d’apprentissage [Avec Azure ML](http://aka.ms/datascienceprocess).
