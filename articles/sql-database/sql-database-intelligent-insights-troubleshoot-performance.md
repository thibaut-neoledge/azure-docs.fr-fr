---
title: "Résoudre les problèmes de performances liés à Azure SQL Database avec Intelligence Insights | Microsoft Docs"
description: "Intelligent Insights vous permet de résoudre les problèmes de performances liés à Azure SQL Database."
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
ms.openlocfilehash: edd8c526a681c9cd5226ede6110f21c3362aaef6
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Résoudre les problèmes de performances liés à Azure SQL Database avec Intelligence Insights

Cette page fournit des informations sur les problèmes de performances liés à Azure SQL Database détectés par le biais du journal de diagnostic des performances de base de données [Insights Intelligent](sql-database-intelligent-insights.md). Ce journal de diagnostic peut être envoyé à [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hub](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Stockage Azure](sql-database-metrics-diag-logging.md#stream-into-azure-storage) ou une solution tierce pour des fonctionnalités d’alertes et de rapports DevOps personnalisées.

> [!NOTE]
> Pour appréhender rapidement la résolution des problèmes de performances liés à Azure SQL Database via Intelligent Insights, consultez l’organigramme [Flux de résolution des problèmes recommandé](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) dans le présent document.
>

## <a name="detectable-database-performance-patterns"></a>Modèles de performances de base de données détectables

Intelligent Insights détecte automatiquement les problèmes de performances liés à Azure SQL Database en fonction des temps d’attente, erreurs ou délais d’expiration de l’exécution des requêtes, puis génère des modèles de performances détectées dans le journal de diagnostic. Les modèles de performances détectables sont résumés dans le tableau suivant :

| Modèles de performances détectables | Détails générés |
| :------------------- | ------------------- |
| [Atteinte des limites de ressources](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | La consommation des ressources disponibles (DTU), les threads de travail de base de données ou les sessions de connexion de base de données disponibles sur l’abonnement surveillé ont atteint leurs limites et entraînent des problèmes de performances pour Azure SQL Database. |
| [Augmentation de la charge de travail](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Une augmentation de la charge de travail ou une accumulation continue de la charge de travail sur la base de données a été détectée et entraîne des problèmes de performances pour Azure SQL Database. |
| [Sollicitation de la mémoire](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Les rôles de travail qui demandent des allocations de mémoire les attendent pendant des intervalles de temps statistiquement importants, ou un nombre de plus en plus grand de rôles de travail demandent des allocations de mémoire, ce qui affecte les performances d’Azure SQL Database. |
| [Verrouillage](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Un verrouillage excessif de la base de données a été détecté et impacte les performances d’Azure SQL Database. |
| [Augmentation de MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | L’option Degré maximal de parallélisme (MAXDOP) a été modifiée et affecte l’efficacité de l’exécution des requêtes.  |
| [Contention de verrous de page](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Une contention de verrous de page a été détectée et impacte les performances d’Azure SQL Database. Plusieurs threads tentent simultanément d’accéder aux mêmes pages de tampon de données en mémoire, ce qui augmente les temps d’attente et impacte les performances d’Azure SQL Database. |
| [Index manquant](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Un problème d’index manquant a été détecté et impacte les performances d’Azure SQL Database. |
| [Nouvelle requête](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Une nouvelle requête a été détectée et impacte les performances globales d’Azure SQL Database.  |
| [Statistiques d’attente inhabituelles](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Des temps d’attente de base de données inhabituels ont été détectés et impactent les performances d’Azure SQL Database. |
| [Contention de TempDB](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Plusieurs threads essaient d’accéder aux mêmes ressources TempDB, ce qui provoque un goulet d’étranglement qui impactent les performances d’Azure SQL Database. |
| [Pénurie de DTU du pool élastique](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | La pénurie de DTU disponibles dans le pool élastique affecte les performances d’Azure SQL Database. |
| [Régression de plan](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Un nouveau plan ou une modification de la charge de travail d’un plan existant ont été détectés et impactent les performances d’Azure SQL Database. |
| [Modification de la valeur de configuration à l’échelle de la base de données](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Une modification de configuration apportée à la base de données affecte les performances d’Azure SQL Database. |
| [Client lent](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Un client d’application lent, incapable d’utiliser suffisamment rapidement la sortie d’Azure SQL Database, a été détecté et impacte les performances d’Azure SQL Database. |
| [Rétrogradation à un niveau tarifaire inférieur](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | La rétrogradation à un niveau tarifaire inférieur a diminué les ressources disponibles et impacte les performances d’Azure SQL Database. |

> [!TIP]
> Pour optimiser en continu les performances d’Azure SQL Database, envisagez d’activer le [paramétrage automatique d’Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning). Il s’agit d’une fonctionnalité unique de l’intelligence intégrée Azure SQL qui surveille en permanence votre base de données SQL Azure, règle automatiquement les index et applique les corrections de plan d’exécution de requête.
>

La section suivante décrit plus en détail les modèles de performances détectables précédemment listés.

## <a name="reaching-resource-limits"></a>Atteinte des limites de ressources

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances détectables combine les problèmes de performances liés à l’atteinte des limites de ressources, des rôles de travail et des sessions disponibles. Une fois que ce problème de performances a été détecté, un champ descriptif du journal de diagnostics indique si le problème est lié aux limites de ressources, des rôles de travail ou des sessions.

Les ressources sur Azure SQL Database sont généralement appelées [ressources DTU](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-what-is-a-dtu) et consistent en une mesure fusionnée des ressources du processeur et des E/S (E/S des journaux de données et des transactions). Le modèle Atteinte des limites de ressources est reconnu lorsqu’une dégradation des performances de requête est détectée et due à l’atteinte de l’une des limites de ressources mesurées.

La ressource Limites de sessions indique le nombre de connexions simultanées disponibles à Azure SQL Database. Ce modèle de performances est reconnu quand des applications qui se connectent à des bases de données Azure SQL Database ont atteint le nombre de connexions simultanées disponibles à la base de données. Si les applications essaient d’utiliser plus de sessions que le nombre disponible sur une base de données, les performances des requêtes sont affectées.

L’atteinte des limites de rôles de travail est un cas spécifique d’atteinte des limites de ressources dans la mesure où les rôles de travail disponibles ne sont pas comptabilisés dans l’utilisation des DTU. L’atteinte des limites de rôles de travail sur une base de données peut entraîner l’augmentation des temps d’attente propres aux ressources et ainsi dégrader les performances de requête.

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère des hachages des requêtes qui ont affecté les performances et les pourcentages de consommation de ressources. Vous pouvez utiliser ces informations comme point de départ de l’optimisation de la charge de travail de votre base de données. Plus particulièrement, vous pouvez envisager d’optimiser les requêtes qui influent sur la dégradation des performances, d’ajouter des index ou d’optimiser les applications avec une distribution de charge de travail plus homogène. Si vous ne pouvez pas réduire les charges de travail ou effectuer une optimisation, vous pouvez éventuellement augmenter le niveau tarifaire de votre abonnement Azure SQL Database afin d’augmenter la quantité de ressources disponibles.

Si vous avez atteint les limites de sessions disponibles, vous pouvez envisager d’optimiser vos applications en réduisant le nombre de connexions à la base de données. Si vous ne pouvez pas réduire le nombre de connexions depuis vos applications à la base de données, vous pouvez envisager d’augmenter le niveau tarifaire de votre base de données, ou de fractionner et déplacer votre base de données dans plusieurs bases de données pour en distribuer la charge de travail de façon plus équilibrée.

Pour plus de suggestions sur la résolution des problèmes liés aux limites de sessions, consultez [Comment traiter les limites de connexions maximales Azure SQL Database](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Pour connaître les limites de ressources disponibles pour le niveau de votre abonnement, consultez [Limites de ressources Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Augmentation de la charge de travail

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances identifie les problèmes provoqués par l’augmentation de la charge de travail, ou dans sa forme la plus grave, par une accumulation de la charge de travail.

Cette détection s’effectue en combinant plusieurs mesures. La mesure de base détecte une augmentation de la charge de travail par rapport à la base de référence de la charge de travail passée. L’autre forme de détection repose sur la mesure d’une grande augmentation dans les threads de travail actifs, suffisamment grande pour avoir un impact sur les performances de requête.

Dans sa forme la plus grave, la charge de travail s’accumule en permanence en raison de l’incapacité d’Azure SQL Database à la gérer. Ainsi, la taille de la charge de travail ne cesse d’augmenter (c.-à-d. qu’elle est en situation d’accumulation). En raison de cette situation, le temps d’attente de l’exécution de la charge de travail augmente, ce qui représente l’un des problèmes de performances de base de données les plus graves. Ce problème est détecté en surveillant l’augmentation du nombre de threads de travail abandonnés. 

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère le nombre de requêtes dont l’exécution a augmenté et le hachage de la requête qui contribue le plus à l’augmentation de la charge de travail. Vous pouvez utiliser ces informations comme point de départ de l’optimisation de la charge de travail et, plus particulièrement, pour la requête identifiée comme contribuant le plus à l’augmentation de la charge de travail.

Vous pouvez également envisager de distribuer les charges de travail de façon plus homogène à la base de données. Vous pouvez aussi envisager d’optimiser la requête qui affecte les performances et ajoute des index. Une autre approche consisterait à distribuer votre charge de travail entre plusieurs bases de données. Si ce n’est pas possible, vous pouvez envisager d’augmenter le niveau tarifaire de votre abonnement Azure SQL Database pour augmenter la quantité de ressources disponibles.

## <a name="memory-pressure"></a>Sollicitation de la mémoire

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances indique une dégradation des performances de la base de données actuelle due à la sollicitation de la mémoire, ou dans sa forme la plus grave, à une condition d’accumulation de mémoire par rapport à la base de référence des performances des 7 jours précédents.

La sollicitation de la mémoire indique une condition de performances où il existe un nombre suffisamment élevé de threads de travail qui demandent des allocations de mémoire dans Azure SQL Database. Une condition d’utilisation élevée de la mémoire existe, dans laquelle Azure SQL Database n’est pas en mesure d’allouer efficacement la mémoire à tous les rôles de travail qui en demande. L’une des raisons les plus courantes de ce problème est liée à la quantité de mémoire disponible pour Azure SQL Database d’une part et, d’autre part, à une augmentation de la charge de travail qui entraîne l’augmentation du nombre de threads de travail et de la sollicitation de la mémoire.

Une forme plus grave de sollicitation de la mémoire implique une condition d’accumulation de mémoire, laquelle indique que le nombre de threads de travail qui demandent des allocations de mémoire est plus élevé que le nombre de requêtes qui libèrent de la mémoire. Ce nombre de threads de travail demandant des allocations de mémoire peut aussi augmenter en continu (c’est-à-dire s’accumuler), puisque le moteur n’est pas capable d’allouer de la mémoire de manière suffisamment efficace pour répondre à la demande. La condition d’accumulation de mémoire représente l’un des problèmes de performances de base de données les plus graves.

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère des détails sur le magasin d’objets mémoire avec le régisseur (c’est-à-dire le thread de travail) marqué en tant que raison la plus grande de l’utilisation élevée de la mémoire, ainsi que les horodatages concernés. Ces informations peuvent être utilisées comme base pour la résolution des problèmes. 

Vous pouvez envisager d’optimiser ou de supprimer des requêtes liées aux régisseurs dont l’utilisation de la mémoire est la plus élevée. Vous pouvez aussi vérifier que vous n’interrogez pas des données que vous ne prévoyez pas d’utiliser. Une bonne pratique consiste à toujours utiliser une clause WHERE dans vos requêtes. De plus, il est recommandé de créer des index non-cluster pour rechercher les données, au lieu d’analyser les données.

Vous pouvez également envisager de réduire la charge de travail, de l’optimiser ou de la distribuer entre plusieurs bases de données. Une autre approche consisterait à distribuer votre charge de travail entre plusieurs bases de données. Si ce n’est pas possible, vous pouvez envisager d’augmenter le niveau tarifaire de votre abonnement Azure SQL Database pour augmenter la quantité de ressources mémoire disponibles pour la base de données.

Pour obtenir d’autres suggestions pour résoudre les problèmes, consultez [Memory Grants Meditation: The mysterious SQL Server memory consumer with Many Names](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/) (Réflexion sur les allocations de mémoire : Le mystérieux consommateur de mémoire SQL Server qui porte plusieurs noms).

## <a name="locking"></a>Verrouillage

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances indique une dégradation des performances de la base de données actuelle, où un verrouillage excessif de la base de données a été détecté par rapport à la base de référence des performances des 7 jours précédents. 

Dans le système de gestion de base de données relationnelle (SGBDR) moderne, le verrouillage est essentiel pour implémenter des systèmes multithread dans lesquels les performances sont maximisées par l’exécution simultanée de plusieurs rôles de travail et transactions de bases de données parallèles quand cela est possible. Plus simplement, le verrouillage dans ce contexte fait référence au mécanisme d’accès intégré dans lequel une seule transaction peut accéder exclusivement aux lignes, pages, tables et fichiers requis sans entrer en concurrence avec une autre transaction concernant des ressources. Quand la transaction qui a verrouillé les ressources à des fins d’utilisation en a terminé avec elles, le verrou sur ces ressources est libéré, ce qui permet à d’autres transactions d’accéder aux ressources requises. Pour plus d’informations sur le verrouillage, consultez [Verrouillage dans le moteur de base de données](https://msdn.microsoft.com/library/ms190615.aspx).

Si les transactions exécutées par le moteur SQL attendent très longtemps pour accéder à des ressources verrouillées à des fins d’utilisation, ce temps d’attente joue un rôle sur le ralentissement des performances d’exécution de la charge de travail. 

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère des détails sur le verrouillage pouvant servir de base pour la résolution des problèmes. Vous pouvez analyser les requêtes bloquantes signalées, c’est-à-dire les requêtes qui introduisent la dégradation des performances de verrouillage, et les supprimer. Dans certains cas, vous pouvez réussir à optimiser les requêtes bloquantes.
La façon la plus simple et la plus sûre d’atténuer ce problème consiste à préférer des transactions courtes et à réduire l’empreinte de verrou des requêtes les plus coûteuses. Vous pouvez envisager de diviser un grand lot d’opérations en plus petites opérations. Une bonne pratique consiste à réduire l’empreinte de verrou des requêtes en les rendant les plus efficaces possible. Pensez à réduire les grosses analyses car elles augmentent le risque d’interblocage et ont un impact négatif sur les performances globales de la base de données. Pour les requêtes identifiées qui entraînent un verrouillage, vous pouvez envisager de créer de nouveaux index ou d’ajouter des colonnes à l’index existant afin d’éviter les analyses de table. Pour plus de suggestions, consultez [Comment résoudre les problèmes de blocage provoqués par l’escalade de verrous dans SQL Server](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Augmentation de MAXDOP

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances détectables indique une condition dans laquelle un plan d’exécution de requête choisi a été mis en parallèle plus qu’il ne devrait. L’optimiseur de requête Azure SQL Database améliore les performances de la charge de travail en décidant d’exécuter la requête en parallèle pour accélérer les choses quand cela est possible. Dans certains cas toutefois, les rôles de travail parallèles qui traitent une requête passent plus de temps à s’attendre les uns les autres pour synchroniser et fusionner les résultats qu’à exécuter la même requête avec moins de rôles de travail parallèles, voire même, parfois, un seul thread de travail.

Le système expert analyse les performances de la base de données actuelle par rapport à la période de référence, puis détermine si une requête préalablement en cours d’exécution s’exécute plus lentement qu’avant parce que le plan d’exécution de la requête est plus parallélisé qu’il ne le devrait.

L’option de configuration de serveur MAXDOP est utilisée sur Azure SQL Database pour contrôler le nombre de cœurs de processeur pouvant être utilisé pour exécuter la même requête en parallèle. 

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère des hachages de requête pour les requêtes dont la durée d’exécution a augmenté en raison d’une parallélisation trop importante. Le journal génère également des temps d’attente CXP. Ce temps représente la durée pendant laquelle un thread organisateur/coordinateur unique (thread 0) attend que tous les autres threads aient terminé avant de fusionner les résultats et de continuer. De plus, le journal de diagnostic génère les temps d’attente d’exécution globale des requêtes peu performantes. Ces informations forment la base de la résolution du problème.

Vous pouvez commencer par examiner les possibilités d’optimiser ou de simplifier les requêtes complexes. Une bonne pratique consiste à diviser les longs programmes de traitement par lots en plus petits programmes. En outre, vérifiez que vous avez des index créés en complément de vos requêtes. Vous pouvez également envisager d’appliquer manuellement l’option MAXDOP (degré maximal de parallélisme) à une requête particulière identifiée comme peu performante. Cette opération est configurée à l’aide de T-SQL. Consultez [Configurer l’option de configuration du serveur Degré maximal de parallélisme](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

La définition de l’option de configuration du serveur MAXDOP sur zéro (0) comme valeur par défaut indique qu’Azure SQL Database peut utiliser tous les cœurs de processeur logiques disponibles pour paralléliser des threads exécutant une seule requête. La définition de MAXDOP sur un (1) indique qu’un seul cœur peut être utilisé l’exécution d’une seule requête. Dans la pratique, cela signifie que le parallélisme est désactivé. En fonction de votre situation, du nombre de cœurs disponibles pour la base de données et des informations du journal de diagnostic, vous pouvez envisager de régler l’option MAXDOP sur le nombre de cœurs pour l’exécution parallèle des requêtes qui a permis de résoudre le problème pour vous.

## <a name="pagelatch-contention"></a>Contention de verrous de page

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances indique que la dégradation des performances de la charge de travail de la base de données actuelle est due à une contention de verrous de page par rapport à la base de référence de la charge de travail des 7 jours précédents.

Les verrous sont des mécanismes de synchronisation légers utilisés par Azure SQL Database pour activer le multithreading et pour garantir la cohérence des structures en mémoire, notamment des index, des pages de données et d’autres structures internes.

Il existe de nombreux types de verrous disponibles sur Azure SQL Database. Pour des raisons de simplicité, des verrous de mémoire tampon sont utilisés pour protéger les pages en mémoire dans le pool de mémoires tampons et des verrous d’E/S sont utilisés pour protéger les pages qui ne sont pas encore chargées dans le pool de mémoires tampons. Chaque fois que des données sont écrites ou lues à partir d’une page dans le pool de mémoires tampons, un thread de travail a besoin de préalablement acquérir un verrou de mémoire tampon pour la page. Chaque fois qu’un thread de travail tente d’accéder à une page qui n’est pas déjà disponible dans le pool de mémoires tampons en mémoire, une demande d’E/S est effectuée pour charger les informations requises à partir du stockage indiquant une forme plus grave de dégradation des performances.

Une contention des verrous de page se produit quand plusieurs threads tentent simultanément d’acquérir des verrous sur la même structure en mémoire, augmentant ainsi le temps d’attente avant l’exécution des requêtes. En cas de contention d’E/S de verrous de page quand des données ont besoin d’être accessibles à partir du stockage, ce temps d’attente est encore plus important et peut donc considérablement affecter les performances de la charge de travail. La contention de verrous de page constitue le scénario le plus courant dans lequel des threads s’attendent les uns les autres et entrent en concurrence pour obtenir des ressources sur plusieurs systèmes de processeur.

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère les détails sur la contention de verrous de page qui peuvent servir de base à la résolution du problème.

Comme un verrou de page est un mécanisme de contrôle interne d’Azure SQL Database, il détermine automatiquement quand les utiliser. Les décisions d’application, notamment la conception de schéma, peuvent affecter le comportement des verrous de page en raison du comportement déterministe des verrous.
Une manière de gérer une contention de verrous consiste à remplacer une clé d’index séquentielle par une clé non séquentielle pour distribuer uniformément les insertions dans une plage d’index. En règle générale, il suffit d’avoir une colonne de début dans l’index qui distribue la charge de travail proportionnellement. Une autre méthode à prendre en compte est le partitionnement de tables. La création d’un schéma de partitionnement de hachage avec une colonne calculée sur une table partitionnée constitue une approche courante pour atténuer une contention de verrous excessive. En cas de contention d’E/S de verrous de page, l’introduction d’index est conseillée car elle permet d’atténuer ce problème de performances. Pour plus d’informations, consultez [Diagnostic et résolution d’une contention de verrous sur SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (téléchargement PDF).

## <a name="missing-index"></a>Index manquant

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances indique une dégradation des performances de la charge de travail de la base de données actuelle par rapport à la base de référence des 7 jours précédents en raison d’un index manquant.

Un index est utilisé pour accélérer les performances des requêtes. Il fournit un accès rapide aux données de table en réduisant le nombre de pages de jeux de données à visiter ou à analyser.

Les requêtes spécifiques qui ont entraîné une dégradation des performances sont identifiés via cette détection pour laquelle la création d’index serait bénéfique pour les performances.

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère des hachages de requête pour les requêtes qui ont été identifiées comme ayant un impact sur les performances de la charge de travail. Vous pouvez envisager de créer des index pour ces requêtes. Vous pouvez également envisager d’optimiser ou de supprimer ces requêtes si elles ne sont pas requises. Dans la pratique, il est conseillé d’éviter d’interroger les données que vous n’utilisez pas.

> [!TIP]
> Saviez-vous que l’intelligence intégrée Azure est capable de gérer automatiquement les index les plus performants de vos bases de données ?
>
> Pour optimiser en continu les performances d’Azure SQL Database, il est recommandé d’activer le [paramétrage automatique d’Azure SQL Database](sql-database-automatic-tuning.md). Il s’agit d’une fonctionnalité unique de l’intelligence intégrée Azure SQL qui surveille en permanence votre base de données SQL Azure, règle et crée automatiquement des index pour vos bases de données.
>

## <a name="new-query"></a>Nouvelle requête

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances détectables indique qu’une nouvelle requête peu performante a été détectée et influe sur les performances de la charge de travail par rapport à la base de référence des performances des 7 jours précédents.

Parfois, l’écriture d’une requête efficace est une tâche difficile. Pour plus d’informations sur l’écriture de requêtes, consultez [Écriture des requêtes SQL](https://msdn.microsoft.com/library/bb264565.aspx) et pour optimiser les performances des requêtes existantes, consultez [Paramétrage des requêtes](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Résolution des problèmes

Le journaux de diagnostic génère des informations sur un maximum de deux (2) nouvelles requêtes qui consomment le plus de processeur, y compris leurs hachages de requête. Puisque la requête détectée exerce un impact sur les performances de la charge de travail, vous pouvez envisager d’optimiser votre requête. Il est conseillé de ne pas récupérer les données que vous n’utilisez pas. Utiliser des requêtes avec une clause WHERE est également recommandé. Simplifier les requêtes complexes et diviser les grandes requêtes en requêtes plus petites sont également recommandés. Il est également conseillé de fractionner les requêtes de traitement par lots volumineuses en requêtes plus petites. Introduire des index pour les nouvelles requêtes constitue généralement une bonne pratique pour atténuer ce problème de performances.

Vous pouvez envisager d’utiliser [Azure SQL Database Query Performance Insight](sql-database-query-performance.md).

## <a name="unusual-wait-statistic"></a>Statistiques d’attente inhabituelles

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances détectables indique une dégradation des performances de la charge de travail où les requêtes peu performantes sont identifiées par rapport à la base de référence de la charge de travail des 7 jours précédents.

Dans ce cas, le système n’a pas pu classer les requêtes peu performantes dans une autre catégorie de performances détectables standard, mais il a détecté des statistiques d’attente responsables de la régression et les considère donc comme des requêtes avec des &#8220;*statistiques d’attente inhabituelles*&#8221;. Il expose également ces statistiques d’attente inhabituelles responsables de la régression. 

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère des informations détaillées sur les temps d’attente inhabituels, les hachages des requêtes concernées et les temps d’attente.

Étant donné que le système n’a pas pu correctement identifier la cause racine des requêtes peu performantes, les informations de diagnostic sur ces dernières peuvent servir de bon point de départ à une résolution manuelle du problème. Vous pouvez envisager d’optimiser les performances de ces requêtes. Il est généralement conseillé de ne pas extraire les données que vous n’utilisez pas et de simplifier et décomposer les requêtes complexes en requêtes plus petites. Pour plus d’informations sur l’optimisation des performances des requêtes, consultez [Paramétrage des requêtes](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Contention de TempDB

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances détectables indique une condition des performances de base de données où il existe un goulot d’étranglement des threads qui essaient d’accéder aux ressources TempDB (cette condition n’est pas liée aux E/S). Le scénario classique de ce problème de performances implique des centaines de requêtes simultanées qui créent, utilisent, puis suppriment toutes des petites tables TempDB, à savoir les tables TempDB. Le système a détecté que le nombre de requêtes simultanées qui utilisent les mêmes tables TempDB a augmenté et présente une pertinence statistique au point d’affecter les performances de la base de données par rapport à la base de référence des performances des 7 jours précédents.

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère des détails sur la contention de TempDB qui peuvent être utilisés comme point de départ à la résolution des problèmes de performances. Il existe quelques actions à envisager pour limiter ce genre de contention et augmenter le débit de la charge de travail globale. Vous pouvez arrêter d’utiliser les tables temporaires. Vous pouvez également envisager d’utiliser des tables à mémoire optimisée. Consultez [Présentation des tables à mémoire optimisée](https://docs.microsoft.com/en-us/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Pénurie de DTU du pool élastique

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances détectables indique une dégradation dans les performances de la charge de travail de la base de données actuelle par rapport à la base de référence des 7 jours précédents en raison du manque de DTU disponibles dans le pool élastique de votre abonnement. 

Les ressources sur Azure SQL Database sont généralement appelées [ressources DTU](sql-database-what-is-a-dtu.md) et consistent en une mesure fusionnée des ressources du processeur et des E/S (E/S des journaux de données et des transactions). Les [ressources du pool élastique Azure](sql-database-elastic-pool.md) sont utilisées comme pool de ressources eDTU disponibles partagées entre plusieurs bases de données à des fins de mise à l’échelle. Quand les ressources eDTU disponibles dans le pool élastique ne sont pas suffisamment grandes pour prendre en charge toutes les bases de données du pool, un problème de performances lié à une pénurie de DTU dans le pool élastique est détecté par le système.

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère des informations sur le pool élastique, répertorie les bases de données qui consomment le plus de DTU et fournit un pourcentage des DTU du pool utilisées par la base de données la plus consommatrice.

Comme cette condition de performances est liée au fait que plusieurs bases de données utilisent le même pool d’eDTU dans le pool élastique, les étapes de résolution du problème doivent se concentrer sur les bases de données qui consomment le plus de DTU. Vous pouvez envisager de réduire la charge de travail sur les bases de données les plus consommatrices, notamment en optimisant les requêtes les plus consommatrices sur ces bases de données. Vous pouvez aussi vérifier que vous n’interrogez pas des données que vous n’utilisez pas. Une autre approche consiste à optimiser les applications qui utilisent les bases de données les plus consommatrices et à redistribuer la charge de travail entre plusieurs bases de données.

Si la réduction et l’optimisation de la charge de travail actuelle sur vos bases de données les plus consommatrices de DTU n’est pas possible, envisagez d’augmenter le niveau tarifaire de votre pool élastique. Une telle augmentation entraîne celle des DTU disponibles dans le pool élastique.

## <a name="plan-regression"></a>Régression de plan

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances détectables indique une condition dans laquelle Azure SQL Database démarre en utilisant un plan d’exécution des requêtes sous-optimal. Ce plan sous-optimal entraîne généralement une exécution accrue des requêtes responsable de temps d’attente plus longs pour les requêtes en cours et les autres requêtes.

Azure SQL Database détermine le plan d’exécution des requêtes qui coûte le moins à l’exécution des requêtes. Toutefois, au fur et à mesure que le type des requêtes et des charges de travail change, les plans existants ne sont parfois plus efficaces ou Azure SQL Database peut ne pas avoir effectué une évaluation correcte. À titre de correction, vous pouvez forcer manuellement les plans d’exécution des requêtes.

Ce modèle de performances détectables combine trois cas différents de régression de plan : régression de nouveau plan, régression de plan antérieur et modification de la charge de travail par des plans existants. Le type précis de régression de plan qui a eu lieu est indiqué dans la propriété &#8220;*détails*&#8221; du journal de diagnostic.

La condition de régression de nouveau plan fait référence à un état dans lequel Azure SQL Database commence à exécuter un nouveau plan d’exécution des requêtes qui n’est pas aussi efficace que le plan antérieur. La condition de régression de plan antérieur fait référence à un état dans lequel Azure SQL Database passe de l’utilisation d’un nouveau plan plus efficace à celle du plan antérieur qui n’est pas aussi efficace que le nouveau plan. La modification de la charge de travail par des plans existants fait référence à l’état dans lequel deux plans, le plan antérieur et le nouveau plan, ne cessent d’alterner, avec une tendance à choisir le plan le moins performant.

Pour plus d’informations sur les régressions de plan, consultez [What is plan regression in SQL server](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/) (Définition de la régression de plan dans SQL Server). 

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère les hachages de requête, l’ID du plan adéquat, l’ID du plan inadéquat et les ID de requête qui peuvent servir de base à la résolution des problèmes de performances liés à cette condition.

Vous pouvez déterminer quel plan est le plus performant pour vos requêtes spécifiques en les identifiant à l’aide des hachages de requête fournis. Une fois que vous avez déterminé quel plan fonctionne le mieux pour vos requêtes, vous pouvez le forcer manuellement. Pour plus d’informations, consultez [Comment SQL Server prévient les régressions de plan](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Saviez-vous que l’intelligence intégrée Azure est capable de gérer automatiquement les plans d’exécution des requêtes les plus performants de vos bases de données ?
>
> Pour optimiser en continu les performances d’Azure SQL Database, il est recommandé d’activer le [paramétrage automatique d’Azure SQL Database](sql-database-automatic-tuning.md). Il s’agit d’une fonctionnalité unique de l’intelligence intégrée Azure SQL qui surveille en permanence votre base de données SQL Azure, règle et crée automatiquement les plans d’exécution des requêtes les plus performants pour vos bases de données.
>

## <a name="database-scoped-configuration-value-change"></a>Modification de la valeur de configuration à l’échelle de la base de données

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances détectables indique une condition dans laquelle une modification de la configuration a été effectuée à l’échelle de la base de données et entraîne une régression des performances par rapport au comportement de la charge de travail de la base de données pendant les 7 jours précédents. Cette condition indique que les modifications récentes apportées à la configuration à l’échelle de la base de données ne semblent pas être bénéfiques pour les performances de votre base de données.

Il est possible d’apporter une modification de configuration à l’échelle de la base de données pour chaque base de données individuelle. Cette configuration est utilisée au cas par cas pour optimiser les performances individuelles de votre base de données. Les options suivantes peuvent être configurées pour chaque base de données individuelle : MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES et CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Résolution des problèmes

Le journal de diagnostic génère les modifications de configuration à l’échelle de la base de données qui ont été récemment apportées et qui ont provoqué la dégradation des performances par rapport au comportement de la charge de travail pendant les 7 jours précédents. Vous pouvez revenir aux valeurs précédentes de la configuration ou éventuellement effectuer un paramétrage valeur par valeur jusqu’à obtention du niveau de performance souhaité. Vous pouvez également envisager de copier les valeurs de configuration à l’échelle de la base de données d’une base de données similaire dont les performances sont satisfaisantes. Si vous n’arrivez pas à résoudre le problème de performances, envisagez de rétablir les valeurs par défaut d’Azure SQL Database et essayez de les paramétrer à partir de cette base de référence.

Pour plus d’informations sur l’optimisation de la configuration à l’échelle de la base de données et la syntaxe T-SQL utilisée pour modifier la configuration, consultez [Alter database scoped configuration (Transact-SQL)](https://msdn.microsoft.com/en-us/library/mt629158.aspx).

## <a name="slow-client"></a>Client lent

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances détectables indique une condition dans laquelle le client qui utilise Azure SQL Database n’est pas en mesure de consommer la sortie de la base de données assez rapidement par rapport à la base de données qui envoie les résultats. Comme Azure SQL Database ne place pas les résultats des requêtes exécutées dans une mémoire tampon, il se produit un ralentissement et une attente que le client consomme les résultats de requête transmis avant de continuer. Cette condition peut également être liée à un réseau lent qui ne parvient pas à transmettre suffisamment rapidement les résultats depuis la base de données SQL Azure vers le client consommateur.

Cette condition est générée uniquement s’il existe une régression des performances détectée par rapport au comportement de la charge de travail de la base de données pendant les 7 jours précédents. Ainsi, ce problème de performances est uniquement détecté s’il existe une dégradation des performances statistiquement significative par rapport au comportement précédent des performances.

### <a name="troubleshooting"></a>Résolution des problèmes

Ce modèle de performances détectables indique une condition côté client qui exige d’être résolue au niveau de l’application ou du réseau côté client. Le journal de diagnostic génère les hachages de requête et les temps d’attente les plus longs quant à la consommation par le client, pendant les deux dernières heures. Vous pouvez utiliser ces informations comme base pour la résolution du problème.

Vous pouvez envisager d’optimiser les performances de votre application liées à la consommation de ces requêtes. Vous pouvez aussi rechercher d’éventuels problèmes de latence du réseau. Puisque le problème de dégradation des performances se base sur une modification de la base de référence des performances des 7 derniers jours, vous pouvez rechercher d’éventuelles modifications apportées aux applications ou au réseau pendant cette période récente qui pourraient avoir entraîné cet événement de régression des performances. 

## <a name="pricing-tier-downgrade"></a>Rétrogradation à un niveau tarifaire inférieur

### <a name="what-is-happening"></a>Ce qui se passe

Ce modèle de performances détectables indique une condition dans laquelle le niveau tarifaire de votre base de données Azure SQL Database est rétrogradé à un niveau inférieur. En raison de la réduction des ressources (DTU) disponibles pour la base de données, le système a détecté une baisse des performances de la base de données actuelle par rapport à la base de référence des 7 jours précédents.

De plus, il existe peut-être une condition dans laquelle le niveau tarifaire de votre abonnement Azure SQL Database a été rétrogradé, puis repassé à un niveau supérieur sur une courte période. Cette condition indique une détection d’une dégradation des performances temporaire générée dans la section des détails du journal de diagnostics en tant que rétrogradation et augmentation du niveau tarifaire.

### <a name="troubleshooting"></a>Résolution des problèmes

Si vous avez réduit votre niveau tarifaire, et par conséquent les DTU disponibles pour Azure SQL Database, et que vous êtes satisfait des performances, vous n’avez rien à faire pour l’instant. Si, après la réduction de votre niveau tarifaire, vous n’êtes pas satisfait des performances d’Azure SQL Database, vous pouvez réduire les charges de travail de votre base de données ou augmenter votre niveau tarifaire.

## <a name="recommended-troubleshooting-flow"></a>Flux de résolution des problèmes recommandé

Suivez l’organigramme ci-dessous pour découvrir l’approche recommandée pour la résolution des problèmes de performances à l’aide d’Intelligent Insights.

Accédez à Intelligent Insights par le biais du portail Azure en accédant à Azure SQL Analytics. Essayez de localiser l’alerte de performances entrante et cliquez dessus. Identifiez ce qui se passe dans la page des détections. Examinez l’analyse fournie de la cause première du problème, le texte de la requête, les tendances des temps de requête et l’évolution de l’incident. En utilisant la recommandation donnée par Intelligent Insights pour atténuer le problème de performances, essayez de le résoudre. 

[![Organigramme de résolution des problèmes](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Cliquez sur l’organigramme pour en télécharger une version PDF.

Intelligent Insights a généralement besoin d’une (1) heure pour effectuer l’analyse de la cause première du problème de performances. Si vous ne parvenez pas à localiser votre problème dans Intelligent Insights (dans la plupart des cas, il s’agit de problèmes qui ont moins d’1 heure) et si ce problème est critique pour vous, utilisez le magasin de données des requêtes (QDS) pour identifier manuellement la cause première du problème de performances. Pour plus d’informations, consultez [Analyse des performances à l’aide du magasin de requêtes](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Étapes suivantes
- Découvrir les concepts [Intelligent Insights](sql-database-intelligent-insights.md)
- Utiliser le [journal de diagnostic des performances d’Intelligent Insights dans Azure SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md)
- Surveiller [Azure SQL Database avec Azure SQL Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)
- Apprendre à [collecter et utiliser les données de journaux de vos ressources Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

