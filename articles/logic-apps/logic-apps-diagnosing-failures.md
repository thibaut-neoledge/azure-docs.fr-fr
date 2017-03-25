---
title: "Diagnostiquer des échecs - Azure Logic Apps | Microsoft Docs"
description: "Méthodes courantes pour comprendre les points de défaillance des applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 25f4520bae3089ce9f11efcf6acd176255043e70
ms.lasthandoff: 03/10/2017


---
# <a name="diagnose-logic-app-failures"></a>Diagnostiquer des échecs d’applications logiques
Si vous rencontrez des problèmes ou des échecs avec vos applications logiques, certaines approches peuvent vous aider à mieux comprendre l’origine des défaillances.  

## <a name="azure-portal-tools"></a>Outils du Portail Azure
Le Portail Azure fournit plusieurs outils permettant de diagnostiquer chaque application logique à différentes étapes.

### <a name="trigger-history"></a>Historique du déclencheur

Chaque application logique comporte au moins un déclencheur. Si vous constatez que les applications ne se déclenchent pas, commencez par consulter l’historique du déclencheur pour plus d’informations. Vous pouvez accéder à l’historique du déclencheur dans le panneau principal de l’application logique.

![Emplacement de l’historique du déclencheur][1]

L’historique du déclencheur répertorie tous les tentatives effectuées par le déclencheur de votre application logique. Vous pouvez cliquer sur chaque tentative du déclencheur pour explorer les détails, en particulier, les entrées ou sorties générées par la tentative du déclencheur. Si vous identifiez des déclencheurs ayant échoué, sélectionnez la tentative du déclencheur et choisissez le lien **Sorties** pour passer en revue les messages d’erreur générés, par exemple, indiquant des informations d’identification FTP non valides.

Voici les différents états que vous pouvez voir :

* **Ignoré**. Le point de terminaison a été interrogé pour vérifier les données et a reçu une réponse indiquant qu’aucune donnée n’était disponible.
* **Réussi**. Le déclencheur a reçu une réponse indiquant que les données étaient disponibles. Cet état peut provenir d’un déclencheur manuel, d’un déclencheur de périodicité ou d’un déclencheur d’interrogation. Cet état est généralement accompagné d’un état **Déclenché**, sauf si vous avez une condition ou une commande SplitOn en mode Code qui n’a pas été satisfaite.
* **Échec**. Une erreur a été générée.

#### <a name="start-a-trigger-manually"></a>Démarrer un déclencheur manuellement

Si vous souhaitez que l’application logique recherche immédiatement un déclencheur disponible (sans attendre la prochaine périodicité), vous pouvez cliquer sur **Sélectionner le déclencheur** dans le panneau principal pour forcer une vérification. Par exemple, si vous cliquez sur ce lien avec un déclencheur Dropbox, le workflow interroge immédiatement Dropbox pour rechercher de nouveaux fichiers.

### <a name="run-history"></a>Historique d’exécution

Chaque déclenchement entraîne une exécution. Vous pouvez accéder aux informations d’exécution dans le panneau principal, qui contient de nombreux détails pouvant vous aider à comprendre le déroulement du workflow.

![Emplacement de l’historique d’exécution][2]

Une exécution affiche l’un des états suivants :

* **Réussi**. Toutes les actions ont réussi. Les éventuelles défaillances ont été corrigées par une action exécutée ultérieurement dans le workflow. Autrement dit, la défaillance a été corrigée par une action configurée pour s’exécuter après l’échec d’une autre action.
* **Échec**. Au moins une action a rencontré un échec qui n’a pas été traité par une action ultérieure dans le workflow.
* **Annulé**. Le workflow était en cours d’exécution mais a reçu une demande d’annulation.
* **Exécution en cours**. Si un workflow est en cours d’exécution. Cet état peut se produire pour les workflows limités ou en raison du plan de tarification actuel. Pour en savoir plus, consultez les [limites d’action sur la page de tarification](https://azure.microsoft.com/pricing/details/app-service/plans/). La configuration des diagnostics (les graphiques apparaissant en dessous de l’historique des exécutions) peut également fournir des informations sur les événements de limitation qui se produisent.

Lorsque vous examinez un historique d’exécution, vous pouvez rechercher des détails supplémentaires.  

#### <a name="trigger-outputs"></a>Sorties du déclencheur

Les sorties du déclencheur affichent les données reçues du déclencheur. Ces sorties peuvent vous aider à déterminer si toutes les propriétés ont renvoyé les valeurs attendues.

> [!NOTE]
> Si vous rencontrez un contenu que vous ne comprenez pas, découvrez comment Azure Logic Apps [gère les différents types de contenu](../logic-apps/logic-apps-content-type.md).
> 

![Exemples de sorties du déclencheur][3]

#### <a name="action-inputs-and-outputs"></a>Entrées et sorties d’actions

Vous pouvez explorer les entrées et sorties associées à une action. Ces données sont utiles pour comprendre la taille et la forme des sorties, mais aussi pour identifier les messages d’erreur susceptibles d’avoir été générés.

![Entrées et sorties d’actions][4]

## <a name="debug-workflow-runtime"></a>Déboguer le runtime du workflow

En plus du contrôle des entrées, des sorties et des déclencheurs d’une exécution, vous pouvez ajouter des étapes à un workflow pour faciliter le débogage. 
[RequestBin](http://requestb.in) est un outil puissant que vous pouvez ajouter en tant qu’étape d’un workflow. À l’aide de RequestBin, vous configurez un inspecteur de requête HTTP qui détermine précisément la taille, la forme et le format d’une requête HTTP. Vous pouvez créer un élément RequestBin et coller l’URL dans une action HTTP POST de l’application logique avec le contenu du corps que vous voulez tester (par exemple, une expression ou une autre sortie de l’étape). Après avoir exécuté l’application logique, vous actualisez votre élément RequestBin pour voir comment la requête a été formée lors de sa génération à partir du moteur Logic Apps.

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png

