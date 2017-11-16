---
title: "Phase d’acceptation du client du cycle de vie du processus TDSP (Team Data Science Process) - Azure | Microsoft Docs"
description: "Objectifs, tâches et livrables associés à la phase d’acceptation du client de vos projets de science des données"
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
ms.date: 11/04/2017
ms.author: bradsev;
ms.openlocfilehash: e150b3e7c7e98443264dd5b8aaeda1bfe6047b2c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2017
---
# <a name="customer-acceptance"></a>Acceptation du client

Cet article présente les objectifs, tâches et livrables associés à la phase d’acceptation du client du processus TDSP. Ce processus indique un cycle de vie recommandé que vous pouvez utiliser pour structurer vos projets de science des données. Le cycle de vie expose les principales phases que les projets exécutent généralement, souvent de manière itérative :

   1. **Présentation de l’entreprise**
   2. **Acquisition et compréhension des données**
   3. **Modélisation**
   4. **Déploiement**
   5. **Acceptation du client**

Voici une représentation visuelle du cycle de vie TDSP : 

![Cycle de vie TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objectif
**Finaliser les livrables du projet** : vérifiez que le pipeline, le modèle et leur déploiement dans un environnement de production sont conformes aux objectifs du client.

## <a name="how-to-do-it"></a>Marche à suivre
Deux tâches principales sont traitées dans cette phase :

   * **Validation du système** : vérifiez que le modèle et le pipeline déployés répondent aux besoins du client.
   * **Remise du projet** : livrez le projet à l’entité chargée d’exécuter le système dans l’environnement de production.

Le client doit confirmer que le système répond à ses besoins professionnels et que les réponses apportées aux questions sont suffisamment précises pour permettre le déploiement du système en production en vue de son utilisation par son application cliente. Toute la documentation est finalisée et révisée. Le projet est remis à l’entité responsable des opérations. Cette entité peut être une équipe de science des données au sein du service informatique ou chez le client ou un agent du client chargé d’exécuter le système dans l’environnement de production. 

## <a name="artifacts"></a>Artefacts
L’artefact principal produit dans cette étape finale est le **Rapport de clôture du projet pour le client**. Ce rapport technique contient tous les détails du projet qui sont utiles pour comprendre le fonctionnement du système. Le processus TDSP fournit un modèle de [rapport de clôture](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md). Vous pouvez utiliser le modèle tel quel ou le personnaliser pour les besoins des clients spécifiques. 


## <a name="next-steps"></a>Étapes suivantes

Voici les liens vers chaque étape du cycle de vie TDSP :

   1. [Présentation de l’entreprise](lifecycle-business-understanding.md)
   2. [Acquisition et compréhension des données](lifecycle-data.md)
   3. [Modélisation](lifecycle-modeling.md)
   4. [Déploiement](lifecycle-deployment.md)
   5. [Acceptation du client](lifecycle-acceptance.md)

Nous indiquons des procédures pas à pas complètes qui illustrent toutes les étapes du processus correspondant à des scénarios spécifiques. L’article [Example walkthroughs](walkthroughs.md) (Exemples de procédures pas à pas) contient une liste des scénarios ainsi que des liens et des descriptions de miniatures. Les procédures pas à pas montrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente. 

Pour obtenir des exemples sur l’exécution de procédures dans les processus TDSP utilisant Azure Machine Learning Studio, consultez [Utilisation du processus de science des données avec Azure Machine Learning](http://aka.ms/datascienceprocess).
