---
title: "Activer le réglage automatique pour Azure SQL Database | Microsoft Docs"
description: "Vous pouvez facilement activer le réglage automatique sur Azure SQL Database."
services: sql-database
documentationcenter: 
author: vvasic
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 06/05/2016
ms.author: vvasic
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: b391b1f7aa37c5a06fc320ce892534187deb4959
ms.contentlocale: fr-fr
ms.lasthandoff: 06/13/2017

---
# <a name="enable-automatic-tuning"></a>Activer le réglage automatique

Azure SQL Database est un service de données géré automatiquement qui surveille vos requêtes en permanence et identifie les actions que vous pouvez effectuer pour améliorer les performances de votre charge de travail. Vous pouvez consulter les recommandations et les appliquer manuellement ou laisser Azure SQL Database appliquer automatiquement des actions correctives : il s’agit du **mode de réglage automatique**. Le réglage automatique peut être activé au niveau du serveur ou de la base de données.

## <a name="enable-automatic-tuning-on-server"></a>Activer le réglage automatique sur le serveur

Pour activer le réglage automatique sur le serveur Azure SQL Database, accédez au serveur dans le portail Azure, puis sélectionnez **Réglage automatique** dans le menu. Sélectionnez les options de réglage automatique que vous souhaitez activer et sélectionnez **Appliquer** :

![Serveur](./media/sql-database-automatic-tuning-enable/server.png)

Les options de réglage automatique sur le serveur sont appliquées à toutes les bases de données du serveur. Par défaut, toutes les bases de données héritent de la configuration de leur serveur parent, mais celle-ci peut être remplacée et spécifiée individuellement pour chaque base de données.

## <a name="configure-automatic-tuning-on-database"></a>Configurer le réglage automatique sur la base de données

Le portail Azure vous permet de spécifier individuellement la configuration de réglage automatique de chaque base de données.

> [!NOTE]
> Il est généralement recommandé de gérer la configuration du réglage automatique au niveau du serveur, afin que les mêmes paramètres de configuration soient appliqués automatiquement à chaque base de données. Configurez le réglage automatique au niveau d’une base de données si celle-ci est différente des autres sur le même serveur.
>

Pour activer le réglage automatique sur une seule base de données, accédez à la base de données dans le portail Azure, puis sélectionnez **Réglage automatique**. Vous pouvez configurer une seule base de données de façon à ce qu’elle hérite des paramètres de la base de données en cochant la case, ou vous pouvez spécifier individuellement la configuration d’une base de données.

![Base de données](./media/sql-database-automatic-tuning-enable/database.png)

Une fois que vous avez sélectionné la configuration appropriée, cliquez sur **Appliquer**.

## <a name="next-steps"></a>Étapes suivantes
* Lisez l’[article Réglage automatique](sql-database-automatic-tuning.md) pour en savoir plus sur le réglage automatique et sur la manière dont il peut vous aider à améliorer vos performances.
* Consultez [Recommandations en matière de performances](sql-database-advisor.md) pour obtenir une vue d’ensemble des recommandations relatives aux performances Azure SQL Database.
* Pour connaître l’impact de vos principales requêtes sur les performances, consultez [Query Performance Insights](sql-database-query-performance.md).

