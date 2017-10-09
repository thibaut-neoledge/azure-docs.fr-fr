---
title: "Phase de présentation de l’entreprise du cycle de vie du processus TDSP (Team Data Science Process) - Azure | Microsoft Docs"
description: "Décrit les objectifs, les tâches et les livrables associés à la phase de présentation de l’entreprise de vos projets de science des données."
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
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="business-understanding"></a>Présentation de l’entreprise

Cette rubrique présente les objectifs, les tâches et les livrables associés à la **phase de présentation de l’entreprise** du processus TDSP (Team Data Science Process). Ce processus indique un cycle de vie recommandé que vous pouvez utiliser pour structurer vos projets de science des données. Le cycle de vie présente les étapes majeures que les projets déroulent habituellement, souvent de manière itérative :

* **Présentation de l’entreprise**
* **Acquisition de données et compréhension**
* **Modélisation**
* **Déploiement**
* **Acceptation du client**

Voici une représentation visuelle du **cycle de vie du processus TDSP (Team Data Science Process)**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objectifs
* Sont spécifiées les **variables clés** devant servir de **cibles du modèle** et dont les mesures associées permettent de déterminer la réussite du projet.
* Les **sources de données** pertinentes auxquelles l’entreprise a ou doit avoir accès sont identifiées.

## <a name="how-to-do-it"></a>Marche à suivre
Deux tâches principales sont traitées dans cette phase : 

* **Définir les objectifs** : collaborez avec votre client et autres parties prenantes pour comprendre et identifier les problèmes métier. Formulez des questions qui définissent les objectifs commerciaux et que les techniques de sciences des données peuvent cibler.
* **Identifier les sources de données** : recherchez les données pertinentes qui vous aident à répondre aux questions qui définissent les objectifs du projet.

### <a name="11-define-objectives"></a>1.1 Définir les objectifs

1. Un objectif central de cette étape consiste à identifier les **variables d’activité** clés que l’analyse doit prédire. Ces variables sont appelées **cibles du modèle** et les mesures qui leur sont associées sont utilisées pour déterminer la réussite du projet. Deux exemples de telles cibles sont les prévisions de ventes ou la probabilité qu’une commande soit frauduleuse.

2. Définissez les **objectifs du projet** en posant et affinant des questions « précises » qui sont à la fois pertinentes, spécifiques et non équivoques. La science des données est le processus consistant à utiliser des noms et des nombres pour répondre à ces questions. Pour plus d’informations sur la manière de poser des questions précises, voir le blog [Comment pratiquer la science des données](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/). La science des données et l’apprentissage automatique sont généralement utilisés pour répondre à cinq types de questions :
 
   * Quelle quantité (ou combien) ? (régression)
   * Quelle catégorie ? (classification)
   * Quel groupe ? (clustering)
   * Est-ce étrange ? (détection des anomalies)
   * Quelle est l’option appropriée ? (recommandation)

    Déterminez laquelle de ces questions vous posez et comment le fait d’y répondre vous permet d’atteindre vos objectifs professionnels.

3. Définissez **l’équipe de projet** en spécifiant les rôles et responsabilités de ses membres. Développez un plan à étapes général auquel vous vous référez au fil de la découverte de nouvelles informations.  

4. **Définissez des mesures de réussite**. Par exemple : atteindre la précision de prédiction de la désinscription des clients de X % à la fin de ce projet de 3 mois, afin de pouvoir proposer des promotions pour réduire la désinscription. Les mesures doivent satisfaire à l’acronyme **SMART** : 
   * **S**pécifiques 
   * **M**esurables
   * **A**tteignables 
   * **R**elevant (pertinentes) 
   * **T**ime-bound (associées à un délai) 

### <a name="12-identify-data-sources"></a>1.2 Identifier les sources de données
Identifiez les sources de données qui contiennent des exemples connus de réponses à vos questions précises. Recherchez les données suivantes :

* Données **pertinentes** pour la question. Disposons-nous de mesures de la cible et de caractéristiques qui sont associées à la cible ?
* Données qui constituent une **mesure exacte** de notre cible de modèle et des caractéristiques dignes d’intérêt.

Il n’est pas rare, par exemple, de constater que les systèmes existants doivent collecter et consigner d’autres types de données pour résoudre le problème et atteindre les objectifs du projet. Dans ce cas, vous pouvez rechercher des sources de données externes ou mettre à jour vos systèmes pour collecter des données récentes.

## <a name="artifacts"></a>Artefacts
Voici les livrables de cette phase :

* [**Charte**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md) : un modèle standard est fourni dans la définition de la structure de projet TDSP. Il s’agit d’un document actif qui est mis à jour tout au long du projet en fonction des nouvelles découvertes et de l’évolution des exigences pour l’entreprise. Il est important que vous vous référiez à ce document, en ajoutant des détails, à mesure que vous progressez dans le processus de découverte. Veillez à ce que les clients et autres parties prenantes demeurent impliqués dans la réalisation des modifications et communiquez-leur clairement les raisons des modifications.  
* [**Sources de données**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources) : il s’agit de la section **Raw Data Sources (Sources de données brutes)** du rapport **Data Definitions (Définitions des données)** qui figure dans le dossier **Data Report (Rapport de données)** du projet TDSP. Elle spécifie les emplacements d’origine et de destination des données brutes. Au cours de phases ultérieures, vous renseignez des détails supplémentaires, tels que des scripts permettant de déplacer les données vers votre environnement analytique.  
* [**Dictionnaires de données**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries) : ce document fournit des descriptions des données fournies par le client. Ces descriptions incluent des informations sur le schéma (types de données, informations sur les règles de validation, le cas échéant) et les diagrammes entité-relation s’ils sont disponibles.

## <a name="next-steps"></a>Étapes suivantes

Voici des liens vers chaque étape du cycle de vie du processus TDSP :

* [1. Présentation de l’entreprise](lifecycle-business-understanding.md)
* [2. Acquisition de données et compréhension](lifecycle-data.md)
* [3. Modélisation](lifecycle-modeling.md)
* [4. Déploiement](lifecycle-deployment.md)
* [5. Acceptation du client](lifecycle-acceptance.md)

Des procédures pas à pas complètes illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. La rubrique [Exemples de procédures pas à pas](walkthroughs.md) les répertorie et les décrit brièvement, en les accompagnant de liens. Elles illustrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente. 

Par obtenir des exemples d’exécution des étapes du processus TDSP qui utilisent Azure Machine Learning Studio, consultez le parcours de formation [Avec Azure ML](http://aka.ms/datascienceprocess).
