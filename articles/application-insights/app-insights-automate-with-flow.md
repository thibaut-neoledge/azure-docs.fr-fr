---
title: Automatiser les processus Azure Application Insights avec Microsoft Flow
description: "Découvrez comment utiliser Microsoft Flow pour automatiser rapidement des processus reproductibles à l’aide d’Application Insights Connector pour Flow."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 7e17fde55097a8e852be33bac66940a05fdd9df6
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatiser les processus Application Insights avec Connector pour Microsoft Flow

Vous devez exécuter à plusieurs reprises les mêmes requêtes sur vos données de télémétrie pour vérifier le bon fonctionnement de votre service ? Vous cherchez à automatiser ces requêtes pour rechercher les tendances et les anomalies, et générer vos propres flux de travail autour d’elles ? Application Insights Connector (préversion) pour Microsoft Flow est exactement ce dont vous avez besoin !
Avec cette intégration, plusieurs processus peuvent maintenant être automatisés sans l’écriture d’une seule ligne de code. Après avoir créé un flux à l’aide d’une action Application Insights, votre requête Application Insights Analytics s’exécute automatiquement. Vous pouvez ajouter des actions supplémentaires. Flow offre des centaines d’actions à disposition. Par exemple, vous pouvez utiliser Flow pour envoyer automatiquement une notification par courrier électronique ou créer un bogue dans Visual Studio Team Services. Vous pouvez également utiliser l’un des nombreux [modèles](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) disponibles pour Microsoft Flow Connector. Ces modèles accélèrent le processus de création d’un flux. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="tutorial-for-creating-a-flow-for-application-insights"></a>Didacticiel de création d’un flux pour Application Insights

Dans ce didacticiel, vous allez apprendre à créer un flux qui utilise l’algorithme de clusters automatiques Analytics aux attributs de groupe dans les données pour une application web. Le flux envoie automatiquement les résultats par courrier électronique. Il s’agit d’un exemple de la façon dont vous pouvez combiner Microsoft Flow et Application Insights Analytics. 

### <a name="step-1-create-a-flow"></a>Étape 1 : Créer un flux
1. Connectez-vous sur : http://flow.microsoft.com et sélectionnez **Mes flux**.
2. Cliquez sur **Créer un flux à partir de rien**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Étape 2 : Créer un déclencheur pour votre flux
1. Choisissez **Planifier** , puis **Planification - Récurrence**.
2. Définissez la **Fréquence** Sur Jour avec un **intervalle** de 1.

![Boîte de dialogue du déclencheur de flux](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-azure-application-insights-action"></a>Étape 3 : Ajouter une action Application Azure Insights
1. Cliquez sur **Nouvelle étape**, puis sélectionnez **Ajouter une action**.
2. Recherchez Azure Application Insights.
3. Cliquez sur Azure Application Insights : préversion de la requête de visualisation Analytics.

![Écran de la requête Exécuter Analytics](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Étape 4 : Se connecter à une ressource Application Insights

**Configuration requise**

Vous avez besoin d’un ID d’application et d’une clé API pour votre ressource afin d’effectuer cette étape. Vous pouvez les récupérer dans le portail Azure, comme illustré dans le diagramme suivant :

![ID d’application dans le portail Azure](./media/app-insights-automate-with-flow/appid.png) 

- Nommez votre connexion, l’ID d’application et la clé API.

![Écran de connexion Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Étape 5 : Spécifier le type de requête et de graphique Analytics
Cet exemple sélectionne les requêtes ayant échoué au cours du dernier jour et les met en corrélation avec les exceptions qui se sont produites dans le cadre de l’opération. Analytics établit des corrélations en fonction de l’identificateur operation_Id. La requête segmente ensuite les résultats à l’aide de l’algorithme de cluster automatique. Lorsque vous créez vos propres requêtes, pensez à vérifier qu’elles fonctionnent correctement dans Analytics avant de les ajouter à votre flux.

- Ajoutez la requête Analytics suivante et sélectionnez le type de graphique de tableau HTML. 

```
requests
| where timestamp > ago(1d)
| where success == "False"
| project name, operation_Id
| join ( exceptions
    | project problemId, outerMessage, operation_Id
) on operation_Id
| evaluate autocluster()
```
![Écran de configuration de requête Analytics](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Étape 6 : Configurer le flux pour envoyer un courrier électronique

1. Cliquez sur **Nouvelle étape**, puis sélectionnez **Ajouter une action**.
2. Recherchez Office 365 Outlook.
3. Cliquez sur Office 365 Outlook : Envoi d’un courrier électronique.
![Sélection d’Office 365 Outlook](./media/app-insights-automate-with-flow/flow2b.png)

4. Dans l’action de messagerie, ajoutez les données suivantes :
 - Spécifiez l’adresse e-mail du destinataire
 - Fournissez un objet au courrier électronique
 - Placez votre curseur dans le champ **Corps**, puis dans le menu de contenu dynamique qui s’ouvre sur la droite, sélectionnez **Corps**.
 - Cliquez sur **Afficher les options avancées**.

 ![Configuration d’Office 365 Outlook](./media/app-insights-automate-with-flow/flow5.png)

5. Dans le menu de contenu dynamique, procédez comme suit :
- Sélectionnez **Nom de la pièce jointe**
- Sélectionnez **Contenu de la pièce jointe**
- Sélectionnez **Oui** dans le champ **Est HTML**

![Écran de configuration de messagerie Office 365](./media/app-insights-automate-with-flow/flow7.png)
### <a name="step-7-save-and-test-your-flow"></a>Étape 7 : Enregistrer et tester votre flux
- Nommez votre flux et cliquez sur **Créer un flux**.

![Écran de création Microsoft Flow](./media/app-insights-automate-with-flow/flow8.png)

Vous pouvez attendre que le déclencheur exécute cette action, ou vous pouvez exécuter immédiatement le flux en [exécutant le déclencheur à la demande](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Lorsque le flux est exécuté, les destinataires que vous avez spécifiés dans la liste des e-mails reçoivent un message du type suivant :

![Exemple de message électronique](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Étapes suivantes

- Découvrez la création de [requêtes Analytics](app-insights-analytics-using.md).
- Découvrez [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->






