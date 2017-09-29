---
title: Journal de diagnostic du niveau de performance Intelligent Insights - Azure SQL Database | Microsoft Docs
description: "Intelligent Insights fournit un journal de diagnostic des problèmes de performance d’Azure SQL Database"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 4890baa4ead3323834a82b3f9340cf751bf0c755
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="use-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Utiliser le journal de diagnostic du niveau de performance d’Azure SQL Database Intelligent Insights

Cette page fournit des informations sur l’utilisation du journal de diagnostic du niveau de performances d’Azure SQL Database généré par [Insights Intelligent](sql-database-intelligent-insights.md), son format et les données qu’il contient pour vos besoins de développement personnalisés. Ce journal de diagnostic peut être envoyé à [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hub](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Stockage Azure](sql-database-metrics-diag-logging.md#stream-into-azure-storage) ou une solution tierce pour des fonctionnalités d’alertes et de rapports DevOps personnalisées.

## <a name="log-header"></a>En-tête du journal

Le journal de diagnostic utilise le format standard JSON pour générer des conclusions d’Intelligent Insights. La propriété de catégorie exacte permettant d’accéder au journal d’Intelligent Insights est une valeur fixe &#8220; **SQLInsights**&#8221;.

L’en-tête du journal est commun et se compose du timestamp (TimeGenerated) de la création d’une entrée ainsi qu’un ID de ressource (ResourceId) faisant référence à une base de données Azure SQL Database à laquelle l’entrée se rapporte. La catégorie (Category), le niveau (Level) et le nom de l’opération (OperationName) sont des propriétés fixes dont la valeur ne change pas : ils indiquent que l’entrée de journal est fournie à titre d’information et qu’elle provient d’analyses intelligentes (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ID du problème et base de données concernés

La propriété d’identification des problèmes (issueId_d) offre un moyen unique de suivi des problèmes de performances jusqu'à leur résolution. Intelligent Insights observe le cycle de vie de chaque problème comme suit : Active (actif), Verifying (en cours de vérification) et Completed (terminé). Par le biais de chacune de ces phases d’état Intelligent Insights peut enregistrer plusieurs enregistrements d’événement dans le journal et pour chacune de ces entrées le numéro d’ID du problème reste unique. Intelligent Insights effectue le suivi de ce problème par le biais de son cycle de vie et génère une information dans le journal de diagnostic toutes les 15 minutes.

Une fois qu’un problème de performances est détecté et aussi longtemps qu’il dure, il est considéré comme « Active » sous la propriété de statut (Status). Une fois qu’un problème détecté est atténué, il est vérifié et signalé comme « Verifying » sous la propriété de statut (Status). Dans le cas où le problème n’existe plus, la propriété status (Status) signale ce problème en tant que « Completed ».
De pair avec l’ID du problème, le journal de diagnostic indique les timestamps de début (intervalStartTime_t) et de fin (intervalEndTme_t) de l’événement particulier associé à un problème signalé dans le journal de diagnostic.

Le pool élastique de propriété (elasticPoolName_s) indique à quel pool élastique la base de données avec un problème appartient. Si la base de données ne fait pas partie d’un pool élastique, cette propriété n’a aucune valeur. La base de données dans laquelle un problème a été détecté est révélée dans le nom de la base de données (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported timestamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported timestamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: Active, Verifying and Complete
```

## <a name="detected-issues"></a>Problèmes détectés

La section suivante du journal de performances Intelligent Insights contient les problèmes de performances détectées via l’intelligence artificielle intégrée. Les détections sont révélées dans les détections de propriété JSON, à savoir la catégorie d’un problème, l’impact des problèmes, les requêtes affectées et les métriques. Il convient de noter que la propriété de détections peut contenir plusieurs problèmes de performances.

Les problèmes de performance détectés sont signalés avec la structure de propriété de détection suivante :

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // tperformance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Les modèles de performance détectables et les détails générés dans le journal de diagnostic figurent dans le tableau ci-dessous.

### <a name="detection-category"></a>Catégorie de détection

La propriété Category (catégorie) décrit la catégorie de modèles de performance détectables. Consultez le tableau suivant pour toutes les catégories possibles des modèles de performance détectables. Plus de détails sont disponibles sur la page [Résolution des problèmes de performances de base de données avec Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

Selon le problème de performances détecté, les détails générés dans le fichier du journal de diagnostic diffèrent en conséquence.

| Modèles de performances détectables | Détails générés |
| :------------------- | ------------------- |
| Atteinte des limites de ressources | <li>Ressources affectées</li><li>Hachages de requête</li><li>Pourcentage de la consommation de ressources</li> |
| Augmentation de la charge de travail | <li>Nombre de requêtes dont l’exécution a augmenté</li><li>Hachages de requêtes avec la plus grande contribution à l’augmentation de la charge de travail</li> |
| Sollicitation de la mémoire | <li>Régisseur de mémoire</li> |
| Verrouillage | <li>Hachages de requête affectés</li><li>Blocage des hachages de requête</li> |
| Augmentation de MAXDOP | <li>Hachages de requête</li><li>Temps d’attente CXP</li><li>Temps d’attente</li> |
| Contention de verrous de page | <li>Interroger les hachages de requêtes à l’origine de la contention</li> |
| Index manquant | <li>Hachages de requête</li> |
| Nouvelle requête | <li>Hachage de requête des nouvelles requêtes</li> |
| Statistiques d’attente inhabituelles | <li>Types d’attente inhabituels</li><li>Hachages de requête</li><li>Temps d’attente de requête</li> |
| Contention de TempDB | <li>Interroger les hachages de requêtes à l’origine de la contention</li><li>Attribution de requête au délai d’attente de contention de verrous de page de la base de données globale [ % ]</li> |
| Pénurie de DTU du pool élastique | <li>Pool élastique</li><li>Base de données de consommation de DTU supérieur</li><li>Pourcentage du DTU du pool utilisé par le consommateur supérieur</li> |
| Régression de plan | <li>Hachage de requête</li><li>ID de plan corrects</li><li>ID de plan incorrects</li><li>Hachages de requête</li> |
| Modification de la valeur de configuration à l’échelle de la base de données | <li>Modifications de configuration à l’échelle de la base de données par rapport aux valeurs par défaut</li> |
| Client lent | <li>Hachages de requête</li><li>Temps d’attente</li> |
| Passage à une version antérieure du niveau tarifaire | <li>Notification de texte</li> |

### <a name="impact"></a>Impact

La propriété Impact (impact) décrit à quel point un comportement détecté a contribué au problème rencontré par la base de données, avec un chiffre allant de 1 à 3 : 3 correspond à la contribution la plus élevée, 2 correspond à une contribution modéré et 1 est la contribution la plus faible. La valeur d’impact peut être utilisée comme entrée pour l’automatisation d’alerte personnalisée, en fonction de vos besoins spécifiques. La propriété des requêtes impactées (QueryHashes) fournit une liste des requêtes de hachage qui ont été affectés par une détection en particulier.

### <a name="impacted-queries"></a>Requêtes impactées

La section suivante du journal Intelligent Insights fournit des informations sur les requêtes particulières qui ont été affectés par les problèmes de performance détectés. Cette information est indiquée en tant que tableau d’objets incorporés dans la propriété impact_s. La propriété Impact se compose d’entités et de mesures. Les entités font référence à une requête spécifique (type : Query) et un hachage de requête unique révélé sous la valeur de propriété (Value). En outre, chacune des requêtes divulguées est suivie d’une mesure et d’une valeur, indiquant un problème de performances détecté.

Dans l’exemple de journal suivant, il convient de noter que la requête avec le hachage 0x9102EXZ4 a été détectée car elle se caractérise par une augmentation de la durée d’exécution (mesure : DurationIncreaseSeconds) avec la valeur de 110 secondes, ce qui indique que l’exécution de cette requête spécifique a duré 110 secondes de plus. Il convient de noter que plusieurs requêtes peuvent être détectées en raison du fait que cette section du journal spécifique peut inclure plusieurs entrées de requête.

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Mesures

L’unité de mesure pour chaque mesure signalée est fournie sous la propriété metric (métrique) avec les valeurs possibles : secondes, nombre et pourcentage. La valeur d’une métrique mesurée est indiquée dans la propriété value (valeur).

La propriété DurationIncreaseSeconds fournit une unité de mesure, en secondes, et pour CriticalErrorCount, l’unité de mesure est un nombre représentant un nombre d’erreurs.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Recommandations en matière d’analyse et d’amélioration de la cause racine

La dernière partie du journal de performances Intelligent Insights se rapporte à l’analyse automatisée des causes racine du problème identifié de dégradation des performances identifié dans une formulation conviviale par le biais de la propriété d’analyse de la cause racine (rootCauseAnalysis_s). Les recommandations d’amélioration sont incluses dans la formulation enregistrée dans le journal dans les cas où cela est possible.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Le journal de performances Intelligent Insights peut être utilisé avec [Azure Log Analytics]( https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql) ou une solution tierce pour des fonctionnalités d’alertes et de rapports DevOps personnalisées.

## <a name="next-steps"></a>Étapes suivantes
- Découvrir les concepts [Intelligent Insights](sql-database-intelligent-insights.md)
- Apprendre à [résoudre les problèmes de performances liés à Azure SQL Database avec Intelligence Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- Apprendre à [surveiller Azure SQL Database à l’aide d’Azure SQL Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)
- [Collecter et utiliser des données de journaux à partir de vos ressources Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)




