---
title: Alertes dans Log Analytics | Microsoft Docs
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
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7f8603c9ddfd3f99ea38ad07b7a8a0a553e5e4dd


---
# <a name="alerts-in-log-analytics"></a>Alertes dans Log Analytics
Les alertes dans Log Analytics identifient des informations importantes dans votre référentiel OMS.  Les règles d’alerte exécutent automatiquement des recherches de journal selon une planification et créent un enregistrement d’alerte si les résultats satisfont à des critères spécifiques.  La règle peut ensuite exécuter automatiquement une ou plusieurs actions pour vous avertir de l’alerte ou appeler un autre processus de façon proactive.   

![Alertes Log Analytics](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>Création d’une règle d’alerte
Pour créer une règle d’alerte, vous commencez par créer une recherche de journal pour les enregistrements qui doivent appeler l’alerte.  Vous pouvez ensuite utiliser le bouton **Alerte** pour créer et configurer la règle d’alerte.

1. Dans la page de présentation d’OMS, cliquez sur **Recherche de journal**.
2. Créez une requête de recherche de journal ou sélectionnez une recherche de journal enregistrée. 
3. Cliquez sur **Alerte** en haut de la page pour ouvrir l’écran **Ajouter une règle d’alerte**.
4. Reportez-vous aux tableaux ci-dessous pour plus d’informations sur les options de configuration de l’alerte.
5. Quand vous fournissez la fenêtre de temps de règle d’alerte, le nombre d’enregistrements qui satisfont aux critères de recherche pour cette fenêtre s’affiche.  Cela peut vous aider à déterminer la fréquence correspondant au nombre de résultats attendus.
6. Cliquez sur **Enregistrer** pour terminer la règle d’alerte.  Son exécution démarre immédiatement.

![Ajouter une règle d’alerte](media/log-analytics-alerts/add-alert-rule.png)

| Propriété | Description |
|:--- |:--- |
| **Informations sur l’alerte** | |
| Nom |Nom unique identifiant la règle d’alerte. |
| Niveau de gravité |Gravité de l’alerte créée par cette règle. |
| Requête de recherche |Sélectionnez **Utiliser la requête de recherche actuelle** pour utiliser la requête actuelle ou sélectionnez une recherche enregistrée dans la liste.  La syntaxe de la requête est fournie dans la zone de texte, où vous pouvez la modifier si nécessaire. |
| Fenêtre de temps |Spécifie l’intervalle de temps pour la requête.  La requête retourne uniquement les enregistrements qui ont été créés dans cette plage de l’heure actuelle.  Cela peut être toute valeur comprise entre 5 minutes et 24 heures.  La valeur doit être supérieure ou égale à la fréquence de l’alerte.  <br><br>  Par exemple, si la fenêtre de temps est définie sur 60 minutes et que la requête est exécutée à 1h15, seuls les enregistrements créés entre 12h15 et 1h15 apparaissent. |
| **Planification** | |
| Seuil |Critère déterminant la création d’une alerte.  Une alerte est créée si le nombre d’enregistrements retournés par la requête satisfait à ce critère. |
| Fréquence des alertes |Spécifie la fréquence à laquelle la requête doit être exécutée.  Peut être toute valeur comprise entre 5 minutes et 24 heures.  La valeur doit être égale ou inférieure à la fenêtre de temps. |
| Supprimer les alertes |Quand vous activez la suppression de la règle d’alerte, les actions correspondant à la règle sont désactivées pour une durée définie quand une alerte est créée.  La règle est toujours en cours d’exécution et crée des enregistrements d’alerte si les critères sont satisfaits.  Cette option vous donne le temps de résoudre le problème sans exécuter des actions en double. |
| **Actions** | |
| E-mail de notification |Spécifiez **Oui** si vous souhaitez qu’un e-mail soit envoyé quand l’alerte est déclenchée. |
| Objet |Objet de l’e-mail.  Vous ne pouvez pas modifier le corps du message. |
| Destinataires |Adresses de tous les destinataires de l’e-mail.  Si vous spécifiez plusieurs adresses, séparez-les par un point-virgule (;). |
| webhook |Spécifiez **Oui** si vous souhaitez appeler un webhook quand l’alerte est déclenchée. |
| URL du webhook |URL du webhook. |
| Inclure la charge utile JSON personnalisée |Sélectionnez cette option si vous souhaitez remplacer la charge utile par défaut par une charge utile personnalisée. |
| Entrez votre charge utile JSON personnalisée |Charge utile personnalisée pour le webhook.  Pour plus d’informations, consultez la section précédente. |
| Runbook |Spécifiez **Oui** si vous souhaitez démarrer un runbook Azure Automation quand l’alerte est déclenchée. |
| Sélectionner un runbook |Sélectionnez le runbook à démarrer parmi les runbooks disponibles dans le compte Automation configuré dans votre solution Automation. |
| Exécuter sur |Sélectionnez **Azure** pour exécuter le runbook dans le cloud Azure.  Sélectionnez **Worker hybride** pour exécuter le runbook sur un [Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md) dans votre environnement local. |

## <a name="manage-alert-rules"></a>Gérer les règles d’alerte
Vous pouvez obtenir la liste de toutes les règles d’alerte dans le menu **Alertes** des **Paramètres** Log Analytics.  

![Gérer les alertes](./media/log-analytics-alerts/configure.png)

1. Dans la console OMS, sélectionnez la vignette **Paramètres** .
2. Sélectionnez **Alertes**.

Vous pouvez effectuer plusieurs actions à partir de cette vue.

* Désactivez une règle en sélectionnant **Désactivé** en regard de celle-ci.
* Modifiez une règle d’alerte en cliquant sur l’icône en forme de crayon en regard de celle-ci.
* Supprimez une règle d’alerte en cliquant sur l’icône **X** en regard de celle-ci. 

## <a name="setting-time-windows"></a>Définition des fenêtres de temps
### <a name="event-alerts"></a>Alertes d’événement
Les événements incluent les sources de données telles que les journaux des événements Windows, Syslog et les journaux personnalisés.  Vous pouvez créer une alerte quand un événement d’erreur particulier est créé, ou quand plusieurs événements d’erreur sont créés dans une fenêtre de temps spécifique.

Pour associer une alerte à un seul événement, définissez le nombre de résultats sur une valeur supérieure à 0, et la fréquence et la fenêtre de temps sur 5 minutes.  Cette configuration exécute la requête toutes les 5 minutes et vérifie si un événement spécifique a été créé depuis la dernière exécution de la requête.  Une fréquence plus longue peut rallonger le temps qui s’écoule entre l’événement collecté et l’alerte créée.

Certaines applications peuvent consigner une erreur occasionnelle qui ne doit pas nécessairement déclencher une alerte.  Par exemple, l’application peut recommencer le processus à l’origine de l’événement d’erreur et voir sa nouvelle tentative couronnée de succès.  Dans ce cas, la création de l’alerte ne se justifie que si plusieurs événements sont créés dans une fenêtre de temps spécifique.  

Dans certains cas, vous pouvez créer une alerte en l’absence d’événement.  Par exemple, un processus peut enregistrer des événements réguliers pour indiquer qu’il fonctionne correctement.  S’il ne consigne pas un de ces événements dans une fenêtre de temps spécifique, une alerte doit être créée.  Dans ce cas, vous devez définir le seuil sur *Inférieur à 1*.

### <a name="performance-alerts"></a>Alertes de performances
[données de performances](log-analytics-data-sources-performance-counters.md) sont stockées sous la forme d’enregistrements dans le référentiel OMS.  La valeur de chaque enregistrement est la moyenne mesurée au cours des 30 minutes précédentes.  Si vous souhaitez être averti quand un compteur de performances dépasse un seuil spécifique, ce seuil doit être inclus dans la requête.

Par exemple, pour être averti quand le processeur s’exécute à plus de 90 % de ses capacités pendant 30 minutes, utilisez une requête comme *Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90* et définissez le seuil de la règle d’alerte sur *Supérieur à 0*.  

 Étant donné que les [enregistrements de performances](log-analytics-data-sources-performance-counters.md) sont agrégés toutes les 30 minutes, quelle que soit la fréquence à laquelle vous collectez chaque compteur, une fenêtre de temps inférieure à 30 minutes peut ne retourner aucun enregistrement.  En définissant la fenêtre de temps sur 30 minutes, vous avez la certitude d’obtenir un enregistrement unique pour chaque source connectée qui représente la moyenne sur cette période.

## <a name="alert-actions"></a>Actions d’alerte
Outre créer un enregistrement d’alerte, vous pouvez configurer la règle d’alerte pour qu’elle exécute automatiquement une ou plusieurs actions.  Les actions peuvent de façon proactive vous avertir de l’alerte ou appeler un processus qui essaie de corriger le problème détecté.  Les sections suivantes décrivent les actions actuellement disponibles.

### <a name="email-actions"></a>Actions de messagerie
Les actions de messagerie envoient un e-mail avec les détails de l’alerte à un ou plusieurs destinataires.  Vous pouvez spécifier l’objet du message, mais son contenu répond à un format standard construit par Log Analytics.  Il inclut des informations récapitulatives telles que le nom de l’alerte, en plus des détails pouvant décrire jusqu’à dix enregistrements retournés par la recherche de journal.  En outre, il comporte un lien vers une recherche de journal dans Log Analytics qui retourne l’ensemble des enregistrements à partir de cette requête.   L’expéditeur du message est *Équipe Microsoft Operations Management Suite &lt;noreply@oms.microsoft.com&gt;*. 

### <a name="webhook-actions"></a>Actions de webhook
Les actions de webhook permettent d’appeler un processus externe par le biais d’une simple requête HTTP POST.  Le service appelé doit prendre en charge les webhooks et déterminer comment il doit utiliser toute charge utile qu’il reçoit.  Vous pouvez également appeler une API REST qui ne prend pas spécifiquement en charge les webhooks, à condition qu’elle comprenne le format de la requête.  Parmi les exemples d’utilisation d’un webhook en réponse à une alerte, citons l’utilisation d’un service tel que [Slack](http://slack.com) pour envoyer un message avec les détails de l’alerte ou la création d’un incident dans un service tel que [PagerDuty](http://pagerduty.com/).  

Pour une description complète de la création d’une règle d’alerte avec un webhook pour appeler un exemple de service, consultez [Webhooks dans les alertes Log Analytics](log-analytics-alerts-webhooks.md).

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


Pour étudier un exemple complet de création d’une règle d’alerte avec un webhook pour démarrer un service externe, consultez [Exemple de webhook d’alerte dans Log Analytics](log-analytics-alerts-webhooks.md).

### <a name="runbook-actions"></a>Actions de runbook
Les actions de runbook démarrent un runbook dans Azure Automation.  Ce type d’action suppose que la [solution Automation](log-analytics-add-solutions.md) est installée et configurée dans votre espace de travail OMS.  Si elle n’est pas installée quand vous créez une règle d’alerte, un lien vers son installation s’affiche.  Vous pouvez effectuer votre choix parmi les runbooks disponibles dans le compte Automation que vous avez configuré dans la solution Automation.

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


## <a name="alert-records"></a>Enregistrements d’alerte
Les enregistrements d’alerte créés par des règles d’alerte dans Log Analytics ont pour **Type** **Alert** et pour **SourceSystem** **OMS**.  Les propriétés des enregistrements sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Type |*Alert* |
| SourceSystem |*OMS* |
| AlertSeverity |Niveau de gravité de l’alerte. |
| AlertName |Nom de l’alerte. |
| Interroger |Texte de la requête qui a été exécutée. |
| QueryExecutionEndTime |Fin de l’intervalle de temps pour la requête. |
| QueryExecutionStartTime |Début de l’intervalle de temps pour la requête. |
| TimeGenerated |Date et heure de la création de l’alerte. |

Il existe d’autres genres d’enregistrements d’alerte créés par la [solution de gestion des alertes](log-analytics-solution-alert-management.md) et par les [exportations Power BI](log-analytics-powerbi.md).  Ces enregistrements ont tous pour **Type** **Alert**, mais se distinguent par leur propriété **SourceSystem**.

## <a name="next-steps"></a>Étapes suivantes
* Installez la [solution de gestion des alertes](log-analytics-solution-alert-management.md) pour analyser les alertes créées dans Log Analytics, ainsi que les alertes collectées à partir de System Center Operations Manager (SCOM).
* Approfondissez vos connaissances sur les [recherches de journal](log-analytics-log-searches.md) pouvant générer des alertes.
* Effectuez une procédure pas à pas pour [configurer un webhook](log-analytics-alerts-webhooks.md) avec une règle d’alerte.  
* Apprenez à écrire des [runbooks dans Azure Automation](https://azure.microsoft.com/documentation/services/automation) pour corriger les problèmes identifiés par des alertes.




<!--HONumber=Nov16_HO3-->


