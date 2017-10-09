---
title: "Phase d’acceptation du client du cycle de vie du processus TDSP (Team Data Science Process) - Azure | Microsoft Docs"
description: "Décrit les objectifs, les tâches et les livrables associés à la phase d’acceptation du client de vos projets de science des données."
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
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="customer-acceptance"></a>Acceptation du client

Cette rubrique présente les objectifs, les tâches et les livrables associés à la **phase d’acceptation du client** du processus TDSP (Team Data Science Process). Ce processus indique un cycle de vie recommandé que vous pouvez utiliser pour structurer vos projets de science des données. Le cycle de vie présente les étapes majeures que les projets exécutent généralement, souvent de manière itérative :

* **Présentation de l’entreprise**
* **Acquisition de données et compréhension**
* **Modélisation**
* **Déploiement**
* **Acceptation du client**

Voici une représentation visuelle du **cycle de vie du processus TDSP (Team Data Science Process)**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objectif
* **Finaliser les livrables du projet** : vérifiez que le pipeline, le modèle et leur déploiement dans un environnement de production sont conformes aux objectifs des clients.

## <a name="how-to-do-it"></a>Marche à suivre
Deux tâches principales sont traitées dans cette phase :

* **Validation du système** : vérifiez que le modèle et le pipeline déployés répondent aux besoins des clients.
* **Remise du projet** : à l’entité chargée d’exécuter le système en environnement de production.

Le client doit confirmer que le système répond à ses besoins professionnels et que les réponses apportées aux questions sont suffisamment précises pour permettre le déploiement du système en production en vue de son utilisation par son application cliente. Toute la documentation est finalisée et révisée. Le projet est remis à l’entité responsable des opérations. Cette entité peut être une équipe de science des données au sein du service informatique ou chez le client ou un agent du client chargé d’exécuter le système dans l’environnement de production. 

## <a name="artifacts"></a>Artefacts
L’artefact principal produit dans cette étape finale est le **Rapport de clôture du projet pour le client**. Il s’agit du rapport technique contenant tous les détails du projet qui facilitent l’apprentissage et l’utilisation du système. Un modèle de [Rapport de clôture](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) est fourni par le processus TDSP, qui peut être utilisé en l’état ou personnalisé en fonction des besoins du client. 


## <a name="next-steps"></a>Étapes suivantes

Voici des liens vers chaque étape du cycle de vie du processus TDSP :

* [1. Présentation de l’entreprise](lifecycle-business-understanding.md)
* [2. Acquisition de données et compréhension](lifecycle-data.md)
* [3. Modélisation](lifecycle-modeling.md)
* [4. Déploiement](lifecycle-deployment.md)
* [5. Acceptation du client](lifecycle-acceptance.md)

Des procédures pas à pas complètes illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. La rubrique [Exemples de procédures pas à pas](walkthroughs.md) les répertorie et les décrit brièvement, en les accompagnant de liens. Elles illustrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente. 

Par obtenir des exemples d’exécution des étapes du processus TDSP qui utilisent Azure Machine Learning Studio, consultez le parcours d’apprentissage [Avec Azure ML](http://aka.ms/datascienceprocess).
