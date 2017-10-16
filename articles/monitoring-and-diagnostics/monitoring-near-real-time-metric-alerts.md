---
title: "Alertes Métrique en temps quasi réel dans Azure Monitor | Microsoft Docs"
description: "Les alertes Métrique en temps quasi réel permettent de surveiller des métriques de ressources Azure jusqu’à une fréquence de 1 minute."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Alertes Métrique en temps quasi réel (préversion)
Azure Monitor prend désormais en charge un nouveau type d’alertes Métrique appelées alertes Métrique en temps quasi réel (préversion). Cette fonctionnalité est actuellement disponible en préversion publique.
Ces alertes diffèrent des alertes Métrique régulières à plusieurs égards :

- **Latence améliorée** : les alertes Métrique en temps quasi réel permettent de surveiller les modifications des valeurs de métriques jusqu’à des intervalles de 1 minute.
- **Contrôle renforcé des conditions de métrique** : les alertes Métrique en temps quasi réel permettent aux utilisateurs de définir des règles d’alerte plus riches. Les alertes prennent désormais en charge la surveillance des valeurs maximales, minimales, moyennes et totales des métriques.
- **Surveillance combinée de plusieurs métriques** : les alertes Métrique en temps quasi réel peuvent surveiller plusieurs métriques (actuellement deux) avec une seule règle. Une alerte est déclenchée si les deux les métriques violent leurs seuils respectifs durant la période spécifiée.
- **Système de notification modulaire** : les alertes Métrique en temps quasi réel utilisent des [groupes d’actions](monitoring-action-groups.md). Cette fonctionnalité permet aux utilisateurs de créer des actions sous forme modulaire, et de les réutiliser pour plusieurs règles d’alerte.

> [!NOTE]
> Les alertes Métrique en temps quasi réel sont actuellement disponible en préversion publique. Leur fonctionnalité et l’expérience utilisateur sont susceptibles de changer.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Pour quelles ressources puis-je créer près des alertes Métrique en temps quasi réel ?
Voici la liste complète des types de ressources pris en charge par les alertes Métrique en temps quasi réel :

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>Créer une alerte Métrique en temps quasi réel
Actuellement, il n’est possible de créer des alertes Métrique en temps quasi réel que via le portail Azure. La configuration d’alertes Métrique en temps quasi réel via PowerShell, Azure CLI et l’API REST Azure Monitor sera bientôt prise en charge.

1. Sur le [portail](https://portal.azure.com/), localisez la ressource que vous souhaitez surveiller et sélectionnez-la. Cette ressource doit être de l’un des types répertoriés dans la [section précédente](#what-resources-can-i-create-near-real-time-metric-alerts-for). Vous pouvez faire de même pour tous les types de ressources pris en charge de façon centralisée à partir de Moniteur > Alertes.

2. Sélectionnez **Alertes** ou **Règles d’alerte** dans la section SURVEILLANCE. Le texte et l’icône peuvent varier légèrement pour les différentes ressources.
   ![Surveillance](./media/insights-alerts-portal/AlertRulesButton.png)

3. Cliquez sur la commande **Ajouter une alerte Métrique en temps quasi réel (préversion)** . Si la commande est grisée, assurez-vous que la ressource est sélectionnée dans le filtre.

    ![Bouton Ajouter une alerte Métrique en temps quasi réel](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. **Nommez** votre règle d’alerte, puis choisissez une **Description** qui indique également les adresses électroniques de notification.
5. Sélectionnez la **Métrique** à surveiller, puis choisissez une **Condition**, une **Agrégation du temps** et une valeur de **Seuil** pour la métrique. Sélectionnez éventuellement une autre **Métrique** à surveiller, puis choisissez une **Condition**, une **Agrégation du temps** et une valeur de **Seuil** valeur pour la deuxième métrique. 

    ![Ajouter l’alerte Métrique en temps quasi réel1](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png)![Ajouter l’alerte Métrique en temps quasi réel2](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png)
6. Choisissez également la **Période** pendant laquelle les règles de métrique doivent être respectées avant que l’alerte se déclenche. Ainsi, par exemple, si vous utilisez la période « Au cours des 5 dernières minutes » et que votre alerte recherche une utilisation de l’UC supérieure à 80 % (et une valeur NetworkIn supérieure à 500 Mo), l’alerte se déclenche quand l’utilisation de l’UC dépasse 80 % pendant 5 minutes. Après le premier déclenchement, elle se déclenche à nouveau lorsque l’UC reste au-dessous de 80 % pendant cinq minutes. L’alerte est évaluée conformément à la **Fréquence d’évaluation**.


6. Choisissez une **Gravité** appropriée dans la liste déroulante.

7. Spécifiez si vous souhaitez utiliser un **Groupe d’actions** existant ou en créer un.

8. Si vous choisissez de **créer** un groupe d’actions, attribuez-lui un nom et un nom court, spécifiez des actions (SMS, E-mail, Webhook) et complétez les détails requis.


8. Quand vous avez terminé, sélectionnez **OK** pour créer l’alerte.   

Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="managing-near-real-time-metric-alerts"></a>Gestion des alertes Métrique en temps quasi réel
Une fois que vous avez créé une alerte, vous pouvez la sélectionner et :

* Afficher un graphique indiquant le seuil de la métrique et les valeurs réelles du jour précédent.
* La modifier ou la supprimer.
* La **Désactiver** ou l’**Activer** si vous voulez arrêter temporairement ou reprendre l’envoi de notifications pour cette alerte.



