---
title: "Réponses aux alertes dans OMS Log Analytics | Microsoft Docs"
description: "Les alertes dans Log Analytics identifient des informations importantes dans votre référentiel OMS et peuvent de façon proactive vous informer sur des problèmes ou appeler des actions pour tenter de les corriger.  Cet article décrit comment créer une règle d’alerte et détaille les différentes actions qu’elle peut engager."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fdf22ff85a3a76be5de50632c4948df44c2312df
ms.openlocfilehash: b8731e1fe48b7d809b113eb5273e3962542b8f34
ms.lasthandoff: 03/01/2017


---

# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Ajouter des actions à des règles d’alerte dans Log Analytics
Lorsqu’une [alerte est créée dans Log Analytics](log-analytics-alerts.md), vous avez la possibilité de [configurer la règle d’alerte](log-analytics-alerts.md) pour effectuer une ou plusieurs actions.  Cet article décrit les différentes actions possibles et explique la configuration associée à chacune.

| Action | Description |
|:--|:--|
| [E-mail](#email-actions) |    Envoyez un e-mail avec les détails de l’alerte à un ou plusieurs destinataires. |
| [Webhook](#webhook-actions) | Appelez un processus externe par le biais d’une simple requête HTTP POST. |
| [Runbook](#runbook-actions) | Démarrez un runbook dans Azure Automation. |


## <a name="email-actions"></a>Actions de messagerie
Les actions de messagerie envoient un e-mail avec les détails de l’alerte à un ou plusieurs destinataires.  Vous pouvez spécifier l’objet du message, mais son contenu répond à un format standard construit par Log Analytics.  Il inclut des informations récapitulatives telles que le nom de l’alerte, en plus des détails pouvant décrire jusqu’à dix enregistrements retournés par la recherche de journal.  En outre, il comporte un lien vers une recherche de journal dans Log Analytics qui retourne l’ensemble des enregistrements à partir de cette requête.   L’expéditeur du message est *Équipe Microsoft Operations Management Suite &lt;noreply@oms.microsoft.com&gt;*. 

Les propriétés requises par les actions de messagerie sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Objet |Objet de l’e-mail.  Vous ne pouvez pas modifier le corps du message. |
| Destinataires |Adresses de tous les destinataires de l’e-mail.  Si vous spécifiez plusieurs adresses, séparez-les par un point-virgule (;). |


## <a name="webhook-actions"></a>Actions de webhook

Les actions de webhook permettent d’appeler un processus externe par le biais d’une simple requête HTTP POST.  Le service appelé doit prendre en charge les webhooks et déterminer comment il doit utiliser toute charge utile qu’il reçoit.  Vous pouvez également appeler une API REST qui ne prend pas spécifiquement en charge les webhooks, à condition qu’elle comprenne le format de la requête.  Les exemples d’utilisation d’un webhook en réponse à une alerte envoient un message dans [Slack](http://slack.com) ou créent un incident dans [PagerDuty](http://pagerduty.com/).  Pour une description complète de la création d’une règle d’alerte avec un webhook pour appeler Slack, consultez [Webhooks dans les alertes Log Analytics](log-analytics-alerts-webhooks.md).

Les propriétés requises par les actions webhook sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| URL du webhook |URL du webhook. |
| Charge utile JSON personnalisée |Charge utile personnalisée à envoyer avec le webhook.  Voir les détails ci-dessous. |


Les webhooks incluent une URL et une charge utile au format JSON qui correspond aux données envoyées au service externe.  Par défaut, la charge utile comprend les valeurs du tableau suivant.  Vous pouvez choisir de remplacer cette charge utile par une charge utile personnalisée de votre choix.  Dans ce cas, vous pouvez reprendre les variables de chacun des paramètres indiquées dans le tableau pour inclure les valeurs correspondantes dans votre charge utile personnalisée.

| Paramètre | Variable | Description |
|:--- |:--- |:--- |
| AlertRuleName |#AlertRuleName |Nom de la règle d’alerte. |
| AlertThresholdOperator |#thresholdoperator |Opérateur de seuil de la règle d’alerte.  *Supérieur à* ou *Inférieur à*. |
| AlertThresholdValue |#thresholdvalue |Valeur de seuil de la règle d’alerte. |
| LinkToSearchResults |#LinkToSearchResults |Lien vers la recherche de journal Log Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| ResultCount |#searchresultcount |Nombre d’enregistrements dans les résultats de recherche. |
| SearchIntervalEndtimeUtc |#SearchIntervalEndtimeUtc |Heure de fin de la requête au format UTC. |
| SearchIntervalInSeconds |#searchinterval |Fenêtre de temps de la règle d’alerte. |
| SearchIntervalStartTimeUtc |#SearchIntervalStartTimeUtc |Heure de début de la requête au format UTC. |
| SearchQuery |#SearchQuery |Requête de recherche de journal utilisée par la règle d’alerte. |
| SearchResults |Voir ci-dessous |Enregistrements retournés par la requête au format JSON.  Le jeu de résultats est limité aux 5 000 premiers enregistrements. |
| WorkspaceID |#WorkspaceID |ID de votre espace de travail OMS. |

Par exemple, vous pouvez spécifier la charge utile personnalisée suivante qui inclut un paramètre unique appelé *text*.  Le service appelé par ce webhook s’attendrait à recevoir ce paramètre.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Cette charge utile peut donner ce qui suit quand elle est envoyée au webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Pour inclure les résultats de la recherche dans une charge utile personnalisée, ajoutez la ligne suivante en tant que propriété de niveau supérieur dans la charge utile json.  

    "IncludeSearchResults":true

Par exemple, pour créer une charge utile personnalisée qui inclut uniquement le nom de l’alerte et les résultats de recherche, vous pouvez utiliser ce qui suit. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Pour étudier un exemple complet de création d’une règle d’alerte avec un webhook pour démarrer un service externe, consultez [Créer une action d’alerte webhook dans OMS Log Analytics pour envoyer un message à Slack](log-analytics-alerts-webhooks.md).

## <a name="runbook-actions"></a>Actions de runbook
Les actions de runbook démarrent un runbook dans Azure Automation.  Ce type d’action suppose que la [solution Automation](log-analytics-add-solutions.md) est installée et configurée dans votre espace de travail OMS.  Vous pouvez effectuer votre choix parmi les runbooks disponibles dans le compte Automation que vous avez configuré dans la solution Automation.

Les propriétés requises par les actions runbook sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:---|
| Runbook | Runbook que vous souhaitez démarrer lorsqu’une alerte est créée. |
| Exécuter sur | Spécifiez **Azure** pour exécuter le runbook dans le cloud.  Spécifiez **Worker hybride** pour exécuter le runbook sur un agent sur lequel [Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md ) est installé.  |

Les actions de runbook démarrent le runbook en utilisant un [webhook](../automation/automation-webhooks.md).  Quand vous créez la règle d’alerte, elle crée automatiquement un webhook pour le runbook sous le nom **Correction d’alerte OMS** suivi d’un GUID.  

Vous ne pouvez pas compléter directement tous les paramètres du runbook, mais le [paramètre $WebhookData](../automation/automation-webhooks.md) inclut les détails de l’alerte, dont les résultats de la recherche de journal qui l’a créée.  Le runbook doit définir **$WebhookData** en tant que paramètre pour pouvoir accéder aux propriétés de l’alerte.  Les données d’alerte sont disponibles au format json dans une propriété unique appelée **SearchResults** dans la propriété **RequestBody** du **$WebhookData**.  Les propriétés sont répertoriées dans le tableau suivant.

| Nœud | Description |
|:--- |:--- |
| id |Chemin d’accès et GUID de la recherche. |
| __metadata |Informations sur l’alerte, dont le nombre d’enregistrements et l’état des résultats de recherche. |
| value |Entrée distincte pour chaque enregistrement dans les résultats de recherche.  Les détails de l’entrée correspondent aux propriétés et aux valeurs de l’enregistrement. |

Par exemple, le runbook suivant extrait les enregistrements retournés par la recherche de journal et attribue des propriétés différentes selon le type de chaque enregistrement.  Notez que le runbook commence par convertir **RequestBody** à partir de json, de sorte qu’il est utilisable en tant qu’objet dans PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="next-steps"></a>Étapes suivantes
- Effectuez une procédure pas à pas pour [configurer un webhook](log-analytics-alerts-webhooks.md) avec une règle d’alerte.  
- Apprenez à écrire des [runbooks dans Azure Automation](https://azure.microsoft.com/documentation/services/automation) pour corriger les problèmes identifiés par des alertes.
