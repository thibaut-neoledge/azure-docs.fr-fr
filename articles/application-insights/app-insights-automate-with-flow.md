---
title: Automatiser les processus Azure Application Insights avec Microsoft Flow
description: "Découvrez comment utiliser Microsoft Flow pour automatiser rapidement des processus reproductibles en utilisant le connecteur Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: a1d2787626ed8fa71e3e4e9921ffb8a4680014cb
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2017
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatiser les processus Azure Application Insights avec le connecteur pour Microsoft Flow

Vous devez exécuter à plusieurs reprises les mêmes requêtes sur vos données de télémétrie pour vérifier le bon fonctionnement de votre service ? Cherchez-vous à automatiser ces requêtes pour rechercher les tendances et les anomalies, puis générer vos propres flux de travail autour d’elles ? Le connecteur Azure Application Insights (préversion) pour Microsoft Flow est l’outil adapté à ces objectifs.

Avec cette intégration, vous pouvez désormais automatiser de nombreux processus sans écrire la moindre ligne de code. Après avoir créé un flux à l’aide d’une action Application Insights, le flux exécute automatiquement votre requête Application Insights Analytics. 

Vous pouvez également ajouter des actions. Microsoft Flow met à votre disposition des centaines d’actions. Par exemple, vous pouvez utiliser Microsoft Flow pour envoyer automatiquement une notification par courrier électronique ou créer un bogue dans Visual Studio Team Services. Vous pouvez également utiliser l’un des nombreux [modèles](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) disponibles pour le connecteur pour Microsoft Flow. Ces modèles accélèrent le processus de création d’un flux. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Créer un flux pour Application Insights

Dans ce didacticiel, vous allez apprendre à créer un flux qui utilise l’algorithme de clusters automatiques Analytics aux attributs de groupe dans les données pour une application web. Ce flux envoie automatiquement les résultats par courrier électronique. Il s’agit d’un exemple de la façon dont vous pouvez utiliser Microsoft Flow et Application Insights Analytics ensemble. 

### <a name="step-1-create-a-flow"></a>Étape 1 : Créer un flux
1. Connectez-vous à [Microsoft Flow](http://flow.microsoft.com), puis sélectionnez **Mes flux**.
2. Cliquez sur **Créer un flux à partir de rien**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Étape 2 : Créer un déclencheur pour votre flux
1. Sélectionnez **Planifier**, puis **Planification - Récurrence**.
2. Dans la zone **Fréquence**, sélectionnez **Jour** et, dans la zone **Intervalle**, entrez **1**.

    ![Boîte de dialogue du déclencheur de flux Microsoft Flow](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Étape 3 : Ajouter une action Application Insights
1. Cliquez sur **Nouvelle étape**, puis sur **Ajouter une action**.
2. Recherchez **Azure Application Insights**.
3. Cliquez sur **Azure Application Insights – Visualize Analytics query Preview** (Azure Application Insights – Visualiser la requête Analytics Préversion).

    ![Fenêtre d’exécution de la requête Analytics](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Étape 4 : Se connecter à une ressource Application Insights

Pour cette étape, vous avez besoin d’un ID d’application et d’une clé d’API pour votre ressource. Vous pouvez les récupérer sur le portail Azure, comme illustré dans le schéma suivant :

![ID d’application dans le portail Azure](./media/app-insights-automate-with-flow/appid.png) 

- Renseignez le nom de votre connexion, l’ID d’application et la clé d’API.

    ![Fenêtre de connexion Microsoft Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Étape 5 : Spécifier le type de requête et de graphique Analytics
Cet exemple de requête sélectionne les requêtes ayant échoué au cours du dernier jour et les met en corrélation avec les exceptions qui se sont produites dans le cadre de l’opération. Analytics les met en corrélation en fonction de l’identificateur operation_Id. La requête segmente ensuite les résultats à l’aide de l’algorithme de cluster automatique. 

Lorsque vous créez vos propres requêtes, vérifiez qu’elles fonctionnent correctement dans Analytics avant de les ajouter à votre flux.

- Ajoutez la requête Analytics suivante, puis sélectionnez le type de graphique de tableau HTML. 

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
    
    ![Fenêtre de configuration de requête Analytics](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Étape 6 : Configurer le flux pour envoyer un courrier électronique

1. Cliquez sur **Nouvelle étape**, puis sur **Ajouter une action**.
2. Recherchez **Office 365 Outlook**.
3. Cliquez sur **Office 365 Outlook – Envoyer un message électronique**.

    ![Fenêtre de sélection d’Office 365 Outlook](./media/app-insights-automate-with-flow/flow2b.png)

4. Dans la fenêtre **Envoyer un message électronique**, effectuez les étapes suivantes :

   a. Tapez l’adresse e-mail du destinataire.

   b. Tapez l’objet de l’e-mail.

   c. Cliquez n’importe où dans la zone **Corps**, puis, dans le menu de contenu dynamique qui s’ouvre sur la droite, sélectionnez **Corps**.

   d. Cliquez sur **Afficher les options avancées**.

    ![Configuration d’Office 365 Outlook](./media/app-insights-automate-with-flow/flow5.png)

5. Dans le menu de contenu dynamique, effectuez les étapes suivantes :

    a. Sélectionnez **Nom de la pièce jointe**.

    b. Sélectionnez **Attachment Content** (Contenu de la pièce jointe).
    
    c. Dans la zone **Is HTML** (Est HTML), sélectionnez **Oui**.

    ![Fenêtre de configuration d’e-mail Office 365](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Étape 7 : Enregistrer et tester votre flux
- Dans la zone **Nom du flux**, ajoutez un nom pour votre flux, puis cliquez sur **Créer un flux**.

    ![Fenêtre de création de flux](./media/app-insights-automate-with-flow/flow8.png)

Vous pouvez attendre que le déclencheur exécute cette action ou vous pouvez exécuter immédiatement le flux en [exécutant le déclencheur à la demande](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Lorsque le flux s’exécute, les destinataires que vous avez spécifiés dans la liste des e-mails reçoivent un e-mail similaire à :

![Exemple de message électronique](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Étapes suivantes

- Découvrez la création de [requêtes Analytics](app-insights-analytics-using.md).
- Découvrez [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





