---
title: Tarification et facturation - Azure Logic Apps | Microsoft Docs
description: "Découvrez comment fonctionnement la tarification et la facturation avec Azure Logic Apps."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.openlocfilehash: 63784c5e3af360b2f3f8cb330a9df8b27a85d859
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="logic-apps-pricing-model"></a>Modèle de tarification de Logic Apps
Azure Logic Apps vous permet de mettre à l’échelle et d’exécuter les flux de travail d’intégration dans le cloud.  Vous trouverez ci-après plus d’informations sur les plans de facturation et les niveaux de tarification de Logic Apps.
## <a name="consumption-pricing"></a>Tarification de la consommation
La plateforme Logic Apps récemment créée utilise un plan de consommation. Avec le modèle de tarification de la consommation de Logic Apps, vous payez uniquement ce que vous utilisez.  La plateforme Logic Apps n’est pas limitée lors de l’utilisation d’un plan de consommation.
Toutes les actions effectuées lors de l’exécution d’une instance de l’application logique sont mesurées.
### <a name="what-are-action-executions"></a>Que sont les exécutions d’action ?
Chaque étape dans une définition d’application logique est une action, ce qui inclut des déclencheurs, des étapes de flux de contrôle comme les conditions, des étendues, des boucles foreach et do until, ainsi que des appels aux connecteurs et aux actions natives.
Les déclencheurs sont des actions spéciales qui sont conçues pour instancier une nouvelle instance d’une application logique lorsqu’un événement particulier se produit.  Les déclencheurs peuvent avoir différents comportements, qui peuvent affecter la manière dont l’application logique est mesurée.
* **Déclencheur d’interrogation** : ce déclencheur interroge constamment un point de terminaison jusqu’à ce qu’il reçoive un message répondant aux critères de création d’une instance d’une application logique.  L’intervalle d’interrogation peut être configuré dans le déclencheur, dans le concepteur d’applications logiques.  Chaque requête d’interrogation compte comme une exécution d’action, même si elle ne crée pas d’instance d’une application logique.
* **Déclencheur webhook** : ce déclencheur attend qu’un client lui envoie une requête sur un point de terminaison particulier.  Chaque requête envoyée au point de terminaison webhook est considérée comme une exécution d’action. Le déclencheur de requête et le déclencheur Webhook HTTP sont tous deux des déclencheurs webhook.
* **Déclencheur de périodicité** : ce déclencheur crée une instance de l’application logique en fonction de l’intervalle de périodicité configuré dans le déclencheur.  Par exemple, un déclencheur de périodicité peut être configuré pour s’exécuter tous les trois jours ou toutes les minutes.

Les exécutions de déclencheur sont visibles dans le panneau des ressources de Logic Apps dans la partie Historique du déclencheur.

Toutes les actions exécutées, qu’il s’agisse de réussites ou d’échecs, sont considérées comme des exécutions d’action.  Les actions qui ont été ignorées en raison d’une condition non remplie ou celles qui n’ont pas été exécutées, car l’application logique s’est arrêtée avant son achèvement ne sont pas considérées comme des exécutions d’action.

Les actions exécutées dans des boucles sont comptabilisées par itération de la boucle.  Par exemple, une seule et même action d’une boucle foreach en itération dans une liste de 10 éléments est comptabilisée comme le nombre d’éléments de la liste (10) multiplié par le nombre d’actions de la boucle (1) plus une correspondant à l’initiation de la boucle, ce qui, dans cet exemple, représente (10 * 1) + 1 = 11 exécutions d’action.
Il n’est pas possible de créer de nouvelles instances des applications logiques qui sont désactivées. Par conséquent, pendant leur période de désactivation, elles ne peuvent pas être facturées.  N’oubliez pas qu’une fois une application logique désactivée, la suspension de ses instances, avant leur désactivation complète, peut prendre un certain temps.
### <a name="integration-account-usage"></a>Utilisation d’un compte d’intégration
Un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) est inclus dans la consommation en fonction de l’utilisation à des fins d’exploration, de développement et de test, ce qui vous permet d’utiliser les fonctionnalités de traitement [B2B/EDI](logic-apps-enterprise-integration-b2b.md) et [XML](logic-apps-enterprise-integration-xml.md) de Logic Apps sans coût supplémentaire. Vous pouvez créer au maximum un seul compte par région et stocker jusqu'à 10 accords et 25 cartes. Il n’y a pas de limite pour les schémas, les certificats et les partenaires et vous pouvez en télécharger autant que vous en avez besoin.

Outre l’inclusion de comptes d’intégration en fonction de la consommation, vous pouvez également créer des comptes d’intégration standard sans ces limites et avec notre contrat de niveau de service Logic Apps standard. Pour plus d’informations, consultez la page [Tarification Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="app-service-plans"></a>Plans App Service
Les applications logiques créées précédemment faisant référence à un plan App Service gardent le même comportement. Selon le plan choisi, elles sont limitées après le dépassement des exécutions quotidiennes prescrites, mais sont facturées à l’aide de l’indicateur de l’exécution d’action.
Les clients EA qui ont un plan App Service dans leur abonnement, qui ne doit pas être explicitement associé à l’application logique, tirent parti des quantités incluses.  Par exemple, si vous avez un plan App Service standard dans votre abonnement EA et une application logique dans le même abonnement, il ne vous est pas facturé 10 000 exécutions d’action par jour (voir tableau suivant). 

Plans App Service et exécutions d’action quotidiennes autorisées :
|  | Gratuit/Partagé/De base | Standard | Premium |
| --- | --- | --- | --- |
| Exécutions d’action quotidiennes |200 |10 000 |50 000 |
### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Convertir la tarification de plan App Service en tarification de type Consommation
Pour modifier une application logique à laquelle un plan App Service est associé et la définir sur un modèle de consommation, supprimez la référence au plan App Service dans la définition de l’application logique.  Cette modification peut être effectuée par le biais d’un appel à une applet de commande PowerShell : `Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`
## <a name="pricing"></a>Tarification
Pour plus d’informations sur la tarification, voir la page de [tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble de Logic Apps][whatis]
* [Créer votre première application logique][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

