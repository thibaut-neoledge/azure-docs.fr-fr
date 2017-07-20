---
title: "SQL Database - Réglage automatique | Microsoft Docs"
description: "SQL Database analyse la requête SQL et s’adapte automatiquement aux charges de travail utilisateur."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: jovanpop
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f5552793db2d89542782b7d9e8f996314f62e429
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017


---
# <a name="automatic-tuning"></a>Réglage automatique

Azure SQL Database est un service de données entièrement géré qui surveille les requêtes qui sont exécutées sur la base de données et qui peut automatiquement améliorer les performances de la charge de travail. Azure SQL Database dispose d’un mécanisme d’intelligence intégré qui peut automatiquement régler et améliorer les performances de vos requêtes en adaptant de manière dynamique la base de données à votre charge de travail. Le réglage automatique dans Azure SQL Database est peut-être l’une des fonctionnalités les plus importantes que vous pouvez activer dans Azure SQL Database en vue d’optimiser les performances de vos requêtes.

Consultez cet article pour découvrir comment [activer le réglage automatique](sql-database-automatic-tuning-enable.md) via le portail Azure.

## <a name="why-automatic-tuning"></a>À quoi sert le réglage automatique ?

L’une des principales tâches d’administration classique d’une base de données consiste à surveiller la charge de travail, à identifier les requêtes SQL critiques, les index qui doivent être ajoutés pour améliorer les performances et les index rarement utilisés. Azure SQL Database fournit des informations détaillées sur les requêtes et les index que vous devez surveiller. Toutefois, la surveillance permanente de la base de données est une tâche difficile et fastidieuse, en particulier lors du traitement de plusieurs bases de données. La gestion efficace d’un très grand nombre de bases de données peut s’avérer impossible même avec tous les rapports et outils disponibles avec Azure SQL Database et le portail Azure. Au lieu de surveiller et de régler votre base de données manuellement, vous pouvez envisager de déléguer certaines actions de surveillance et de réglage à Azure SQL Database grâce à la fonctionnalité de réglage automatique. 


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="how-does-automatic-tuning-work"></a>Comment fonctionne le réglage automatique ?

Azure SQL Database intègre un processus de surveillance et d’analyse des performances en continu, qui en apprend sans cesse davantage sur les caractéristiques de votre charge de travail et identifie des problèmes et améliorations potentiels.

![Processus de réglage automatique](./media/sql-database-automatic-tuning/tuning-process.png)

Ce processus permet à Azure SQL Database de s’adapter de manière dynamique à votre charge de travail en recherchant les plans et les index qui peuvent améliorer les performances de vos charges de travail et les index qui affectent vos charges de travail. En fonction de ces résultats, le réglage automatique effectue des actions de réglage qui améliorent les performances de votre charge de travail. En outre, Azure SQL Database surveille en permanence les performances après toute modification apportée par le réglage automatique, pour s’assurer qu’elle améliore les performances de votre charge de travail. Toute action qui n’a pas amélioré les performances est automatiquement annulée. Ce processus de vérification est une fonctionnalité clé qui permet de s’assurer que les modifications apportées par le réglage automatique ne diminuent pas les performances de votre charge de travail.

Deux aspects de réglage automatique sont disponibles dans Azure SQL Database :

 -  La **gestion automatique des index**, qui identifie les index qui doivent être ajoutés à votre base de données et ceux qui doivent être supprimés.
 -  La **correction du plan automatique** (déployée prochainement, déjà disponible dans SQL Server 2017), qui identifie les plans problématiques et résout les problèmes de performances du plan SQL.

## <a name="automatic-index-management"></a>Gestion automatique des index

Dans Azure SQL Database, la gestion des index est facile, car Azure SQL Database s’informe sur votre charge de travail et s’assure que vos données sont toujours indexées de manière optimale. Pour assurer des performances optimales de votre charge de travail, l’index doit être correctement conçu. La gestion automatique des index peut vous aider à les optimiser. La gestion automatique des index permet de résoudre les problèmes de performances dans les bases de données indexées de manière incorrecte, ou de conserver et d’améliorer les index dans la structure de base de données existante. 

### <a name="why-do-you-need-index-management"></a>Pourquoi utiliser la gestion des index ?

