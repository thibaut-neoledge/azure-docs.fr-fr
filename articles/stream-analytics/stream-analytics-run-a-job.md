---
title: "Démarrer des travaux de streaming dans Stream Analytics | Microsoft Docs"
description: "Comment exécuter une tâche de diffusion en continu dans Azure Stream Analytics | segment du parcours d’apprentissage."
keywords: "diffusion en continu de tâches"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9d46950f-2b69-49ce-a567-df558c5dd820
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 903f5bc3ea4bfd4087fecb030703d31fc42582ae
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Comment exécuter une tâche de diffusion en continu dans Azure Stream Analytics
Lorsque l'entrée, la requête et la sortie d'une tâche ont été spécifiées, vous pouvez démarrer la tâche Stream Analytics.

Pour démarrer la tâche :

1. Dans le portail Azure Classic, dans le tableau de bord du travail, cliquez sur **Démarrer** au bas de la page.
   
   ![Bouton Démarrer la tâche](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  
   
   Dans le portail Azure, cliquez sur **Démarrer** en haut de la page de votre travail.
   
   ![Bouton Démarrer le travail du portail Azure](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  
2. Spécifiez une valeur **Démarrer la sortie** pour déterminer quand cette tâche commence à générer une sortie. Le paramètre par défaut pour les tâches qui n’ont jamais été démarrées est **Heure de démarrage de la tâche**, ce qui signifie que la tâche commence immédiatement à traiter les données. Vous pouvez également spécifier une heure **personnalisée** dans le passé (pour l’utilisation des données historiques) ou dans le futur (pour retarder le traitement jusqu’à une date ultérieure). Dans les cas où une tâche a déjà été démarrée, puis arrêtée, l’option **Heure du dernier arrêt** est disponible pour reprendre la tâche à partir de la dernière heure de sortie et éviter toute perte de données.  
   
   ![Heure de démarrage de la tâche de diffusion en continu](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  
   
   ![Heure de démarrage du travail de streaming dans le portail Azure](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  
3. Confirmez votre sélection. L’état du travail passe à *Démarrage*, puis à *En cours d’exécution* une fois le travail démarré. Vous pouvez surveiller la progression de l’opération de **démarrage** dans le **Hub de notification** :
   
   ![progression de la tâche de diffusion en continu](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  
   
   ![Progression du travail de streaming dans le portail Azure](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


