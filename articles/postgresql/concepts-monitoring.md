---
title: Surveillance dans Azure Database pour PostgreSQL | Microsoft Docs
description: "Cet article décrit les métriques de surveillance et d’alerte pour Azure Database pour PostgreSQL, notamment les statistiques sur l’UC, les limites, le stockage et la connexion."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: d48159fbc5e52bf1916a744e3912ca7ceb0ab60f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="monitoring-in-azure-database-for-postgresql"></a>Surveillance dans Azure Database pour PostgreSQL
La surveillance des données relatives à vos serveurs vous aide à résoudre les problèmes et à optimiser votre charge de travail. Azure Database pour PostgreSQL propose diverses métriques qui donnent des informations sur le comportement des ressources prenant en charge le serveur MySQL. 

## <a name="metrics"></a>Mesures
Toutes les métriques Azure présentent une fréquence d’une minute et chaque métrique fournit 30 jours d’historique. 

Vous pouvez configurer des alertes basées sur les métriques. Pour obtenir des instructions détaillées, consultez [Configurer des alertes sur les métriques](howto-alert-on-metric.md). 

Les autres tâches incluent la configuration d’actions automatisées, l’exécution d’analyses avancées et l’archivage de l’historique. Pour plus d’informations, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Liste des métriques
Les métriques suivantes sont disponibles pour Azure Database pour PostgreSQL :

|Mesure|Nom d’affichage de la mesure|Unité|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Pourcentage d’UC en cours d’utilisation.|
|compute_limit|Limite d’unités de calcul|Nombre|Nombre maximal d’unités de calcul de ce serveur.|
|compute_consumption_percent|Pourcentage d’unités de calcul|Pourcentage|Pourcentage d’unités de calcul utilisées sur la limite maximale du serveur.|
|memory_percent|Pourcentage de mémoire|Pourcentage|Pourcentage de mémoire en cours d’utilisation.|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Pourcentage d’E/S en cours d’utilisation.|
|storage_percent|Pourcentage de stockage|Pourcentage|Pourcentage de stockage utilisé sur la limite maximale du serveur.|
|storage_used|Stockage utilisé|Octets|Quantité de stockage en cours d’utilisation. Le stockage utilisé par le service inclut les fichiers de base de données, les journaux des transactions et les journaux du serveur.|
|storage_limit|Limite de stockage|Octets|Stockage maximal pour ce serveur.|
|active_connections|Nombre total de connexions actif|Nombre|Nombre de connexions actives sur le serveur.|
|connections_failed|Total de connexions ayant échoué|Nombre|Nombre de connexions au serveur ayant échoué.|


> [!NOTE]
> Une unité de calcul est composée de ressources de mémoire et d’UC. Le pourcentage d’unités de calcul est max(%mémoire, %processeur). Examinez les graphiques de la mémoire et de l’UC pour identifier ce qui contribue aux variations du pourcentage d’unités de calcul. Pour plus d’informations, consultez [Unités de calcul](concepts-compute-unit-and-storage.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir des instructions détaillées, consultez [Configurer des alertes sur les métriques](howto-alert-on-metric.md). 
- Pour savoir comment accéder aux métriques et les exporter à l’aide du portail, de l’API REST ou de l’interface de ligne de commande Azure, consultez [Vue d’ensemble des mesures dans Microsot Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