Les index accélèrent certaines de vos requêtes qui lisent les données à partir des tables ; cependant, ils peuvent ralentir les requêtes qui mettent à jour les données. Vous devez analyser soigneusement quand créer un index et les colonnes que vous devez inclure dans l’index. Certains index peuvent ne plus être nécessaires après un certain temps. Par conséquent, vous devez régulièrement identifier et supprimer les index qui n’apportent aucun avantage. Si vous ignorez les index non utilisés, les performances des requêtes qui mettent à jour les données diminuent sans aucun avantage pour les requêtes qui lisent les données. Les index inutilisés affectent également les performances globales du système, car les mises à jour supplémentaires impliquent une journalisation inutile.

Pour trouver l’ensemble optimal d’index qui améliore les performances des requêtes qui lisent les données de vos tables et ont un impact minimal sur les mises à jour, vous aurez peut-être besoin d’une analyse continue complexe.

Azure SQL Database utilise l’intelligence intégrée et des règles avancées pour analyser vos requêtes, identifier les index qui seraient optimaux pour vos charges de travail actuelles et les index qui peuvent être supprimés. Azure SQL Database permet de s’assurer que vous disposez de l’ensemble d’index minimal nécessaire à l’optimisation des requêtes qui lisent les données, avec un impact réduit sur les autres requêtes.

### <a name="how-to-identify-indexes-that-need-to-be-changed-in-your-database"></a>Comment identifier les index qui doivent être modifiés dans votre base de données ?

Azure SQL Database facilite le processus de gestion des index. Plutôt que d’utiliser un système manuel fastidieux pour l’analyse de la charge de travail et la surveillance de l’index, Azure SQL Database analyse votre charge de travail, identifie les requêtes qui pourraient s’exécuter plus rapidement avec un nouvel index, et identifie les index non utilisés ou en double. Pour plus d’informations sur l’identification des index à modifier, voir [Rechercher des recommandations d’index dans le portail Azure](sql-database-advisor-portal.md).

### <a name="automatic-index-management-considerations"></a>Considérations sur la gestion automatique des index

Si vous constatez que les règles intégrées améliorent les performances de votre base de données, vous pouvez laisser Azure SQL Database gérer automatiquement vos index.

Les actions requises pour créer les index nécessaires dans Azure SQL Database peuvent consommer des ressources et affecter temporairement les performances en termes de charge de travail. Pour minimiser l’impact de la création d’index sur les performances en termes de charge de travail, Azure SQL Database recherche la période appropriée pour chaque opération de gestion des index. Le réglage est reporté à plus tard si la base de données a besoin de ressources pour exécuter votre charge de travail. Il démarre lorsque la base de données dispose de suffisamment de ressources inutilisées qui peuvent être exploitées pour la tâche de maintenance. Une fonctionnalité importante dans la gestion automatique des index est la vérification des actions. Lorsqu’Azure SQL Database crée ou supprime des index, un processus de surveillance analyse les performances de votre charge de travail pour vérifier que l’action les a améliorées. Si l’amélioration n’est pas significative, l’action est immédiatement annulée. De cette manière, Azure SQL Database s’assure que les actions automatiques ne nuisent pas aux performances de votre charge de travail. Les index créés par le réglage automatique sont transparents pour l’opération de maintenance sur le schéma sous-jacent. Les modifications du schéma, par exemple le fait de supprimer ou de renommer des colonnes, ne sont pas bloquées par la présence d’index créés automatiquement. Les index créés automatiquement par Azure SQL Database sont immédiatement supprimés lorsque la table ou les colonnes liées sont supprimées.

## <a name="automatic-plan-choice-correction"></a>Correction automatique du choix de plan

La correction automatique du plan est une nouvelle fonctionnalité de réglage automatique ajoutée dans SQL Server 2017 CTP2.0. Elle identifie les plans SQL qui ne se comportent pas de manière adéquate. Cette option de réglage automatique sera bientôt disponible sur Azure SQL Database. Pour en savoir plus, voir [Réglage automatique dans SQL Server 2017](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="next-steps"></a>Étapes suivantes

- Pour activer le réglage automatique dans Azure SQL Database et permettre à la fonction de réglage automatique de gérer entièrement votre charge de travail, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).
- Pour utiliser le réglage manuel, vous pouvez consulter les [recommandations de réglage dans le portail Azure](sql-database-advisor-portal.md) et appliquer manuellement les recommandations qui amélioreront les performances de vos requêtes.

