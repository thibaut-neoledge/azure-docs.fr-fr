---
title: "Surveiller l’utilisation de la base de données avec Intelligent Insights - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database Intelligent Insights utilise l’intelligence intégrée pour surveiller en permanence l’utilisation de la base de données et détecter les événements perturbateurs entraînant des performances médiocres."
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 8e55fa02d5edda21fbf547397d63f9d1cf987a60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="intelligent-insights"></a>Intelligent Insights

***&amp;#8220;Intelligent Insights vous permet de suivre les performances de votre base de données.&amp;#8221;***

Azure SQL Database Intelligent Insights utilise l’intelligence intégrée pour surveiller en permanence l’utilisation de la base de données et détecter les événements perturbateurs entraînant des performances médiocres. Une fois un tel événement détecté, une analyse détaillée est effectuée, qui génère un journal de diagnostic avec une évaluation intelligente du problème. Cette évaluation se compose d’une analyse de cause racine du problème de performances de la base de données et, dans la mesure du possible, de recommandations pour une amélioration des performances. C’est ce qu’on appelle ***&#8220;Intelligent Insights.&#8221;*** 

## <a name="what-can-intelligent-insights-do-for-you"></a>Qu’est-ce qu’Intelligent Insights peut faire pour vous ?

Intelligent Insights est une fonctionnalité unique de l’intelligence intégrée d’Azure, qui apporte la valeur suivante :

- Surveillance proactive
- Analyse des performances personnalisée
- Détection anticipée de la dégradation des performances de la base de données
- Analyse de la cause racine des problèmes détectés
- Recommandations pour l’amélioration des performances
- Capacité de montée en charge sur des centaines de milliers de bases de données
- Impact positif sur les ressources de DevOps et le coût total de possession

## <a name="how-does-intelligent-insights-work"></a>Comment fonctionne Intelligent Insights ?

Intelligents Insights analyse les performances d’Azure SQL Database en comparant la charge de travail de la base de données au cours de la dernière heure à la ligne de base de la charge de travail sur les 7 derniers jours. La charge de travail de la base de données est composée de requêtes déterminées comme étant les plus significatives pour les performances de la base de données (par exemple, les requêtes les plus répétées et les plus grandes). Chaque base de données étant unique sur le plan de la structure, des données, de l’utilisation et de l’application, chaque ligne de base de la charge de travail générée est unique propre à une instance individuelle. Intelligent Insights, indépendamment de la ligne de base de la charge de travail, analyse des seuils opérationnels absolus et détecte des problèmes de temps d’attente excessifs, des exceptions critiques et des problèmes de paramétrages des requêtes susceptibles d’affecter les performances.

Une fois un problème de dégradation des performances détecté à partir de plusieurs métriques observées à l’aide de l’intelligence artificielle, une analyse est effectuée pour générer un journal de diagnostic contenant des informations intelligentes sur ce qui se passe avec votre base de données. Intelligent Insights facilite le suivi du problème de performances de la base de données de son apparition initiale jusqu’à sa résolution. Cela est possible en suivant les états de chaque problème détecté pendant tout le cycle de vie de celui-ci comprenant la détection initiale du problème, la vérification de l’amélioration des performances et la résolution. Des mises à jour sont fournies dans le journal de diagnostic toutes les 15 minutes. 

<center>![Serveur](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)</center>

