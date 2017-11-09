---
title: "Métriques Azure Event Hubs dans Azure Monitor (préversion) | Microsoft Docs"
description: "Utilisation d’Event Hubs pour surveiller Event Hubs"
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: sethm
ms.openlocfilehash: c3ee6a87592145801167b35ff281c04062e525da
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2017
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Métriques Azure Event Hubs dans Azure Monitor (préversion)

Les métriques Event Hubs vous donnent l’état de vos ressources Event Hubs dans votre abonnement Azure. Avec un ensemble riche de données de métriques, vous pouvez évaluer l’intégrité globale de vos concentrateurs d’événements au niveau de l’espace de noms, mais également au niveau de l’entité. Ces statistiques peuvent être importantes car elles vous aident à surveiller l’état de vos concentrateurs d’événements. Les métriques peuvent également vous aider à résoudre les problèmes constituant des causes premières sans avoir à contacter le support technique Azure.

Azure Monitor fournit des interfaces utilisateur unifiées pour la surveillance entre divers services Azure. Pour plus d’informations, consultez [Analyse dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) et [Récupérer des métriques Azure Monitor avec .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) sur GitHub.

## <a name="access-metrics"></a>Accéder aux mesures

Azure Monitor propose plusieurs méthodes d’accès aux mesures. Vous pouvez accéder à ces métriques depuis le [portail Azure](https://portal.azure.com), ou utiliser les API d’Azure Monitor (REST et .Net) et les solutions d’analyse comme Operation Management Suite et Event Hub. Pour plus d’informations, voir [Mesures Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Les métriques sont activées par défaut, et vous pouvez accéder aux 30 derniers jours de données. Si vous souhaitez conserver des données sur une période plus longue, vous pouvez archiver les données de mesures dans un compte de stockage Azure. Celui-ci est configuré dans les [paramètres de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) dans Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Accéder aux métriques dans le portail

Vous pouvez surveiller les mesures au fil du temps dans le [portail Azure](https://portal.azure.com). L’exemple suivant montre comment afficher les demandes réussies et les demandes entrantes au niveau du compte :

![][1]

Vous pouvez également accéder aux métriques directement via l’espace de noms. Pour ce faire, sélectionnez votre espace de noms, puis **Métriques (préversion)**. Pour afficher les métriques filtrées à l’étendue du concentrateur d’événements, sélectionnez le concentrateur d’événements, puis cliquez sur **Métriques (préversion)**.

Pour les métriques prenant en charge des dimensions, vous devez filtrer avec la valeur de dimension souhaitée, comme dans l’exemple suivant :

![][2]

## <a name="billing"></a>Facturation

L’utilisation de mesures dans Azure Monitor est actuellement gratuite pendant la préversion. Toutefois, si vous utilisez des solutions supplémentaires absorbant des données de métriques, vous pouvez être facturé par ces solutions. Par exemple, vous êtes facturé par Stockage Azure si vous archivez des données de mesures pour un compte de stockage Azure. Vous êtes également facturé par Operation Management Suite (OMS) si vous diffusez en continu des données de métriques vers OMS pour une analyse avancée.

Les métriques suivantes vous donnent une vue d’ensemble de l’intégrité de votre service. 

> [!NOTE]
> Nous déprécions plusieurs métriques lors de leur déplacement sous un autre nom. Vous devrez peut-être mettre à jour vos références. Les métriques marquées avec le mot-clé « dépréciée » ne seront pas gérées à l’avenir.

Toutes les valeurs de métriques sont envoyées à Azure Monitor toutes les minutes. Le fragment de temps définit l’intervalle de temps pour lequel des valeurs de métriques sont présentées. L’intervalle de temps pris en charge pour toutes les métriques de Event Hubs est de 1 minute.

## <a name="request-metrics"></a>Métriques de demande

Compte le nombre de requêtes d’opérations de données et de gestion.

| Nom de métrique | Description |
| ------------------- | ----------------- |
| Demandes entrantes (préversion) | Le nombre de requêtes effectuées auprès de Azure Event Hubs sur une période spécifiée. <br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName |
| Requêtes ayant réussi (préversion)   | Le nombre de requêtes réussies auprès de Azure Event Hubs sur une période spécifiée. <br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName |
| Erreurs du serveur (préversion) | Le nombre de requêtes non traitées en raison d’une erreur sur Azure Event Hubs sur une période spécifiée. <br/><br/>Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName |
|Erreurs utilisateur (préversion)|Le nombre de demandes non traitées en raison d’erreurs utilisateur sur une période spécifiée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Demandes limitées (préversion)|Le nombre de demandes qui ont été limitées car l’utilisation d’une unité de débit a été dépassée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Erreurs de quota dépassé (préversion)|Le nombre de requêtes a dépassé le quota disponible. Consultez [cet article](event-hubs-quotas.md) pour plus d’informations sur les quotas Event Hubs.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|

## <a name="throughput-metrics"></a>Métriques de débit

| Nom de métrique | Description |
| ------------------- | ----------------- |
|Demandes limitées (préversion)|Le nombre de demandes qui ont été limitées car l’utilisation d’une unité de débit a été dépassée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|

## <a name="message-metrics"></a>Métriques de message

| Nom de métrique | Description |
| ------------------- | ----------------- |
|Messages entrants (préversion)|Le nombre d’événements ou de messages envoyés à Event Hubs sur une période spécifiée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Messages sortants (préversion)|Le nombre d’événements ou de messages récupérés depuis Event Hubs sur une période spécifiée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Octets entrants (préversion)|Le nombre d’octets envoyés au service Azure Event Hubs sur une période spécifiée.<br/><br/> Unité : octets <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Octets sortants (préversion)|Le nombre d’octets récupérés depuis le service Azure Event Hubs sur une période spécifiée.<br/><br/> Unité : octets <br/> Type d’agrégation : total <br/> Dimension : EntityName|

## <a name="connection-metrics"></a>Métriques de connexion

| Nom de métrique | Description |
| ------------------- | ----------------- |
|ActiveConnections (préversion)|Le nombre de connexions actives sur un espace de noms ainsi que sur une entité.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Ouvertures de connexion (préversion)|Le nombre de connexions ouvertes.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Connexions fermées (préversion)|Le nombre de connexions fermées.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|

## <a name="event-hubs-capture-metrics"></a>Métriques de capture Event Hubs

Vous pouvez surveiller les métriques de capture Event Hubs lorsque vous activez la fonctionnalité de capture pour vos concentrateurs d’événements. Les métriques suivantes décrivent ce que vous pouvez analyser avec la capture activée.

| Nom de métrique | Description |
| ------------------- | ----------------- |
|Backlog de capture (préversion)|Le nombre d’octets qui ne sont pas encore être capturés dans la destination choisie.<br/><br/> Unité : octets <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Messages capturés (préversion)|Le nombre de messages ou d’événements capturés dans la destination choisie sur une période spécifiée.<br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimension : EntityName|
|Octets capturés (préversion)|Le nombre d’octets capturés dans la destination choisie sur une période spécifiée.<br/><br/> Unité : octets <br/> Type d’agrégation : total <br/> Dimension : EntityName|

## <a name="metrics-dimensions"></a>Dimensions de mesures

Azure Event Hubs prend en charge les dimensions suivantes pour les mesures dans Azure Monitor. L’ajout de dimensions à vos métriques est facultatif. Si vous n’ajoutez pas de dimensions, les métriques sont spécifiées au niveau de l’espace de noms. 

| Nom de métrique | Description |
| ------------------- | ----------------- |
|EntityName| Event Hubs prend en charge les entités de concentrateurs d’événements sous l’espace de noms.|

## <a name="next-steps"></a>Étapes suivantes

* Consultez la [Vue d’ensemble de surveillance Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* Exemple [Récupérer les métriques Azure Monitor avec .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) sur GitHub. 

Pour plus d’informations sur les concentrateurs d’événements, accédez aux liens suivants :

* Prise en main avec un [didacticiel des concentrateurs d’événements](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)
* [Exemples d’applications qui utilisent des Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



