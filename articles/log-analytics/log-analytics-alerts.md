---
title: "Vue d’ensemble des alertes dans OMS Log Analytics | Microsoft Docs"
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
ms.sourcegitcommit: ec7167fb36e54219957629699f33dfce950f86d5
ms.openlocfilehash: 48d921650dbbf3f9cc2a8a0e265e0c7deaebdafd
ms.lasthandoff: 03/01/2017


---
# <a name="respond-to-issues-in-log-analytics-using-alerts"></a>Résoudre les problèmes dans Log Analytics à l’aide des alertes

Les alertes identifient des informations importantes dans votre référentiel Log Analytics.  Elles vous signalent les problèmes de manière proactive et vous permettent de mettre en œuvre des actions automatisées pour les résoudre.  Cet article vous fournit une vue d’ensemble de la création et de l’utilisation des alertes.  


>[!NOTE]
> Pour plus d’informations sur les règles d’alerte relatives aux mesures métriques qui sont actuellement en version préliminaire publique, consultez [New metric measurement alert rule type in Public Preview!](https://blogs.technet.microsoft.com/msoms/2016/11/22/new-metric-measurement-alert-rule-type-in-public-preview/) (Nouveau type de règle d’alerte pour les mesures métriques en version préliminaire publique).

## <a name="alert-rules"></a>Règles d'alerte

Les alertes sont créées par des règles dédiées, qui exécutent automatiquement des recherches de journaux à intervalles réguliers.  Si les résultats de la recherche satisfont des critères particuliers, un enregistrement d’alerte est généré.  La règle peut également exécuter automatiquement une ou plusieurs actions pour vous avertir de l’alerte ou appeler un autre processus de façon proactive.  

![Alertes Log Analytics](media/log-analytics-alerts/overview.png)


Les règles d’alerte sont définies par les détails suivants.

- **Recherche de journaux**.  Il s’agit de la requête qui sera exécutée à chacun des déclenchements de la règle d’alerte.  Les enregistrements renvoyés par cette requête permettent de déterminer si une alerte a été créée.
- **Fenêtre de temps**.  Spécifie l’intervalle de temps pour la requête.  La requête retourne uniquement les enregistrements qui ont été créés dans cette plage précédant son exécution.  Cela peut être toute valeur comprise entre 5 minutes et 24 heures. Par exemple, si la fenêtre de temps est définie sur 60 minutes et que la requête est exécutée à 1h15, seuls les enregistrements créés entre 12h15 et 1h15 apparaissent.
- **Fréquence**.  Spécifie la fréquence à laquelle la requête doit être exécutée. Peut être toute valeur comprise entre 5 minutes et 24 heures. La valeur doit être égale ou inférieure à la fenêtre de temps.  Si la valeur est supérieure à celle de la fenêtre de temps, vous risquez de manquer des enregistrements.<br>Par exemple, imaginons une fenêtre de temps de 30 minutes associée à une fréquence de 60 minutes.  Si la requête est exécutée à 13 h, les enregistrements entre 12 h 30 et 13 h sont renvoyés.  La requête s’exécute ensuite à 14 h, moment auquel elle renvoie les enregistrements entre 13 h 30 et 14 h.  Les enregistrements créés entre 13 h et 13 h 30 ne seront jamais analysés.
- **Seuil**.  Si le nombre d’enregistrements renvoyés par la recherche de journaux dépasse le seuil défini, une alerte est créée.

## <a name="creating-alert-rules"></a>Création des règles d’alerte
Plusieurs méthodes peuvent être mises à profit pour la création et la modification des règles d’alerte.  Pour des instructions détaillées, consultez les articles suivants.  

- [Create and manage alert rules in Log Analytics with the OMS portal](log-analytics-alerts-creating.md) (Créer et gérer des règles d’alerte dans Log Analytics à l’aide du portail OMS).
- [Gérer Log Analytics à l’aide de modèles Azure Resource Manager](log-analytics-template-workspace-configuration.md).
- [API REST d’alerte Log Analytics](log-analytics-api-alerts.md).

## <a name="alert-actions"></a>Actions d’alerte

La règle d’alerte peut exécuter une ou plusieurs actions durant la création d’un enregistrement d’alerte.  Vous pouvez par exemple envoyer un message en réponse à l’alerte ou démarrer un processus amorçant une mesure corrective.  

Vous pouvez par ailleurs mettre à profit les actions pour optimiser la fonctionnalité de Log Analytics avec d’autres services.  Par exemple, aucune fonction de signalement par SMS ou téléphone n’est actuellement proposée.  Cependant, l’action webhook d’une règle d’alerte permet d’appeler un service comme [PagerDuty](https://www.pagerduty.com/) qui offre ce type de fonctionnalités.  Pour consulter un exemple de création d’action webhook pour envoyer un message à l’aide de [Slack](https://slack.com/), accédez à la section [Webhooks dans les alertes Log Analytics](log-analytics-alerts-webhooks.md).

Le tableau suivant répertorie les actions pouvant être exécutées.  Pour en savoir plus sur chacune d’entre elles, consultez la page [Adding actions to alert rules in Log Analytics](log-analytics-alerts-actions.md) (Ajout d’actions aux règles d’alerte dans Log Analytics). 

| Action | Description |
|:--|:--|
| Email  |     Envoyez un e-mail avec les détails de l’alerte à un ou plusieurs destinataires. |
| webhook | Appelez un processus externe par le biais d’une simple requête HTTP POST. |
| Runbook | Démarrez un runbook dans Azure Automation. |


## <a name="alerting-scenarios"></a>Scénarios d’alerte

### <a name="events"></a>Événements
Pour associer une alerte à un seul événement, créez une règle d’alerte en définissant le nombre de résultats sur une valeur **supérieure à 0**, et la fréquence et la fenêtre de temps sur **5 minutes**.  Cette configuration exécute la requête toutes les 5 minutes et vérifie si un événement spécifique a été créé depuis la dernière exécution de la requête.  Une fréquence plus longue peut rallonger le temps qui s’écoule entre l’événement collecté et l’alerte créée.  Le cas échéant, vous utiliseriez une requête similaire à ce qui suit pour définir l’événement qui vous intéresse.

    Type=Event Source=MyApplication EventID=7019 

Certaines applications peuvent consigner une erreur occasionnelle qui ne doit pas nécessairement déclencher une alerte.  Par exemple, l’application peut recommencer le processus à l’origine de l’événement d’erreur et voir sa nouvelle tentative couronnée de succès.  Dans ce cas, la création de l’alerte ne se justifie que si plusieurs événements sont créés dans une fenêtre de temps spécifique.  Ici, vous utiliseriez la même requête mais définiriez le seuil sur une valeur supérieure.  Par exemple, pour déclencher une alerte pour 5 événements sur un intervalle de 30 minutes, vous définissez la fréquence sur **5 minutes**, la fenêtre de temps sur **30 minutes** et le nombre de résultats sur une valeur **supérieure à 4**.    

Dans certains cas, vous pouvez créer une alerte en l’absence d’événement.  Par exemple, un processus peut enregistrer des événements réguliers pour indiquer qu’il fonctionne correctement.  S’il ne consigne pas un de ces événements dans une fenêtre de temps spécifique, une alerte doit être créée.  Dans ce cas, vous définissez le nombre de résultats sur une valeur **inférieure à 1**.

### <a name="performance-alerts"></a>Alertes de performances
À l’image des événements, les [données de performances](log-analytics-data-sources-performance-counters.md) sont stockées sous la forme d’enregistrements dans le référentiel Log Analytics.  Si vous souhaitez être averti quand un compteur de performances dépasse un seuil spécifique, ce seuil doit être inclus dans la requête.

Par exemple, pour être averti quand le processeur s’exécute à plus de 90 % de ses capacités, utilisez une requête comme la suivante en définissant une valeur **supérieure à 0** pour le nombre de résultats.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Pour être averti lorsque la moyenne d’exécution du processeur dépasse 90 % de ses capacités pendant dans une fenêtre de temps spécifique, utilisez une requête comme la suivante avec la [commande measure](log-analytics-search-reference.md#commands) comme suit et définissez le seuil de la règle d’alerte sur une valeur **supérieure à 0**. 

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90


## <a name="alert-records"></a>Enregistrements d’alerte
Les enregistrements d’alerte créés par des règles d’alerte dans Log Analytics ont pour **Type** **Alert** et pour **SourceSystem** **OMS**.  Les propriétés des enregistrements sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Type |*Alert* |
| SourceSystem |*OMS* |
| AlertName |Nom de l’alerte. |
| AlertSeverity |Niveau de gravité de l’alerte. |
| LinkToSearchResults |Lien vers la recherche de journal Log Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| Requête |Texte de la requête qui a été exécutée. |
| QueryExecutionEndTime |Fin de l’intervalle de temps pour la requête. |
| QueryExecutionStartTime |Début de l’intervalle de temps pour la requête. |
| ThresholdOperator | Opérateur utilisé par la règle d’alerte. |
| ThresholdValue | Valeur utilisée par la règle d’alerte. |
| TimeGenerated |Date et heure de la création de l’alerte. |

Il existe d’autres genres d’enregistrements d’alerte créés par la [solution de gestion des alertes](log-analytics-solution-alert-management.md) et par les [exportations Power BI](log-analytics-powerbi.md).  Ces enregistrements ont tous pour **Type** **Alert**, mais se distinguent par leur propriété **SourceSystem**.


## <a name="next-steps"></a>Étapes suivantes
* Créez une règle d’alerte à l’aide du [portail OMS](log-analytics-alerts-creating.md).
* Installez la [solution de gestion des alertes](log-analytics-solution-alert-management.md) pour analyser les alertes créées dans Log Analytics, ainsi que les alertes collectées à partir de System Center Operations Manager (SCOM).
* Approfondissez vos connaissances sur les [recherches de journal](log-analytics-log-searches.md) pouvant générer des alertes.
* Effectuez une procédure pas à pas pour [configurer un webhook](log-analytics-alerts-webhooks.md) avec une règle d’alerte.  
* Apprenez à écrire des [runbooks dans Azure Automation](https://azure.microsoft.com/documentation/services/automation) pour corriger les problèmes identifiés par des alertes.


