---
title: "Tableau de bord Power BI sur Stream Analytics | Microsoft Docs"
description: "Utilisez un tableau de bord Power BI de diffusion en temps réel pour collecter des informations d’analyse décisionnelle et analyser les données volumineuses à partir d’un travail Stream Analytics."
keywords: "tableau de bord d’analyse, tableau de bord en temps réel"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: c60d620d9fc91cfef2340b645e15ed19d3d1f4e1
ms.openlocfilehash: 39b2852c39e8d3eb4687cbcfd9116e1d66fc9d9d


---
# <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics et Power BI : tableau de bord d'analyse en temps réel pour les données de streaming
Azure Stream Analytics vous permet de tirer parti de Microsoft Power BI, l’un des principaux outils d’analyse décisionnelle. Découvrez comment utiliser Azure Stream Analytics pour analyser les volumes élevés de données de streaming et obtenir les résultats dans un tableau de bord d’analyse Power BI en temps réel.

Utilisez [Microsoft Power BI](https://powerbi.com/) pour générer rapidement un tableau de bord dynamique. [Visionnez une vidéo illustrant le scénario](https://www.youtube.com/watch?v=SGUpT-a99MA).

Dans cet article, découvrez comment créer vos propres outils d’analyse décisionnelle en utilisant Power BI comme sortie pour vos tâches Azure Stream Analytics comment utiliser un tableau de bord en temps réel.

## <a name="prerequisites"></a>Composants requis
* Compte Microsoft Azure.
* Compte professionnel ou scolaire pour Power BI
* Terminer le scénario [Détection des fraudes en temps réel](stream-analytics-real-time-fraud-detection.md). Cet article s’appuie sur ce flux de travail et ajoute une sortie de jeu de données de streaming Power BI.

## <a name="add-power-bi-output"></a>Ajouter une sortie Power BI
Maintenant qu’il existe une entrée pour le travail, une sortie Power BI peut être définie. Sélectionnez la zone **Sorties** au milieu du tableau de bord du travail. Cliquez ensuite sur le bouton **+ Ajouter** et créez votre sortie.

![ajouter une sortie](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

Indiquez l’**Alias de sortie**. Vous pouvez entrer n’importe quel alias de sortie auquel vous pourrez facilement vous référer. Cet alias de sortie est particulièrement utile si vous décidez d’avoir des sorties multiples pour votre travail. Dans ce cas, vous devez faire référence à cette sortie dans votre requête. Utilisons par exemple la valeur d’alias de sortie « StreamAnalyticsRealTimeFraudPBI ».

Cliquez ensuite sur le bouton **Autoriser**.

![ajouter une autorisation](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

Une fenêtre s’ouvre pour vous demander d’entrer vos informations d’identification Azure (compte professionnel ou scolaire). Votre travail Azure peut alors accéder à votre zone Power BI.

![autoriser les champs](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

La fenêtre d’autorisation disparaît une fois les informations correctes renseignées. La nouvelle zone de sortie comprend des champs pour le nom du jeu de données et le nom de la table.

![espace de travail PBI](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

Définissez-les comme suit :
* **Espace de travail de groupe** - Sélectionnez un espace de travail dans votre client Power BI sous lequel le jeu de données sera créé.
* **Nom du jeu de données** - Fournissez un nom de jeu de données que vous souhaitez donner à votre sortie de Power BI. Utilisons par exemple « StreamAnalyticsRealTimeFraudPBI ».
* **Nom de la table** - Fournissez un nom de table dans le jeu de données de la sortie de Power BI. Nommons-le par exemple « StreamAnalyticsRealTimeFraudPBI ». Actuellement, la sortie Power BI des travaux Stream Analytics peut avoir seulement une table dans un jeu de données.

Cliquez sur **Créer**. Votre configuration de sortie est terminée.

> [!WARNING]
> Notez que si Power BI dispose déjà d’un groupe de données et d’un tableau portant le même nom que celui que vous avez entré dans le travail Stream Analytics, alors ces données seront écrasées.
> Par ailleurs, vous ne devez pas créer explicitement ce jeu de données et la table dans votre compte Power BI. Ceux-ci sont automatiquement créés au démarrage du travail Stream Analytics et lorsque celui-ci se met à extraire des sorties dans Power BI. Si la requête de travail ne renvoie aucun résultat, le jeu de données et la table ne sont pas créés.
> 
> 

Le jeu de données est créé avec le jeu de paramètres suivants :
* defaultRetentionPolicy : BasicFIFO - données FIFO, 200 000 lignes maximum
* defaultMode : pushStreaming : prend en charge les vignettes de streaming et les éléments visuels basés sur des rapports traditionnels (ou push)
* la création de jeux de données avec d’autres indicateurs n’est pas prise en charge pour l’instant

Pour plus d’informations sur les jeux de données Power BI, consultez la référence [API REST Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-query"></a>Écrire une requête
Accédez à l’onglet **Requête** de votre travail. Écrivez la requête dont vous souhaitez obtenir la sortie dans votre Power BI. Par exemple, il peut s’agit d’une requête SQL telle que la suivante pour intercepter les fraudes SIM dans le secteur des télécommunications :


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (e.g. Australia and Europe) within 5 seconds) */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime
   
/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between 1 and 5 seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Créer le tableau de bord dans Power BI

Accédez à [Powerbi.com](https://powerbi.com) , puis connectez-vous à l’aide de votre compte professionnel ou scolaire. Si la requête du travail Stream Analytics génère des résultats, vous voyez que votre jeu de données est déjà créé :

![jeu de données de streaming](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

Cliquez sur Ajouter une vignette et sélectionnez vos données de streaming personnalisées.

![jeu de données de streaming personnalisé](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

Sélectionnez ensuite votre jeu de données dans la liste :

![votre jeu de données de streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

À présent, créez une carte de visualisation et sélectionnez le champ fraudulentcalls.

![ajouter une fraude](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

Nous avons à présent un compteur de fraudes !

![compteur de fraudes](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

Refaites l’exercice consistant à ajouter une vignette, mais en sélectionnant cette fois un graphique en courbes. Ajoutez fraudulentcalls comme valeur et axis comme windowend. J’ai sélectionné les 10 dernières minutes :

![appels frauduleux](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


Notez que ce didacticiel ne vous a montré la création que d’un seul type de graphique pour un jeu de données. Power BI peut vous aider à créer d’autres outils d’analyse décisionnelle clients pour votre organisation. Pour un autre exemple de tableau de bord Power BI, visionnez la vidéo de [prise en main de Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Pour plus d’informations sur la configuration d’une sortie Power BI et pour utiliser des groupes Power BI, passez en revue la [section Power BI](stream-analytics-define-outputs.md#power-bi) dans [Présentation des sorties Stream Analytics](stream-analytics-define-outputs.md "Présentation des sorties Stream Analytics"). Vous pouvez également consulter la page [Tableaux de bord dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)pour en savoir plus sur la création des tableaux de bord avec Power BI.

## <a name="limitations-and-best-practices"></a>Limites et meilleures pratiques
Power BI utilise des contraintes d'accès concurrentiel et de débit comme indiqué ici : [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Tarification de Power BI")

Actuellement, votre Power BI peut être appelé environ une fois par seconde. Les éléments visuels de streaming prennent en charge les paquets de 15 Ko. Au-delà de cette taille, les éléments visuels de streaming échouent (mais push continue de fonctionner).

C’est pour cette raison que Power BI s’applique naturellement dans les cas où l’analyse de flux de données Microsoft Azure débouche sur une réduction significative de la charge des données.
Nous vous recommandons d’utiliser TumblingWindow ou HoppingWindow pour garantir que le push de données est au plus de 1 push/seconde et que votre requête respecte les exigences en matière de débit. Vous pouvez utiliser l’équation suivante pour calculer la valeur à donner à votre fenêtre en secondes :

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Par exemple, si 1 000 appareils envoient des données chaque seconde, il s’agit de la référence Pro Power BI qui prend en charge 1 000 000 lignes/heure et si vous souhaitez obtenir la moyenne des données par appareil sur Power BI, vous pouvez exécuter un push toutes les 4 secondes par appareil (comme indiqué ci-dessous) :  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Cela signifie que nous pouvons modifier la requête d’origine :

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Renouveler une autorisation
Vous devez authentifier de nouveau votre compte Power BI si son mot de passe a été modifié depuis la création ou la dernière authentification de votre travail. Si Multi-Factor Authentication (MFA) est configuré sur votre client Azure Active Directory (AAD), vous devrez également renouveler l’autorisation Power BI toutes les 2 semaines. Un symptôme de ce problème est l’absence de sortie de la travail et une « erreur d’authentification de l’utilisateur » dans les journaux des opérations.

De même, si une tâche tente de démarrer alors que le jeton a expiré, une erreur se produit et le démarrage de la tâche échoue. Pour résoudre ce problème, arrêtez votre tâche en cours d'exécution et accédez à votre sortie Power BI.  Cliquez sur le lien « Renouveler l’autorisation », puis redémarrez votre tâche depuis l’heure du dernier arrêt pour éviter de perdre des données. Une fois que l’autorisation est actualisée avec Power BI, une alerte verte apparaît dans la zone d’autorisation pour indiquer que le problème est résolu.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


