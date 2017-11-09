---
title: Monitoring dans Azure Database pour MySQL  | Microsoft Docs
description: "Cet article décrit les métriques de monitoring et d’alerte pour Azure Database pour MySQL, notamment les statistiques sur l’UC, les limites, le stockage et la connexion."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: 9af447d54faa8ee96e4b79beb274b437eea57626
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitoring dans Azure Database pour MySQL
Le monitoring des données sur vos serveurs vous permet de résoudre les problèmes et d’optimiser votre charge de travail. Azure Database pour MySQL propose diverses métriques qui fournissent des insights sur le comportement des ressources prenant en charge le serveur MySQL. 

## <a name="metrics"></a>Mesures
Toutes les métriques Azure ont une fréquence d’une minute et chaque métrique fournit 30 jours d’historique. 

Vous pouvez configurer des alertes sur les métriques. Pour des instructions étape par étape, consultez [Configurer des alertes](howto-alert-on-metric.md). 

Les autres tâches incluent la configuration d’actions automatisées, l’exécution d’une analytique avancée et l’archivage de l’historique. Pour plus d’informations, consultez la [Vue d’ensemble des métriques Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Liste des métriques
Ces métriques sont disponibles pour Azure Database pour MySQL :

|Mesure|Nom d’affichage de la mesure|Unité|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Pourcentage d’UC en cours d’utilisation.|
|compute_limit|Limite d’unités de calcul|Nombre|Nombre maximal d’unités de calcul de ce serveur|
|compute_consumption_percent|Pourcentage d’unités de calcul|Pourcentage|Pourcentage d’unités de calcul utilisées par rapport à la limite maximale du serveur.|
|memory_percent|Pourcentage de mémoire|Pourcentage|Pourcentage de mémoire en cours d’utilisation.|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Pourcentage d’E/S en cours d’utilisation.|
|storage_percent|Pourcentage de stockage|Pourcentage|Pourcentage de stockage utilisé par rapport à la limite maximale du serveur.|
|storage_used|Stockage utilisé|Octets|Quantité de stockage en cours d’utilisation. Le stockage utilisé par le service inclut les fichiers de base de données, les journaux de transaction et les journaux du serveur.|
|storage_limit|Limite de stockage|Octets|Stockage maximal pour ce serveur.|
|active_connections|Nombre total de connexions actif|Nombre|Nombre de connexions actives sur le serveur.|
|connections_failed|Total de connexions ayant échoué|Nombre|Nombre de connexions au serveur ayant échoué.|


> [!NOTE]
> Une unité de calcul est composée de la mémoire et du processeur. Le pourcentage Unité de calcul est max(%mémoire, %processeur). Examinez les graphiques de la mémoire et du processeur pour identifier ce qui contribue aux modifications du pourcentage Unité de calcul. Pour plus d’informations, consultez [Unités de calcul](concepts-compute-unit-and-storage.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour des instructions étape par étape, consultez [Configurer des alertes](howto-alert-on-metric.md). 
- Pour plus d’informations sur la façon d’accéder aux métriques et de les exporter à l’aide du portail Azure, de l’API REST ou de CLI, consultez [Vue d’ensemble des métriques Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
