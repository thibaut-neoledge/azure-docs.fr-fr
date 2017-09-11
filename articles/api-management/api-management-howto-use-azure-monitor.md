---
title: "Gestion des API avec Azure Monitor | Microsoft Docs"
description: "Découvrez comment surveiller votre service de Gestion des API Azure avec Azure Monitor."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: 717e033aa4bbd4dd8ebcc727c3f551aee81770dc
ms.contentlocale: fr-fr
ms.lasthandoff: 08/31/2017

---
# <a name="monitor-api-management-with-azure-monitor"></a>Gestion des API avec Azure Monitor
Azure Monitor est un nouveau service Azure qui fournit une source unique d’analyse pour toutes vos ressources Azure. Avec Azure Monitor, vous pouvez visualiser, interroger, acheminer, archiver et agir sur les mesures et journaux provenant de vos ressources Azure, comme la gestion des API. 

La vidéo suivante montre comment surveiller la gestion des API à l’aide d’Azure Monitor. Pour en savoir plus sur Azure Monitor, consultez [Prise en main d’Azure Monitor]. 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>Mesures
La gestion des API émet actuellement cinq mesures et nous prévoyons d’en ajouter plus à l’avenir. Ces mesures sont émises chaque minute, pour une visibilité en quasi temps réel de l’intégrité de vos API. Voici un résumé des mesures :
* Nombre total de demandes de passerelle : le nombre de requêtes d’API dans la période. 
* Demandes de la passerelle ayant abouti : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP de succès, dont 304, 307 et toute valeur inférieure à 301 (par exemple, 200). 
* Demandes de la passerelle ayant échoué : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP d’échec, dont 400 et toute valeur supérieure à 500.
* Demandes de la passerelle non autorisées : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP comprenant 401, 403 et 429. 
* Autres demandes de la passerelle : le nombre de requêtes d’API ayant reçu des codes de réponse qui n’appartiennent à aucune des catégories précédentes (par exemple, 418).

Vous pouvez accéder aux mesures dans votre service de gestion des API, ou accéder aux mesures de toutes vos ressources Azure dans Azure Monitor. Pour voir les mesures dans votre service de Gestion des API :
1. Ouvrez le portail Azure.
2. Allez à votre service de Gestion des API.
3. Cliquez sur **Mesures**.

![Panneau Mesures][metrics-blade]

Pour plus d’informations sur l’utilisation des mesures, consultez la [Présentation des mesures].

## <a name="activity-logs"></a>Journaux d’activité
Les journaux d’activité fournissent des informations sur les opérations qui ont été effectuées sur les services de Gestion des API. Ils étaient auparavant nommés « Journaux d’audit » ou « Journaux des opérations ». Avec les journaux d’activité, vous pouvez déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur vos services de Gestion des API. 

> [!NOTE]
> Les journaux d’activité n’incluent pas les opérations de lecture (GET) ou les opérations effectuées dans le portail des éditeurs classique ou utilisant les API de gestion d’origine.

Vous pouvez accéder aux journaux d’activité dans votre service de Gestion des API, ou accéder aux journaux de toutes vos ressources Azure dans Azure Monitor. Pour afficher les journaux d’activité dans votre service de Gestion des API :
1. Ouvrez le portail Azure.
2. Allez à votre service de Gestion des API.
3. Cliquez sur **Journal d’activité**.

![Panneau Journaux d’activité][activity-logs-blade]

Pour plus d’informations sur l’utilisation des mesures, consultez la [Présentation des journaux d’activité].

## <a name="alerts"></a>Alertes
Vous pouvez configurer les paramètres pour recevoir des alertes en fonction des mesures et des journaux d’activité. Azure Monitor vous permet de configurer une alerte pour effectuer les opérations suivantes lors de son déclenchement :

* Envoyer un e-mail de notification
* Appeler un webhook
* Appeler une application logique Azure

Vous pouvez configurer des règles d’alerte dans votre service de Gestion des API, ou dans Azure Monitor. Pour les configurer dans la Gestion des API : 
1. Ouvrez le portail Azure.
2. Allez à votre service de Gestion des API.
3. Cliquez sur **Règles d'alerte**.

![Panneau Règles d’alerte][alert-rules-blade]

Pour plus d’informations sur l’utilisation des alertes, consultez [Présentation des alertes].

## <a name="diagnostic-logs"></a>Journaux de diagnostic
Les journaux de diagnostic offrent des informations détaillées sur les opérations et erreurs qui sont importantes pour l’audit ainsi qu’à des fins de dépannage. Les journaux de diagnostic diffèrent des journaux d’activité. Le journal d’activité fournit des informations sur les opérations qui ont été effectuées sur vos ressources Azure. Les journaux de diagnostic fournissent des informations sur les opérations effectuées par votre ressource.

La Gestion des API fournit actuellement des journaux de diagnostic (par lot toutes les heures) concernant chaque requête d’API. Chaque entrée a la structure suivante :

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

Vous pouvez accéder aux journaux de diagnostic dans votre service de Gestion des API, ou accéder aux journaux de toutes vos ressources Azure dans Azure Monitor. Pour afficher les journaux de diagnostic dans votre service de Gestion des API :
1. Ouvrez le portail Azure.
2. Allez à votre service de Gestion des API.
3. Cliquez sur **Journal de diagnostic**.

![Panneau Journaux de diagnostic][diagnostic-logs-blade]

Pour plus d’informations sur l’utilisation des mesures, consultez la [Présentation des journaux de diagnostic].

## <a name="next-step"></a>Étape suivante

* [Prise en main d’Azure Monitor]
* [Présentation des mesures]
* [Présentation des journaux d’activité]
* [Présentation des journaux de diagnostic]
* [Présentation des alertes]

[Prise en main d’Azure Monitor]: ../monitoring-and-diagnostics/monitoring-get-started.md
[Présentation des mesures]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[Présentation des journaux d’activité]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[Présentation des journaux de diagnostic]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[Présentation des alertes]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png

