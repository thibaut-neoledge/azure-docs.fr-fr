---
title: "Configurer des alertes pour des requêtes dans Stream Analytics | Microsoft Docs"
description: "Présentation des alertes Stream Analytics"
keywords: configurer des alertes
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/26/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: a0c9c771c273728cf35d9cd727e1570e2a9bff5a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configuration d’alertes pour des tâches Azure Stream Analytics
## <a name="introduction-monitor-page"></a>Introduction : page de surveillance
Vous pouvez configurer des règles pour déclencher une alerte quand une mesure atteint une condition que vous spécifiez. Par exemple, vous pouvez configurer une alerte pour une condition comme suit :

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Les règles peuvent être configurées sur des mesures par le biais du portail ou [par programmation](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sur les données des journaux des opérations.

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurer des alertes dans le portail Azure
1. Dans le portail Azure, ouvrez le travail Stream Analytics pour lequel vous souhaitez créer une alerte. 

2. Dans le panneau **Travail**, cliquez sur la section **Surveillance**.  

3. Dans le panneau **Métrique**, cliquez sur la commande **Ajouter une alerte**.

      ![Configuration du portail Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Entrez un nom et une description.

5. Utilisez les sélecteurs pour définir la condition dans laquelle l’alerte est envoyée.

6. Indiquez des informations sur la destination de l’alerte.

      ![Configuration d’une alerte pour un travail Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Pour plus d’informations sur la configuration d’alertes dans le portail Azure, consultez [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


