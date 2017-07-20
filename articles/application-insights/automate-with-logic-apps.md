---
title: Automatisez les processus Azure Application Insights avec une application logique Azure.
description: "Découvrez comment automatiser rapidement des processus reproductibles à l’aide du connecteur Application Insights pour votre application logique Azure."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 571a76253fa62a89f325e0c295e9a7e98e201079
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-an-azure-logic-app"></a>Automatiser les processus Application Insights avec une application logique Azure

Vous devez exécuter à plusieurs reprises les mêmes requêtes sur vos données de télémétrie pour vérifier le bon fonctionnement de votre service ? Vous cherchez à automatiser ces requêtes pour rechercher les tendances et les anomalies, et générer vos propres flux de travail autour d’elles ? Application Insights Connector (préversion) pour Azure Logic Apps est exactement ce dont vous avez besoin !
Avec cette intégration, plusieurs processus peuvent maintenant être automatisés sans l’écriture d’une seule ligne de code. Vous pouvez créer votre application logique avec le connecteur Application Insights pour rapidement automatiser tous les processus Application Insights. Vous pouvez également ajouter des actions. Logic Apps offre des centaines d’actions à disposition. Par exemple, vous pouvez automatiquement envoyer une notification par e-mail ou créer un bogue dans Visual Studio Team Services pour votre application logique. Vous pouvez également utiliser l’un des nombreux [modèles](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) disponibles pour Logic Apps. Ces modèles accélèrent le processus de création de votre application logique. 

## <a name="tutorial-for-creating-an-azure-logic-app-for-application-insights"></a>Didacticiel de création d’une application logique Azure pour Application Insights

Dans ce didacticiel, vous apprenez à créer une application logique Azure qui utilise l’algorithme de cluster automatique Analytics pour regrouper des attributs dans les données pour une application web. Le flux envoie automatiquement les résultats par e-mail. Il s’agit d’un exemple de la façon dont vous pouvez utiliser Application Insights Analytics et Azure Logic Apps ensemble. 

### <a name="step-1-create-a-logic-app"></a>Étape 1 : Créez une application logique
1. Connectez-vous à https://portal.azure.com.
2. Créez une application logique à partir du menu Nouveau/Web + mobile.

![Écran Nouvelle application logique](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Étape 2 : Créez un déclencheur pour votre application logique
1.  Dans le concepteur d’application logique, sous Démarrer avec un déclencheur courant, choisissez Périodicité.
2.  Définissez le paramètre Fréquence sur Jour avec un intervalle de 1.

![Boîte de dialogue Déclencheur d’application logique](./media/automate-with-logic-apps/logicapp2.png)

![Boîte de dialogue Fréquence d’application logique](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-azure-application-insights-action"></a>Étape 3 : Ajoutez une action Application Azure Insights
1. Cliquez sur **Nouvelle étape**, puis sélectionnez **Ajouter une action**.
2. Recherchez Azure Application Insights.
3. Cliquez sur Azure Application Insights: Visualize Analytics query Preview (Azure Application Insights : Visualiser la requête - préversion).

![Écran d’exécution de la requête Analytics](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Étape 4 : Connectez-vous à une ressource Application Insights

**Configuration requise**

Vous avez besoin d’un ID d’application et d’une clé d’API pour votre ressource afin d’effectuer cette étape. Vous pouvez les récupérer dans le portail Azure, comme illustré dans le schéma suivant :

![ID d’application dans le portail Azure](./media/automate-with-logic-apps/appid.png) 

- Renseignez le nom de votre connexion, l’ID d’application et la clé d’API.

![Écran de connexion Flow](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Étape 5 : Spécifiez le type de requête et de graphique Analytics
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
![Écran de configuration de requête Analytics](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-app-to-send-email"></a>Étape 6 : Configurez l’application pour envoyer un e-mail

1. Cliquez sur **Nouvelle étape**, puis sélectionnez **Ajouter une action**.
2. Recherchez Office 365 Outlook.
3. Cliquez sur Office 365 Outlook - Envoyer un message électronique.
![Sélection d’Office 365 Outlook](./media/automate-with-logic-apps/flow2b.png)

4. Dans l’action d’envoi d’un e-mail, ajoutez les données suivantes :
 - Spécifiez l’adresse e-mail du destinataire.
 - Indiquez un objet pour l’e-mail.
 - Placez votre curseur dans le champ **Corps**, puis dans le menu de contenu dynamique qui s’ouvre sur la droite, sélectionnez **Corps**.
 - Cliquez sur **Afficher les options avancées**.

 ![Configuration d’Office 365 Outlook](./media/automate-with-logic-apps/flow5.png)

5. Dans le menu de contenu dynamique, procédez comme suit :
- Sélectionnez **Nom de la pièce jointe**.
- Sélectionnez **Attachment Content** (Contenu de la pièce jointe).
- Sélectionnez **Oui** dans le champ **Is HTML** (Est HTML).

![Écran de configuration d’e-mail Office 365](./media/automate-with-logic-apps/flow7.png)
### <a name="step-7-save-and-test-your-logic-app"></a>Étape 7 : Enregistrez et testez votre application logique
1. Cliquez sur **Enregistrer** pour enregistrer vos modifications.
1. Vous pouvez attendre que le déclencheur exécute l’application logique ou vous pouvez l’exécuter immédiatement en sélectionnant **Exécuter**.

![Écran de création d’application logique](./media/automate-with-logic-apps/step7.png)

Lorsque votre application logique s’exécute, les destinataires que vous avez spécifiés dans la liste des e-mails reçoivent un e-mail similaire à ce qui suit :

![E-mail d’application logique](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur la création de [requêtes Analytics](app-insights-analytics-using.md).
- Apprenez-en plus sur [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->






