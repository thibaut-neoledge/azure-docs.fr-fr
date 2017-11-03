---
title: "Résoudre les problèmes et diagnostiquer les échecs - Azure Logic Apps | Microsoft Docs"
description: "Comprendre comment et pourquoi les applications logiques entraînent des échecs"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 10/15/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: de706f711e9c57b2e575d130a2a0cfd0bdc907a1
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2017
---
# <a name="troubleshoot-and-diagnose-logic-app-failures"></a>Résoudre les problèmes et diagnostiquer les échecs d’applications logiques

Votre application logique génère des informations qui peuvent vous aider à diagnostiquer et déboguer ses problèmes. Vous pouvez effectuer le diagnostic d’une application logique en examinant chaque étape dans le flux de travail via le portail Azure. Vous pouvez également ajouter des étapes à un flux de travail pour effectuer un débogage du runtime.

## <a name="review-trigger-history"></a>Examiner l’historique du déclencheur

Chaque application logique démarre avec un déclencheur. Si le déclencheur n’est pas activé, commencez par consulter son historique. Cet historique répertorie toutes les tentatives de déclenchement effectuées par votre application logique et contient des détails sur les entrées et sorties pour chacune d’entre elles.

1. Pour vérifier si le déclencheur a été activé, dans le menu de votre application logique, sélectionnez **Vue d’ensemble**. Dans **Historique du déclencheur**, examinez l’état du déclencheur.

   > [!TIP]
   > Si vous ne voyez pas le menu de l’application logique, essayez de revenir au tableau de bord Azure et de rouvrir votre application logique.

   ![Examiner l’historique du déclencheur](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Si vous ne trouvez pas les données que vous attendez, choisissez **Actualiser** dans la barre d’outils.
   > * Si la liste affiche plusieurs tentatives de déclenchement et que vous ne trouvez pas l’entrée que vous cherchez, essayez de filtrer la liste.

   Voici les états possibles d’une tentative de déclenchement :

   | État | Description | 
   | ------ | ----------- | 
   | **Réussi** | Le déclencheur a vérifié le point de terminaison et a trouvé des données disponibles. En règle générale, un état « Déclenché » s’affiche également à côté de cet état. Dans le cas contraire, la définition du déclencheur peut comporter une condition ou une commande `SplitOn` qui n’a pas été satisfaite. <p>Cet état peut provenir d’un déclencheur manuel, d’un déclencheur de périodicité ou d’un déclencheur d’interrogation. Un déclencheur peut s’exécuter correctement, mais l’exécution elle-même risque malgré tout d’échouer si les actions génèrent des erreurs non gérées. | 
   | **Ignoré** | Le déclencheur a vérifié le point de terminaison, mais n’a trouvé aucune donnée. | 
   | **Échec** | Une erreur est survenue. Pour consulter les messages d’erreur générés suite à un échec de déclencheur, sélectionnez cette tentative de déclenchement, puis choisissez **Sorties**. Vous pourrez par exemple trouver des entrées qui ne sont pas valides. | 
   ||| 

   Vous pouvez avoir plusieurs entrées de déclencheur avec les mêmes date et heure, ce qui se produit quand votre application logique trouve plusieurs éléments. 
   Chaque fois que le déclencheur est activé, le moteur Logic Apps crée une instance d’application logique qui exécute votre flux de travail. Par défaut, chaque instance s’exécute en parallèle afin qu’aucun flux de travail ne doive attendre avant de lancer l’exécution.

   > [!TIP]
   > Vous pouvez revérifier le déclencheur sans attendre la prochaine récurrence. Dans la barre d’outils de la vue d’ensemble, choisissez **Run trigger** (Exécuter le déclencheur), puis sélectionnez le déclencheur, ce qui force une vérification. Vous pouvez également sélectionner **Exécuter** dans la barre d’outils du Concepteur d’applications logiques.

3. Pour examiner les détails d’une tentative de déclenchement, sélectionnez-la dans **Historique du déclencheur**. 

   ![Sélectionner une tentative de déclenchement](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Passez en revue les entrées et les sorties générées par le déclencheur. Les sorties du déclencheur affichent les données reçues du déclencheur. Ces sorties peuvent vous aider à déterminer si toutes les propriétés ont renvoyé les valeurs attendues.

   > [!NOTE]
   > Si vous rencontrez un contenu que vous ne comprenez pas, découvrez comment Azure Logic Apps [gère les différents types de contenu](../logic-apps/logic-apps-content-type.md).

   ![Sorties du déclencheur](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Examiner l’historique des exécutions

Chaque déclencheur activé démarre une exécution de flux de travail. Vous pouvez examiner le déroulement de cette exécution, y compris l’état de chaque étape dans le flux de travail, ainsi que les entrées et les sorties pour chacune d’entre elles.

1. Dans le menu de l’application logique, choisissez **Vue d’ensemble**. Sous **Historique des exécutions**, passez en revue l’exécution du déclencheur activé.

   > [!TIP]
   > Si vous ne voyez pas le menu de l’application logique, essayez de revenir au tableau de bord Azure et de rouvrir votre application logique.

   ![Examiner l’historique des exécutions](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Si vous ne trouvez pas les données que vous attendez, choisissez **Actualiser** dans la barre d’outils.
   > * Si la liste affiche plusieurs exécutions et que vous ne trouvez pas l’entrée que vous cherchez, essayez de filtrer la liste.

   Voici les états possibles d’une exécution :

   | État | Description | 
   | ------ | ----------- | 
   | **Réussi** | Toutes les actions ont réussi. <p>Si des échecs se sont produits dans une action spécifique, une action ultérieure dans le flux de travail a géré cette défaillance. | 
   | **Échec** | Au moins une action a échoué, et aucune action ultérieure dans le flux de travail n’a été configurée pour gérer l’échec. | 
   | **Annulé** | Le workflow était en cours d’exécution mais a reçu une demande d’annulation. | 
   | **Exécution** | Si un workflow est en cours d’exécution. <p>Cet état peut se produire pour les flux de travail limités ou en raison du plan de tarification actuel. Pour plus d’informations, consultez les [limites liées aux actions sur la page relative à la tarification](https://azure.microsoft.com/pricing/details/logic-apps/). Si vous configurez la [journalisation des diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md), vous pouvez également obtenir des informations sur tous les événements de limitation qui se produisent. | 
   ||| 

2. Passez en revue les détails de chaque étape dans une exécution spécifique. Dans **Historique des exécutions**, sélectionnez l’exécution que vous souhaitez examiner.

   ![Examiner l’historique des exécutions](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Que l’exécution elle-même ait réussi ou échoué, la vue Détails de l’exécution présente chaque étape et indique si elle a réussi ou échoué.

   ![Affichage des détails relatifs à une exécution d’application logique](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Pour examiner les entrées, les sorties et les messages d’erreur pour une étape spécifique, cliquez sur cette étape afin que la zone se développe et affiche les détails. Par exemple :

   ![Affichage des détails de l’étape](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Effectuer le débogage du runtime

Pour faciliter le débogage, vous pouvez ajouter des étapes de diagnostic à un flux de travail, ainsi qu’examiner les historiques des déclencheurs et des exécutions. Par exemple, vous pouvez ajouter des étapes qui utilisent le service [RequestBin](http://requestb.in) afin de pouvoir inspecter les requêtes HTTP et déterminer précisément leur taille, leur forme et leur format.

1. Créez un élément RequestBin, que vous pouvez rendre privé et visible uniquement dans votre navigateur.

2. Dans votre application logique, ajoutez une action HTTP POST avec le contenu du corps que vous voulez tester (par exemple, une expression ou une autre sortie de l’étape).

3. Collez l’URL de votre élément RequestBin dans l’action HTTP POST.

4. Pour voir comment une requête a été formée lors de sa génération à partir du moteur Logic Apps, exécutez l’application logique, puis actualisez votre élément RequestBin.

## <a name="next-steps"></a>Étapes suivantes

[Analyser votre application logique](../logic-apps/logic-apps-monitor-your-logic-apps.md)