<properties 
   pageTitle="Tableau des limites Stream Analytics"
   description="Décrit les limites du système et les tailles recommandées pour les connexions et les composants Stream Analytics."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="10/22/2015"
   ms.author="jeffstok" />

| Identificateur de la limite | Limite | Commentaires |
|----------------- | ------------|--------- |
| Nombre maximal d’unités de diffusion en continu par abonnement par région | 50 | Une demande d’augmentation des unités de diffusion en continu pour votre abonnement au-delà de 50 peut s’effectuer en contactant le [Support technique Microsoft](https://support.microsoft.com/fr-FR). |
| Débit maximal d’une unité de diffusion en continu | 1 Mo/s* | Le débit maximal par unité de diffusion dépend du scénario. Le débit réel peut être inférieur et dépend du partitionnement et de la complexité de la requête. Pour plus d’informations, vois l’article [Mise à l’échelle des travaux Azure Stream Analytics pour augmenter le débit](../articles/stream-analytics/stream-analytics-scale-jobs.md). |
| Limitation de requêtes d’instruction SELECT | 5 sorties par requête | Cette limite peut être augmentée par la suite. |

<!---HONumber=Nov15_HO1-->