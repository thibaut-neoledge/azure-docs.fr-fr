---
title: "Tableau de bord Power BI sur Azure Stream Analytics | Microsoft Docs"
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
ms.date: 03/28/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 278f73d9ee3f64727b34b3674cd38eaf5301e675
ms.contentlocale: fr-fr
ms.lasthandoff: 05/01/2017


---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics et Power BI : tableau de bord d’analyse en temps réel pour les données de streaming
Azure Stream Analytics vous permet de tirer parti de Microsoft Power BI, l’un des principaux outils d’analyse décisionnelle. Découvrez comment utiliser Azure Stream Analytics pour analyser des volumes élevés de données de streaming et obtenir les résultats dans un tableau de bord d’analyse Power BI en temps réel.

Utilisez [Microsoft Power BI](https://powerbi.com/) pour générer rapidement un tableau de bord dynamique. [Regardez une vidéo illustrant ce scénario](https://www.youtube.com/watch?v=SGUpT-a99MA).

Dans cet article, vous allez découvrir comment créer vos propres outils d’analyse décisionnelle en utilisant Power BI comme sortie pour vos travaux Azure Stream Analytics. Vous allez également découvrir comment utiliser un tableau de bord en temps réel.

## <a name="prerequisites"></a>Composants requis
* Compte Microsoft Azure.
* Compte professionnel ou scolaire pour Power BI.
* Suivi du scénario [Détection des fraudes en temps réel](stream-analytics-real-time-fraud-detection.md). L’article que vous lisez s’appuie maintenant sur le flux de travail qui est décrit dans l’article [Prise en main de l’utilisation d’Azure Stream Analytics : détection des fraudes en temps réel](stream-analytics-real-time-fraud-detection.md) et ajoute une sortie du jeu de données de streaming de Power BI.

## <a name="add-power-bi-output"></a>Ajouter une sortie Power BI
Maintenant qu’il existe une entrée pour le travail, une sortie Power BI peut être définie.

1. Sélectionnez la zone **Sorties** au milieu du tableau de bord du travail, puis **+ Ajouter** pour créer votre sortie.

    ![Ajouter une sortie](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

2. Indiquez l’**Alias de sortie**. Vous pouvez utiliser n’importe quel alias de sortie auquel il vous sera facile de vous référer. Cet alias de sortie est utile si vous décidez d’avoir plusieurs sorties pour votre travail. Dans ce cas, référez-vous à cette sortie dans votre requête. Utilisons par exemple la valeur d’alias de sortie « StreamAnalyticsRealTimeFraudPBI ».

3. Sélectionnez **Autoriser**.

    ![Ajouter une autorisation](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

4. Une fenêtre s’ouvre dans laquelle vous pouvez indiquer vos informations d’identification Azure (pour un compte professionnel ou scolaire). Elle contient également votre travail Azure avec un accès à votre zone Power BI.

    ![Autoriser les champs](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

5. L’autorisation disparaît une fois que vous avez indiqué les informations nécessaires. La zone **Nouvelle sortie** contient les champs **Nom du jeu de données** et **Nom de la table**.

    ![Espace de travail PBI](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

6. Définissez-les comme suit :
    * **Espace de travail de groupe** : sélectionnez un espace de travail dans votre client Power BI sous lequel créer le jeu de données.
    * **Nom du jeu de données** : fournissez le nom de jeu de données que vous souhaitez donner à votre sortie Power BI, par exemple « StreamAnalyticsRealTimeFraudPBI ».
    * **Nom de la table** : indiquez un nom de table sous le jeu de données de la sortie Power BI, par exemple « StreamAnalyticsRealTimeFraudPBI ». Pour le moment, la sortie Power BI des travaux Stream Analytics ne peut contenir qu’une table dans un jeu de données.

7. Sélectionnez **Créer**. La configuration de votre sortie est à présent terminée.

> [!WARNING]
> Si Power BI dispose déjà d’un jeu de données et d’une table portant le même nom que ceux de ce travail Stream Analytics, les données existantes sont alors écrasées.
> Nous vous recommandons donc de ne pas créer explicitement ce jeu de données et cette table dans votre compte Power BI. Ceux-ci sont automatiquement créés au démarrage du travail Stream Analytics et lorsque celui-ci se met à injecter des sorties dans Power BI. Si la requête de travail ne retourne aucun résultat, le jeu de données et la table ne sont pas créés.
>
>

Le jeu de données est créé avec les paramètres suivants :
* **defaultRetentionPolicy : BasicFIFO** : données FIFO avec un maximum de 200 000 lignes.
* **defaultMode : pushStreaming** : prend en charge les vignettes de streaming et les éléments visuels basés sur des rapports standard (ou push).

Pour le moment, vous ne pouvez pas créer de jeux de données avec d’autres indicateurs.

Pour plus d’informations sur les jeux de données Power BI, consultez la référence [API REST Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-query"></a>Écrire une requête
Accédez à l’onglet **Requête** de votre travail. Écrivez la requête dont vous souhaitez obtenir la sortie dans Power BI. Par exemple, elle peut être semblable à la requête SQL suivante pour intercepter les fraudes SIM dans le secteur des télécommunications :


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime

/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between one and five seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Créer le tableau de bord dans Power BI

1. Accédez à [Powerbi.com](https://powerbi.com), puis connectez-vous à l’aide de votre compte professionnel ou scolaire. Si la requête du travail Stream Analytics génère des résultats, vous voyez que votre jeu de données est déjà créé, comme indiqué dans l’illustration suivante :

    ![Jeu de données de streaming](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. Sélectionnez successivement **Ajouter une vignette** et vos données de streaming personnalisées.

    ![Jeu de données de streaming personnalisé](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

3. Sélectionnez votre jeu de données dans la liste.

    ![Votre jeu de données de streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

4. Créez une visualisation sous forme de carte, puis sélectionnez le champ **fraudulentcalls**.

    ![Ajouter une fraude](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

    Nous avons à présent un compteur de fraudes !

    ![Compteur de fraudes](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

5. Refaites l’exercice consistant à ajouter une vignette, mais en sélectionnant cette fois le graphique en courbes. Ajoutez **fraudulentcalls** comme valeur et **windowend** comme axe. Nous avons sélectionné les dix dernières minutes, comme illustré dans la capture d’écran suivante :

![Appels frauduleux](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


Ce didacticiel vous montre comment créer un seul type de graphique pour un jeu de données. Power BI peut vous aider à créer d’autres outils d’analyse décisionnelle clients pour votre organisation. Pour un autre exemple de tableau de bord Power BI, visionnez la vidéo de [prise en main de Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Pour plus d’informations sur la configuration d’une sortie Power BI et sur l’utilisation des groupes Power BI, passez en revue la [section Power BI](stream-analytics-define-outputs.md#power-bi) dans [Présentation des sorties Stream Analytics](stream-analytics-define-outputs.md "Présentation des sorties Stream Analytics"). L’article [Tableaux de bord dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) constitue une autre ressource utile.

## <a name="learn-about-limitations-and-best-practices"></a>En savoir plus sur les limites et les meilleures pratiques
Power BI utilise des contraintes d’accès concurrentiel et de débit comme décrit [dans cette page sur Power BI](https://powerbi.microsoft.com/pricing "Tarification de Power BI").

Actuellement, Power BI peut être appelé environ une fois par seconde. Les éléments visuels de streaming prennent en charge des paquets de 15 Ko. Au-delà de cette taille, ils échouent (mais les transmissions de type push continuent de fonctionner).

En raison de cette limite, Power BI s’applique naturellement dans les cas où Azure Stream Analytics débouche sur une réduction significative de la charge des données.
Nous vous recommandons d’utiliser la fenêtre bascule ou la fenêtre récurrente pour vous assurer que la transmission de type push des données est effectuée au plus une fois par seconde et que votre requête respecte les exigences de débit.

Vous pouvez utiliser l’équation suivante pour calculer la valeur à donner à votre fenêtre en secondes :

![Equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Par exemple :
- Vous disposez de 1 000 appareils qui envoient des données à des intervalles d’une seconde.
- Vous utilisez la référence Power BI Pro qui prend en charge 1 000 000 lignes par heure.
- Vous souhaitez publier la quantité moyenne de données par appareil sur Power BI.

L’équation est donc la suivante :

![Equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Cela signifie que nous pouvons modifier la requête d’origine et la remplacer par ce qui suit :

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
Si le mot de passe a été modifié depuis la création ou la dernière authentification de votre travail, vous devez réauthentifier votre compte Power BI. Si Azure Multi-Factor Authentication est configuré sur votre client Azure Active Directory (Azure AD), vous devez également renouveler l’autorisation Power BI toutes les deux semaines. Si vous ne la renouvelez pas, vous pouvez observer des symptômes comme un manque de sortie du travail ou une « erreur d’authentification d’utilisateur » dans les journaux des opérations.

De même, si un travail tente de démarrer après l’expiration du jeton, une erreur se produit et le démarrage du travail échoue. Pour résoudre ce problème, arrêtez le travail en cours d’exécution et accédez à votre sortie Power BI. Pour éviter toute perte de données, sélectionnez le lien **Renouveler l’autorisation**, puis redémarrez votre travail depuis l’**heure du dernier arrêt**.

Une fois que l’autorisation a été actualisée avec Power BI, une alerte verte apparaît dans la zone d’autorisation pour indiquer que le problème a été résolu.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

