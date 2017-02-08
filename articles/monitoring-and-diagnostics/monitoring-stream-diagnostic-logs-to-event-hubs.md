---
title: Stream Azure Diagnostic Logs to Event Hubs (Diffuser en continu les journaux de diagnostic vers Event Hubs) | Microsoft Docs
description: "Découvrez comment diffuser en continu les journaux de diagnostic vers Event Hubs."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: c9063fcc59d83cb2a6a159cf3a69234417138a5c
ms.openlocfilehash: 5cadc3ea77ba13d40876c7bc11d2aa1d6abe6b87


---
# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Diffuser en continu les journaux de diagnostic vers Event Hubs
Les **[journaux de diagnostic Azure](monitoring-overview-of-diagnostic-logs.md)** peuvent être diffusés quasiment en temps réel sur n’importe quelle application à l’aide de l’option « Exporter vers Event Hubs » intégrée au portail, ou en activant l’identifiant de règle Service Bus dans un paramètre de diagnostic via les applets de commande PowerShell Azure ou l’interface de ligne de commande Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Ce que vous pouvez faire avec les journaux de diagnostic et Event Hubs
Voici quelques façons d’utiliser la fonctionnalité de diffusion en continu pour les journaux de diagnostic :

* **Diffuser en continu des journaux sur des systèmes de journalisation et de télémétrie tiers** : au fil du temps, la diffusion en continu sur Event Hubs deviendra le mécanisme de diffusion de vos journaux de diagnostic vers les SIEM et les solutions d’analyse de journaux tiers.
* **Afficher l’état d’intégrité du service en diffusant des données de chemin réactif vers PowerBI** : en utilisant Event Hubs, Stream Analytics et PowerBI, vous pouvez facilement transformer vos données de diagnostic en informations en temps réel sur vos services Azure. [Cette documentation vous explique comment configurer un client Event Hubs, traiter les données avec Stream Analytics et utiliser PowerBI comme sortie](../stream-analytics/stream-analytics-power-bi-dashboard.md). Voici quelques conseils pour la configuration des journaux de diagnostic :
  
  * Un client Event Hubs est automatiquement créé pour une catégorie de journaux de diagnostic lorsque vous activez l’option dans le portail ou via PowerShell. Dans l’espace de noms Service Bus, sélectionnez le client Event Hubs avec le nom commençant par « insights- ».
  * Voici un exemple de requête Stream Analytics que vous pouvez utiliser pour analyser simplement toutes les données de journal dans une table PowerBI :

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

* **Créer une plateforme de journalisation et de télémétrie personnalisée** : si vous disposez déjà d’une plateforme de télémétrie personnalisée, ou si vous envisagez d’en créer une, la nature hautement évolutive de publication et d’abonnement d’Event Hubs vous permet d’intégrer avec souplesse les journaux de diagnostic. [Consultez ici le guide de Dan Rosanova sur l’utilisation d’Event Hubs dans une plateforme de télémétrie à échelle mondiale.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-diagnostic-logs"></a>Activer la diffusion en continu des journaux de diagnostic
Vous pouvez activer la diffusion en continu des journaux de diagnostic par programme, via le portail ou à l’aide de [l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx). Dans les deux cas, vous choisissez un espace de noms Service Bus et un client Event Hubs est créé dans l’espace de noms pour chaque catégorie de journal que vous activez. Une **catégorie de journal** de diagnostic est un type de journal qu’une ressource peut collecter. Vous pouvez sélectionner les catégories de journaux que vous souhaitez collecter pour une ressource particulière dans le portail Azure sous le panneau Diagnostics.

![Catégories de journaux disponibles dans le portail](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [!WARNING]
> L’activation et la diffusion en continu de journaux de diagnostic à partir de ressources de calcul (par exemple, les machines virtuelles ou Service Fabric) [nécessitent des étapes de configuration différentes](../event-hubs/event-hubs-streaming-azure-diags-data.md).
> 
> 

Il n’est pas nécessaire que l’espace de noms de Service Bus ou du hub d’événements se trouve dans le même abonnement que la ressource générant des journaux, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.

### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell
Pour activer la diffusion en continu via les [applets de commande Azure PowerShell](insights-powershell-samples.md), vous pouvez utiliser l’applet de commande `Set-AzureRmDiagnosticSetting` avec ces paramètres :

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

L’ID de règle Service Bus est une chaîne au format `{service bus resource ID}/authorizationrules/{key name}`, par exemple, `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.

### <a name="via-azure-cli"></a>Via l’interface de ligne de commande Azure
Pour activer la diffusion en continu via [l’interface de ligne de commande Azure](insights-cli-samples.md), vous pouvez utiliser la commande `insights diagnostic set` comme suit :

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Utilisez le même format pour l’ID de règle Service Bus, comme expliqué pour l’applet de commande PowerShell.

### <a name="via-azure-portal"></a>Via le portail Azure
Pour activer la diffusion en continu via le portail Azure, accédez aux paramètres de diagnostic d’une ressource et sélectionnez « Exporter vers Event Hub ».

![Exporter vers Event Hubs dans le portail](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Sélectionnez un espace de noms Service Bus pour le configurer. L’espace de noms sélectionné sera l’espace où le client Event Hubs sera créé (si c’est la première fois que vous diffusez en continu des journaux de diagnostic) ou vers lequel le client Event Hubs diffusera les journaux (si des ressources diffusent déjà cette catégorie de journal vers cet espace de noms). La stratégie définit les autorisations dont dispose le mécanisme de diffusion en continu. À l’heure actuelle, la diffusion vers les clients Event Hubs requiert des autorisations de gestion, d’envoi et d’écoute. Vous pouvez créer ou modifier les stratégies d’accès partagé de l’espace de noms Service Bus dans le portail Azure Classic sous l’onglet « Configurer » pour votre espace de noms Service Bus. Pour mettre à jour l’un de ces paramètres de diagnostic, le client doit avoir l’autorisation ListKey sur la règle d’autorisation Service Bus.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Comment utiliser les données de journal d’Event Hubs ?
Voici des exemples de données de sortie provenant d’Event Hubs :

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nom de l’élément | Description |
| --- | --- |
| records |Un tableau regroupant tous les événements de journal de cette charge utile. |
| time |L’heure à laquelle l’événement s’est produit. |
| category |La catégorie de journal associée à cet événement. |
| resourceId |L’ID de la ressource qui a généré cet événement. |
| operationName |Le nom de l’opération. |
| level |facultatif. Indique le niveau de l’événement de journal. |
| properties |Les propriétés de l’événement. |

Une liste de tous les fournisseurs de ressources qui prennent en charge la diffusion en continu vers Event Hubs est disponible [ici](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Diffusion de données à partir des ressources de calcul
Vous pouvez également transmettre en continu des journaux de diagnostic à partir des ressources de calcul à l’aide de l’agent Windows Azure Diagnostics. [Consultez cet article](../event-hubs/event-hubs-streaming-azure-diags-data.md) pour découvrir comment configurer cela.

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les journaux de diagnostic Azure](monitoring-overview-of-diagnostic-logs.md)
* [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)




<!--HONumber=Dec16_HO2-->


