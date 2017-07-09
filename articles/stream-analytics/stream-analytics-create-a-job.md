---
title: "Guide pratique pour créer un travail de traitement d’analyse de données pour Stream Analytics | Microsoft Docs"
description: "Créer une tâche de traitement d’analyse de données pour Stream Analytics | segment du parcours d’apprentissage."
keywords: "traitement d’analyse de données"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 2e989ab0cb95a1caef44b2f235b162f2c12a79cc
ms.contentlocale: fr-fr
ms.lasthandoff: 07/04/2017


---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Comment créer une tâche de traitement d’analyse de données pour Stream Analytics
La ressource de niveau supérieur dans Azure Stream Analytics est une tâche Stream Analytics.  Elle se compose d'une ou plusieurs sources de données d'entrée, une requête exprimant la transformation de données et une ou plusieurs cibles de sortie où les résultats sont écrits. Ensemble, ces éléments permettent à l’utilisateur de traiter l’analyse des données dans différents scénarios de données de diffusion en continu.

Pour utiliser Stream Analytics, commencez par créer une tâche Stream Analytics.  Notez que cette action n'a aucune incidence de facturation tant que la tâche n'a pas démarré.

1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com) en ligne ou au [portail Azure](https://portal.azure.com/).
2. Dans le portail : cliquez successivement sur **Nouveau**, sur **Data Services** ou **Data Analytics** (selon votre portail), sur **Azure Stream Analytics** ou **Stream Analytics**, puis sur **Création rapide**.
   
   ![Assistant Tâche de traitement d’analyse de données](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  
   
   ![Créer une tâche de traitement d’analyse de données](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  
3. Spécifiez la configuration souhaitée pour la tâche Stream Analytics.
   
   * Dans la zone **Nom de la tâche** , entrez un nom pour identifier la tâche Stream Analytics. Une fois le **nom de la tâche** validé, une coche verte s’affiche dans la zone Nom de la tâche. Le **Nom de la tâche** ne peut contenir que des caractères alphanumériques et le caractère « - », et doit compter entre 3 et 63 caractères.
   * Utilisez **Région** dans le portail Azure ou **Emplacement** dans le portail Azure pour spécifier l’emplacement géographique où vous souhaitez exécuter le travail.
   * Si vous utilisez le portail Azure, sélectionnez ou créez un compte de stockage à utiliser comme **Compte de stockage de surveillance régionale**. Ce compte de stockage est utilisé pour stocker les données de surveillance de toutes les tâches Stream Analytics en cours d'exécution dans cette région.
   * Si vous utilisez le portail Azure, indiquez un **groupe de ressources** nouveau ou existant contenant les ressources associées à votre application.
4. Une fois les options de la nouvelle tâche Stream Analytics configurées, cliquez sur **Créer une tâche Stream Analytics**. La création de la tâche Stream Analytics peut prendre plusieurs minutes. Pour vérifier l'état, vous pouvez suivre l'avancement dans le hub de notifications.
   
   ![Hub de notification des tâches de traitement d’analyse de données](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  
   
   ![Créer une tâche de traitement d’analyse de données dans le portail Azure](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Le nouveau travail est affiché avec l’état **Créé**. Notez que le bouton **Démarrer** est désactivé. Avant de pouvoir démarrer la tâche, vous devez configurer son entrée, sa requête et sa sortie.
   
   ![Statut de la tâche de traitement d’analyse de données](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  
   
   ![Statut de la tâche de traitement d’analyse de données dans le portail Azure](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


