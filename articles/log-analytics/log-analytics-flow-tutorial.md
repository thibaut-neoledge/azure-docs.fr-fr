---
title: Automatiser les processus Azure Log Analytics avec Microsoft Flow
description: "Découvrez comment utiliser Microsoft Flow pour automatiser rapidement les processus récurrents en utilisant le connecteur Azure Log Analytics."
services: log-analytics
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: log-analytics
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 9884b97aad8f022e6bc20b2a630ccdd07ebc64db
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2017
---
# <a name="automate-log-analytics-processes-with-the-connector-for-microsoft-flow"></a>Automatiser les processus Log Analytics avec le connecteur Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) vous permet de créer des flux de travail automatisés à l’aide de centaines d’actions issues d’une grande variété de services. La sortie d’une action peut être utilisée en tant qu’entrée dans un autre service. Ainsi, vous pouvez créer une intégration entre différents services.  Le connecteur Azure Log Analytics pour Microsoft Flow permet de créer des flux de travail comprenant des données récupérées lors des recherches effectuées dans les journaux avec Log Analytics.

Par exemple, Microsoft Flow peut vous permettre d’utiliser les données Log Analytics dans une notification par e-mail Office 365, de créer un bogue dans Visual Studio Team Services ou de publier un message Slack.  Vous pouvez déclencher un flux de travail à l’aide d’un calendrier ou d’une action d’un service connecté, telle que la réception d’un e-mail ou d’un tweet.  

Le didacticiel de cet article vous montre comment créer un flux qui envoie automatiquement par e-mail les résultats d’une recherche effectuée dans les journaux avec Log Analytics, ce qui ne constitue qu’un exemple de ce que vous pouvez faire avec Log Analytics dans Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Étape 1 : Créer un flux
1. Connectez-vous à [Microsoft Flow](http://flow.microsoft.com), puis sélectionnez **Mes flux**.
2. Cliquez sur **+ Créer entièrement**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Étape 2 : Créer un déclencheur pour votre flux
1. Cliquez sur **Rechercher dans l’ensemble des connecteurs et des déclencheurs**.
2. Dans la zone de recherche, tapez **Planification**.
3. Sélectionnez **Planifier**, puis **Planification - Récurrence**.
4. Dans la zone **Fréquence**, sélectionnez **Jour**, et dans la zone **Intervalle**, entrez **1**.<br><br>![Boîte de dialogue du déclencheur Microsoft Flow](media/log-analytics-flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Étape 3 : Ajouter une action Log Analytics
1. Cliquez sur **+ Nouvelle étape**, puis sur **Ajouter une action**.
2. Recherchez **Log Analytics**.
3. Cliquez sur **Azure Log Analytics – Exécuter la requête et visualiser les résultats**.<br><br>![Fenêtre d’exécution de la requête Log Analytics](media/log-analytics-flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Étape 4 : Configurer l’action Log Analytics

1. Spécifiez les détails de votre espace de travail, y compris l’ID d’abonnement, le groupe de ressources et le nom de l’espace de travail.
2. Dans la zone **Requête**, ajoutez la requête Log Analytics suivante :  Ceci est un exemple de requête. Vous pouvez donc la remplacer par n’importe quelle autre requête retournant des données.
```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
```

2. Comme **Type de graphique**, sélectionnez **Tableau HTML**.<br><br>![Action Log Analytics](media/log-analytics-flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Étape 5 : Configurer le flux pour envoyer un e-mail

1. Cliquez sur **Nouvelle étape**, puis sur **+ Ajouter une action**.
2. Recherchez **Office 365 Outlook**.
3. Cliquez sur **Office 365 Outlook – Envoyer un message électronique**.<br><br>![Fenêtre de sélection d’Office 365 Outlook](media/log-analytics-flow-tutorial/flow04.png)

4. Spécifiez l’adresse e-mail d’un destinataire dans la fenêtre **À**, ainsi qu’un objet dans **Objet**.
5. Cliquez n’importe où dans la zone **Corps**.  La fenêtre **Contenu dynamique** s’ouvre avec les valeurs des actions précédentes.  
6. Sélectionnez **Corps**.  Voici les résultats de la requête dans l’action Log Analytics.
6. Cliquez sur **Afficher les options avancées**.
7. Dans la zone **Is HTML** (Est HTML), sélectionnez **Oui**.<br><br>![Fenêtre de configuration d’e-mail Office 365](media/log-analytics-flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Étape 6 : Enregistrer et tester votre flux
1. Dans la zone **Nom du flux**, ajoutez un nom pour votre flux, puis cliquez sur **Créer un flux**.<br><br>![Enregistrer le flux](media/log-analytics-flow-tutorial/flow06.png)
2. Le flux est maintenant créé et s’exécute au bout d’un jour, ce qui correspond au délai que vous avez spécifié. 
3. Pour tester immédiatement le flux, cliquez sur **Exécuter maintenant**, puis sur **Exécuter le flux**.<br><br>![Exécuter le flux](media/log-analytics-flow-tutorial/flow07.png)
3. Lorsque l’exécution du flux est terminée, consultez les e-mails du destinataire que vous avez spécifié.  Il doit avoir reçu un e-mail dont le corps est semblable à ceci :<br><br>![Exemple de message électronique](media/log-analytics-flow-tutorial/flow08.png)


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [recherches dans les journaux avec Log Analytics](log-analytics-log-search-new.md).
- Découvrez [Microsoft Flow](https://ms.flow.microsoft.com).



