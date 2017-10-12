---
title: "Utilisation de l’API REST d’alerte Log Analytics OMS"
description: "L’API REST d’alerte Log Analytics vous permet de créer et de gérer des alertes dans Log Analytics, qui fait partie d’Operations Management Suite (OMS).  Cet article fournit des détails sur l’API et plusieurs exemples pour effectuer différentes opérations."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ce72ffef4394bf3bbe39fa420c4fcaa965ae35c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Créer et gérer des règles d’alerte dans Log Analytics avec l’API REST
L’API REST d’alerte Log Analytics vous permet de créer et de gérer des alertes dans Operations Management Suite (OMS).  Cet article fournit des détails sur l’API et plusieurs exemples pour effectuer différentes opérations.

L’API REST de recherche Log Analytics est un service RESTful qui est accessible par le biais de l’API REST Azure Resource Manager. Ce document présente des exemples montrant comment accéder à l’API à partir d’une ligne de commande PowerShell en utilisant [ARMClient](https://github.com/projectkudu/ARMClient), outil en ligne de commande open source qui simplifie l’appel de l’API Azure Resource Manager. L'utilisation d’ARMClient et de PowerShell est une des nombreuses options vous permettant d’accéder à l'API de recherche Log Analytics. Grâce à ces outils, vous pouvez utiliser l'API RESTful Azure Resource Manager pour effectuer des appels vers les espaces de travail OMS et exécuter en leur sein des commandes de recherche. L'API produira pour vous des résultats de recherche au format JSON, qui vous permet d'utiliser ces résultats, par programme, de différentes manières.

## <a name="prerequisites"></a>Conditions préalables
Actuellement, les alertes peuvent être créées uniquement avec une recherche enregistrée dans Log Analytics.  Vous pouvez consulter l’ [API REST de recherche de journal](log-analytics-log-search-api.md) pour plus d’informations.

## <a name="schedules"></a>Planifications
Une recherche enregistrée peut avoir une ou plusieurs planifications. La planification définit la fréquence à laquelle la recherche est exécutée et l’intervalle de temps pendant lequel les critères sont identifiés.
Les propriétés des planifications sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Intervalle |Fréquence à laquelle la recherche est exécutée. Exprimée en minutes. |
| QueryTimeSpan |Intervalle de temps pendant lequel les critères sont évalués. Doit être égal ou supérieur à Interval. Exprimée en minutes. |
| Version |Version de l’API utilisée.  Actuellement, cette propriété doit toujours être définie sur 1. |

Par exemple, considérez une requête d’événement avec Interval défini sur 15 minutes et QueryTimeSpan sur 30 minutes. Dans ce cas, la requête est exécutée toutes les 15 minutes et une alerte se déclenche si les critères sont satisfaits pendant un intervalle de 30 minutes.

### <a name="retrieving-schedules"></a>Récupération des planifications
Utilisez la méthode Get pour extraire toutes les planifications d’une recherche enregistrée.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Utilisez la méthode Get avec un ID de planification pour extraire une planification particulière pour une recherche enregistrée.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Voici un exemple de réponse pour une planification.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Création d'une planification
Utilisez la méthode Put avec un ID de planification unique pour créer une planification.  Notez que deux planifications ne peuvent pas avoir le même ID, même si elles sont associées à d’autres recherches enregistrées.  Quand vous créez une planification dans la console OMS, un GUID est créé pour l’ID de la planification.

> [!NOTE]
> Le nom de l’ensemble des recherches enregistrées, des planifications et des actions créées avec l’API Log Analytics doit être en minuscules.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Modification d’une planification
Utilisez la méthode Put avec un ID de planification existant pour la même recherche enregistrée afin de modifier cette planification.  Le corps de la demande doit inclure l’ETag de la planification.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Suppression de planifications
Pour supprimer une planification, utilisez la méthode Delete avec un ID de planification.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Actions
Une planification peut avoir plusieurs actions. Une action peut définir un ou plusieurs processus à effectuer, tels que l’envoi d’un courrier électronique ou le démarrage d’un runbook, ou elle peut définir un seuil qui détermine si les résultats d’une recherche satisfont à certains critères.  Certaines actions définissent ces deux aspects afin que les processus soient exécutés quand le seuil est atteint.

Toutes les actions des planifications sont décrites dans le tableau suivant.  Différents types d’alertes ont différentes propriétés supplémentaires qui sont décrites ci-dessous.

| Propriété | Description |
|:--- |:--- |
| Type |Type de l’action.  Actuellement, les valeurs possibles sont Alert et Webhook. |
| Nom |Nom d’affichage de l’alerte. |
| Version |Version de l’API utilisée.  Actuellement, cette propriété doit toujours être définie sur 1. |

### <a name="retrieving-actions"></a>Récupération des actions
Utilisez la méthode Get pour récupérer toutes les actions d’une planification.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Utilisez la méthode Get avec l’ID d’action pour récupérer une action particulière d’une planification.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Création ou modification des actions
Utilisez la méthode Put avec un ID d’action propre à la planification pour créer une activité.  Quand vous créez une action dans la console OMS, un GUID est défini pour l’ID d’action.

> [!NOTE]
> Le nom de l’ensemble des recherches enregistrées, des planifications et des actions créées avec l’API Log Analytics doit être en minuscules.

Utilisez la méthode Put avec un ID d’action existant pour la même recherche enregistrée afin de modifier cette planification.  Le corps de la demande doit inclure l’ETag de la planification.

Le format de demande pour la création d’une action variant selon le type d’action, des exemples sont fournis dans les sections ci-dessous.

### <a name="deleting-actions"></a>Suppression des actions
Utilisez la méthode Delete avec l’ID d’action pour supprimer une action.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Actions d’alerte
Une planification doit avoir une, et une seule, action d’alerte.  Les actions d’alerte ont une ou plusieurs des sections indiquées dans le tableau suivant.  Chacune est décrite plus en détail ci-dessous.

| Section | Description |
|:--- |:--- |
| Seuil |Critères d’exécution de l’action. |
| EmailNotification |Envoyer des messages à plusieurs destinataires. |
| Correction |Démarrer un runbook dans Azure Automation pour tenter de résoudre le problème identifié. |

#### <a name="thresholds"></a>Seuils
Une action d’alerte ne doit avoir qu’un seul seuil.  Quand les résultats d’une recherche enregistrée correspondent au seuil dans une action associée à cette recherche, tous les autres processus dans cette action sont exécutés.  En outre, une action peut ne contenir qu’un seuil et être ainsi utilisable avec d’autres types d’actions ne comportant pas de seuils.

Les propriétés des seuils sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Opérateur  |Opérateur de comparaison de seuil. <br> gt = supérieur à <br> lt = inférieur à |
| Valeur |Valeur du seuil. |

Par exemple, considérez une requête d’événement avec Interval défini sur 15 minutes, QueryTimeSpan sur 30 minutes et Threshold sur une valeur supérieure à 10. Dans ce cas, la requête est exécutée toutes les 15 minutes et une alerte se déclenche si la requête retourne 10 événements créés en l’espace de 30 minutes.

Voici un exemple de réponse pour une action comportant uniquement un seuil.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Utilisez la méthode Put avec un ID d’action unique pour créer une action de seuil pour une planification.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Utilisez la méthode Put avec un ID d’action existant pour modifier une action de seuil pour une planification.  Le corps de la demande doit inclure l’ETag de l’action.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Notification par courrier électronique
Les notifications par courrier électronique envoient un e-mail à un ou plusieurs destinataires.  Leurs propriétés sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Recipients |Liste d’adresses de messagerie. |
| Objet |Objet de l’e-mail. |
| Pièce jointe |Les pièces jointes n’étant pas actuellement prises en charge, cette propriété est toujours définie sur « None ». |

Voici un exemple de réponse pour une action de notification par courrier électronique comportant un seuil.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Utilisez la méthode Put avec un ID d’action unique pour créer une action de messagerie pour une planification.  L’exemple suivant crée une notification par courrier électronique avec un seuil afin que l’e-mail soit envoyé quand les résultats de la recherche enregistrée dépassent le seuil.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Utilisez la méthode Put avec un ID d’action existant pour modifier une action de messagerie pour une planification.  Le corps de la demande doit inclure l’ETag de l’action.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Actions de correction
Les corrections démarrent un runbook dans Azure Automation qui essaie de corriger le problème identifié par l’alerte.  Vous devez créer un webhook pour le runbook utilisé dans une action de correction, puis spécifier l’URI dans la propriété WebhookUri.  Quand vous créez cette action à l’aide de la console OMS, un webhook est automatiquement créé pour le runbook.

Les propriétés des corrections sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| RunbookName |Nom du runbook. Il doit correspondre à un runbook publié dans le compte automation configuré dans la solution Automation au sein de votre espace de travail OMS. |
| WebhookUri |URI du webhook. |
| Expiry |Date et heure d’expiration du webhook.  Si le webhook n’a pas d’expiration, il peut s’agir de n’importe quelle date future valide. |

Voici un exemple de réponse pour une action de correction comportant un seuil.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Utilisez la méthode Put avec un ID d’action unique pour créer une action corrective pour une planification.  L’exemple suivant crée une correction avec un seuil afin que le runbook démarre quand les résultats de la recherche enregistrée dépassent le seuil.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Utilisez la méthode Put avec un ID d’action existant pour modifier une action corrective pour une planification.  Le corps de la demande doit inclure l’ETag de l’action.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Exemple
Voici un exemple complet pour créer une alerte par courrier électronique.  Cet exemple crée une planification avec une action qui contient un seuil et un e-mail.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Actions de webhook
Les actions de webhook démarrent un processus en appelant une URL et, éventuellement, en fournissant une charge utile à envoyer.  Elles sont similaires aux actions correctives, à la différence qu’elles sont destinées à des webhooks qui peuvent appeler des processus autres que des runbooks Azure Automation.  En outre, elles permettent de fournir une charge utile à remettre au processus distant.

Les actions de webhook n’ont pas de seuil ; en revanche, elles doivent être ajoutées à une planification qui a une action d’alerte comportant un seuil.  Vous pouvez ajouter plusieurs actions webhook, qui sont toutes exécutées si le seuil est atteint.

Les propriétés des actions de webhook sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| WebhookUri |Objet de l’e-mail. |
| CustomPayload |Charge utile personnalisée à envoyer au webhook.  Le format dépend de ce que le webhook attend. |

Voici un exemple de réponse pour l’action webhook et une action d’alerte associée comportant un seuil.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Créer ou modifier une action de webhook
Utilisez la méthode Put avec un ID d’action unique pour créer une action webhook pour une planification.  L’exemple suivant crée une action de webhook et une action d’alerte avec un seuil pour que le webhook soit déclenché quand les résultats de la recherche enregistrée dépassent le seuil.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Utilisez la méthode Put avec un ID d’action existant pour modifier une action webhook pour une planification.  Le corps de la demande doit inclure l’ETag de l’action.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Étapes suivantes
* Utilisez l’ [API REST pour effectuer des recherches de journaux](log-analytics-log-search-api.md) dans Log Analytics.

