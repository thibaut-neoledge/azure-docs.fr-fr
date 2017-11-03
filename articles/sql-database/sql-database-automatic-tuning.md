---
title: "Azure SQL Database - Réglage automatique | Microsoft Docs"
description: "Azure SQL Database analyse la requête SQL et s’adapte automatiquement aux charges de travail utilisateur."
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
ms.workload: On Demand
ms.date: 09/19/2017
ms.author: jovanpop
ms.openlocfilehash: 10f8cca8e519b28f0fe29605419b860f73e04a87
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Réglage automatique dans Azure SQL Database

Azure SQL Database est un service de données entièrement géré qui surveille les requêtes qui sont exécutées sur la base de données et qui améliore automatiquement les performances de la charge de travail. Azure SQL Database dispose d’un mécanisme d’intelligence intégré de [réglage automatique](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) qui peut automatiquement régler et améliorer les performances de vos requêtes en adaptant de manière dynamique la base de données à votre charge de travail. Le réglage automatique dans Azure SQL Database est peut-être l’une des fonctionnalités les plus importantes que vous pouvez activer dans Azure SQL Database en vue d’optimiser les performances de vos requêtes.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="automatic-tuning-options"></a>Options de réglage automatique

Les options de [réglage automatique](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) disponibles dans Azure SQL Database sont les suivantes :
 1. **CREATE INDEX** identifie les index qui peuvent améliorer les performances de votre charge de travail, crée les index et vérifie qu’ils améliorent les performances des requêtes.
 2. **DROP INDEX** identifie les index en double et redondants, ainsi que ceux qui n’ont pas été utilisés depuis longtemps.
 3. **PLAN REGRESSION CORRECTION** identifie les requêtes SQL utilisant le plan d’exécution qui sont plus lentes que le plan précédent, et utilise le dernier plan correct connu au lieu du plan de régression.

Azure SQL Database identifie des recommandations **CREATE INDEX**, **DROP INDEX** et **PLAN REGRESSION CORRECTION** qui peuvent optimiser votre base de données et les affiche dans le portail Azure. Pour plus d’informations sur l’identification des index à modifier, voir [Rechercher des recommandations d’index dans le portail Azure](sql-database-advisor-portal.md). Vous pouvez appliquer manuellement les recommandations à l’aide du portail ou vous pouvez laisser Azure SQL Database appliquer automatiquement les recommandations, surveiller la charge de travail après le changement et vérifier que la recommandation améliore les performances de votre charge de travail.

Vous pouvez activer ou désactiver les options de réglage automatique par base de données ou vous pouvez les configurer sur le serveur logique et les appliquer sur chaque base de données qui hérite des paramètres du serveur. La méthode recommandée consiste à configurer les options de [réglage automatique](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) sur le serveur et à hériter les paramètres sur les bases de données. Cette méthode simplifie la gestion des options de réglage automatique sur un grand nombre de bases de données.

Consultez cet article pour découvrir comment [activer le réglage automatique](sql-database-automatic-tuning-enable.md) via le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

- Pour activer le réglage automatique dans Azure SQL Database et permettre à cette fonction de gérer entièrement votre charge de travail, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).
- Pour utiliser le réglage manuel, vous pouvez consulter les [recommandations de réglage dans le portail Azure](sql-database-advisor-portal.md) et appliquer manuellement les recommandations qui amélioreront les performances de vos requêtes.
- Découvrez plus en détail l’intelligence intégrée qui règle [Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Découvrez plus en détail le [réglage automatique](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) dans Azure SQL Database et SQL Server 2017.