Les métriques utilisées pour mesurer et détecter les problèmes de performances de la base de données sont basées sur la durée des requêtes, les demandes de délai d’expiration, les délais d’attente excessifs et les demandes erronées. Elles sont développées davantage dans la section [Métriques de détection](sql-database-intelligent-insights.md#detection-metrics) de ce document.

## <a name="degradations-detected"></a>Dégradations détectées

Les dégradations des performances d’Azure SQL Database identifiées sont enregistrées dans le journal de diagnostic avec des entrées intelligentes comprenant les propriétés suivantes :

| Propriété             | Détails              |
| :------------------- | ------------------- |
| les informations de base de données  | Métadonnées relatives à une base de données sur laquelle une information a été détectée, telles qu’un URI de ressource |
| Période observée | Heures de début et de fin de la période des informations détectées |
| Métriques impactées | Métriques à l’origine de la génération d’une information : <ul><li>Augmentation de la durée de la requête [secondes]</li><li>Attente excessive [secondes]</li><li>Demandes ayant expiré [pourcentage]</li><li>Demandes erronées [pourcentage]</li></ul>|
| Valeur d’impact | Valeur d’une métrique mesurée |
| Requêtes impactées et codes d’erreur | Hachage de requête ou code d’erreur. Ces éléments peuvent être utilisés pour établir facilement une corrélation avec les requêtes impactées. Des métriques ayant trait à une augmentation de durée de requête, à un temps d’attente, à des nombres d’expiration ou à des codes d’erreur sont fournies. |
| Détections | Détection identifiée au niveau de la base de données pendant la durée d’un événement. Il existe 15 modèles de détection. Voir [Résoudre les problèmes de performances liés à Azure SQL Database avec Intelligence Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Analyse de la cause racine | Analyse de la cause racine du problème identifié dans un format lisible. Certaines informations peuvent contenir une recommandation d’amélioration des performances lorsque cela est possible. |
|||

## <a name="issues-state-lifecycle-active-verifying-and-completed"></a>Cycle de vie des états d’un problème : Actif, Vérification et Terminé

Les problèmes de performances enregistrés dans le journal de diagnostic sont marqués de l’un des trois états de cycle de vie de problème, à savoir Actif, Vérification et Terminé. Une fois qu’un problème de performances est détecté, et tant que l’intelligence intégrée d’Azure SQL considère que le problème est présent, celui-ci est marqué comme étant &#8220;***Actif***&#8221;. Quand le problème est considéré comme corrigé, une vérification a lieu et l’état du problème est modifié en &#8220;***Vérification***&#8221;. Une fois que l’intelligence intégrée d’Azure SQL considère que le problème est résolu, l’état de celui-ci est modifié en &#8220;***Terminé***&#8221;.

## <a name="using-intelligent-insights"></a>Utilisation d’Intelligent Insights

Le journal de diagnostic d’Intelligent Insights peut être envoyé à Azure Log Analytics, à Azure Event Hub et au Stockage Azure, comme décrit dans [Journalisation des métriques et diagnostics d’Azure SQL Database](sql-database-metrics-diag-logging.md). Une fois le journal envoyé à une de ces cibles, il est possible de l’utiliser pour un développement personnalisé d’alertes et de surveillance à l’aide d’outils Microsoft ou tiers. Pour des procédures de résolution des problèmes de performances d’Azure SQL Database à l’aide d’Intelligent Insights, voir [Résoudre les problèmes de performances liés à Azure SQL Database avec Intelligence Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="built-in-intelligent-insights-analytics-with-azure-log-analytics"></a>Analyses Intelligent Insights intégrées avec Azure Log Analytics 

La solution Azure Log Analytics offre des fonctionnalités de création de rapports et d’alertes en plus des données du journal de diagnostic d’Intelligent Insights. Vous trouverez ci-dessous un exemple de rapport Intelligent Insights dans Azure SQL Analytics.

![Serveur](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Une fois le journal de diagnostic d’Intelligent Insights configuré pour diffuser en streaming des données vers Azure SQL Analytics, vous pouvez [surveiller Azure SQL Database à l’aide d’Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Intégrations personnalisées du journal d’Intelligent Insights

Pour le développement d’alertes et d’une surveillance personnalisées à l’aide d’outils Microsoft ou d’outils tiers, voir [Utiliser le journal de diagnostic du niveau de performance d’Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="how-to-set-up-intelligent-insights-with-azure-event-hub"></a>Comment configurer Intelligent Insights avec Azure Event Hub

- Configurez Intelligent Insights pour diffuser en streaming les événements du journal vers Azure Event Hub en suivant la procédure décrite dans [Diffuser en streaming les journaux de diagnostic Azure vers un espace de noms Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).
- Utilisez Azure Event Hub pour personnaliser la surveillance et les alertes en suivant la procédure décrite dans [Que faire des métriques et journaux de diagnostic dans Azure Event Hub](sql-database-metrics-diag-logging.md#stream-into-azure-storage). 

## <a name="how-to-set-up-intelligent-insights-with-azure-storage"></a>Comment configurer Intelligent Insights avec le Stockage Azure

- Configurez Intelligent Insights pour que les informations soient stockées avec le Stockage Azure en suivant la procédure décrite dans [Diffuser en streaming sur le Stockage Azure](sql-database-metrics-diag-logging.md#stream-into-azure-storage).

## <a name="detection-metrics"></a>Métriques de détection

Les métriques utilisées pour les modèles de détection qui génèrent des informations d’Intelligent Insights sont basées sur la surveillance des éléments suivants :

- Durée de la requête
- Demandes de délai d’expiration
- Délais d’attente excessifs 
- Demandes erronées

Le durée de la requête et les demandes de délai d’expiration sont utilisées comme modèles principaux dans la détection des problèmes de performances de la charge de travail de la base de données. C’est parce que ces métriques mesurent directement ce qui se passe avec la charge de travail. Afin de détecter tous les cas possibles de dégradation des performances de la charge de travail, des délais d’attente excessifs et des demandes erronées sont utilisés en tant que modèles supplémentaires pour indiquer des problèmes affectant les performances de la charge de travail.

Le système prend automatiquement en considération les changements de charge de travail et les modifications du nombre de demandes de requête adressées à la base de données afin de déterminer de façon dynamique les seuils normaux et anormaux de performances de la base de données.

Toutes les métriques sont prises en considération dans différentes relations via un modèle de données élaboré scientifiquement, qui classe par catégories les problèmes de performances détectés. Les informations fournies via une analyse intelligente comprennent des détails sur le problème de performances détecté, une analyse de la cause racine du problème, et des recommandations sur la façon d’améliorer les performances d’Azure SQL Database surveillées lorsque cela est possible.

## <a name="query-duration"></a>Durée de la requête

Un modèle de dégradation de la durée de requête analyse des requêtes individuelles et détecte l’augmentation du temps nécessaire pour compiler et exécuter une requête par rapport à la ligne de base des performances.

Quand l’intelligence intégrée d’Azure SQL Database détecte une augmentation significative du temps de compilation ou d’exécution de requêtes ayant un impact sur les performances de la charge de travail, ces requêtes sont marquées comme présentant un problème de dégradation des performances de durée. 

Le journal de diagnostic d’Intelligent Insights génère le hachage de requête de la requête dont les performances sont dégradées, indique si la dégradation des performances est liée à l’augmentation du temps de compilation ou d’exécution de la requête, et la durée accrue de la requête.

## <a name="timeout-requests"></a>Demandes de délai d’expiration

Un modèle de dégradation des demandes de délai d’expiration analyse les requêtes individuelles et détecte toute augmentation des délais d’expiration au niveau de l’exécution de la requête, ainsi que les demandes de délai d’expiration globales au niveau de la base de données comparées à la période de référence des performances.

Étant donné que certaines requêtes peuvent expirer avant même d’atteindre l’étape d’exécution, l’intelligence intégrée d’Azure SQL Database mesure et analyse, sur la base des workers abandonnés par rapport aux demandes effectuées, toutes les requêtes qui ont atteint la base de données, qu’elles aient atteint ou non l’étape d’exécution. 

Lorsque le nombre de délais d’expiration pour les requêtes exécutées, ou le nombre de workers de demande abandonnés dépasse le seuil géré par le système, un journal de diagnostic est rempli d’informations intelligentes.

Les informations générées contiennent le nombre de demandes ayant expiré, le nombre de requêtes ayant expiré et une indication spécifiant si la dégradation des performances était liée à une augmentation du délai d’expiration à l’étape d’exécution, ou au niveau global de la base de données. Quand l’augmentation des délais d’expiration est considérée comme significative pour les performances de la base de données, ces requêtes sont marquées comme présentant un problème de dégradation des performances de délai d’expiration. 

## <a name="excessive-wait-times"></a>Délais d’attente excessifs

Un modèle de délai d’attente excessif surveille les requêtes de base de données individuelles et détecte les statistiques d’attente de requête anormalement élevée dépassant le seuil absolu géré par le système. Les métriques de délai d’attente excessif de requête sont observées à l’aide de la nouvelle fonctionnalité de statistiques d’attente du magasin des données requêtes (sys.query_store_wait_stats) de SQL Server :

- Atteinte des limites de ressources
- Atteinte des limites de ressources de pool élastique
- Nombre excessif de threads de travail ou de session
- Verrouillage de base de données excessif
- Sollicitation de la mémoire
- Autres statistiques d’attente

L’atteinte des limites de ressources ou des limites de ressources de pool élastique indique que la consommation des ressources disponibles sur un abonnement ou dans le pool élastique a augmenté au-delà des seuils absolus, ce qui traduit une dégradation des performances de la charge de travail. Un nombre excessif de threads de travail ou de session indique une condition où le nombre de threads de travail ou de sessions ouvertes a dépassé les seuils absolus, ce qui traduit une dégradation des performances de la charge de travail.

Un verrouillage de base de données excessif indique une condition où le nombre de verrous sur une base de données a dépassé un seuil absolu, ce qui traduit une dégradation des performances de la charge de travail. Une sollicitation de la mémoire est une condition où le nombre de threads qui demandent des allocations de mémoire a dépassé un seuil absolu, ce qui traduit une dégradation des performances de la charge de travail.

La détection d’autres statistiques d’attente indique une condition où diverses métriques mesurées via les statistiques d’attente du magasin de données des requêtes dépassent un seuil absolu, ce qui traduit une dégradation des performances de la charge de travail.

Lorsque des délais d’attente excessifs sont détectés, le journal de diagnostic d’Intelligent Insights produit des résultats variables en fonction des données disponibles, des hachages des requêtes affectant et affectées dont les performances sont dégradées, des détails des métriques entraînant un délai d’attente d’exécution des requêtes et d’un délai d’attente mesuré.

## <a name="errored-requests"></a>Demandes erronées

Un modèle de dégradation des demandes erronées surveille les requêtes individuelles et détecte toute augmentation du nombre de requêtes erronées par rapport à la période de référence. Ce modèle surveille également les exceptions critiques qui ont atteint les seuils absolus gérés par l’intelligence intégrée d’Azure SQL Database. Le système prend automatiquement en considération le nombre de demandes de requête adressées à la base de données, et rend compte de toutes les modifications de la charge de travail survenues durant la période analysée.

Lorsque l’augmentation mesurée des demandes erronées en relation avec le nombre total de demandes effectuées est considéré comme significatif pour les performances de la charge de travail, les requêtes concernées sont marquées comme présentant le problème de dégradation des performances des demandes erronées.

Le journal d’Intelligent Insights renvoie le nombre de demandes erronées, une indication spécifiant si la dégradation des performances était liée à une augmentation des requêtes erronées ou à l’atteinte de l’un des seuils surveillés d’exception critique, et le temps mesuré de la dégradation des performances. 

Si l’une des exceptions critiques surveillées dépasse le seuil absolu géré par le système, une information intelligente est générée avec des détails sur l’exception critique.

## <a name="next-steps"></a>Étapes suivantes
* Apprendre à [résoudre les problèmes de performances liés à Azure SQL Database avec Intelligence Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
* Utiliser le [Journal de diagnostic des performances d’Intelligent Insights dans Azure SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md)
* Apprendre à [surveiller Azure SQL Database à l’aide d’Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md)
* Apprendre à [collecter et utiliser les données de journaux de vos ressources Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)


