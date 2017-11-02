---
title: "Activer le réglage automatique pour Azure SQL Database | Microsoft Docs"
description: "Vous pouvez facilement activer le réglage automatique sur Azure SQL Database."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/19/2016
ms.author: veljko-msft
ms.openlocfilehash: 82db5996c1ba1f224593e4eaa5b3b0067755db49
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
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

Pour activer le réglage automatique sur une seule base de données, accédez à la base de données dans le portail Azure, puis sélectionnez **Réglage automatique**. Vous pouvez configurer une seule base de données de façon à ce qu’elle hérite des paramètres du serveur en sélectionnant l’option, ou vous pouvez spécifier individuellement la configuration d’une base de données.

![Base de données](./media/sql-database-automatic-tuning-enable/database.png)

Une fois que vous avez sélectionné la configuration appropriée, cliquez sur **Appliquer**.

## <a name="next-steps"></a>Étapes suivantes
* Lisez l’[article Réglage automatique](sql-database-automatic-tuning.md) pour en savoir plus sur le réglage automatique et sur la manière dont il peut vous aider à améliorer vos performances.
* Consultez [Recommandations en matière de performances](sql-database-advisor.md) pour obtenir une vue d’ensemble des recommandations relatives aux performances Azure SQL Database.
* Pour connaître l’impact de vos principales requêtes sur les performances, consultez [Query Performance Insights](sql-database-query-performance.md).
