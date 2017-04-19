---
title: "Portail Azure : géoréplication SQL Database | Microsoft Docs"
description: "Configurer la géoréplication pour Azure SQL Database dans le portail Azure et initier le basculement"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d0b29822-714f-4633-a5ab-fb1a09d43ced
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/062/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 78dbbaa272d9889add5bc833c7557618f41056db
ms.lasthandoff: 04/15/2017


---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Configurer la géoréplication active pour Azure SQL Database dans le portail Azure et initier le basculement

Cet article montre comment configurer la géoréplication active pour SQL Database dans le [portail Azure](http://portal.azure.com) et initier le basculement.

Pour lancer un basculement avec le portail Azure, consultez [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec le portail Azure](sql-database-geo-replication-portal.md).

> [!NOTE]
> La géo-réplication active (bases de données secondaires accessibles en lecture) est désormais disponible pour toutes les bases de données de tous les niveaux de service. En avril 2017 sera retiré le type secondaire non accessible en lecture et les bases de données non accessibles en lecture deviendront automatiquement des bases de données secondaires accessibles en lecture.
> 
> 

Pour configurer la géo-réplication active à l’aide du portail Azure, vous devez disposer des ressources suivantes :

* Une Azure SQL Database : la base de données primaire que vous souhaitez répliquer vers une autre région géographique.

> [!Note]
La géo-réplication active doit être entre des bases de données au sein du même abonnement.

## <a name="add-a-secondary-database"></a>Ajout d'une base de données secondaire
Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat géo-réplication.  

Pour ajouter une base de données secondaire, vous devez être le propriétaire ou copropriétaire de l’abonnement.

La base de données secondaire a le même nom que la base de données primaire et, par défaut, le même niveau de service. La base de données secondaire peut être une base de données autonome ou une base de données dans un pool élastique. Pour en savoir plus, consultez [Niveaux de service](sql-database-service-tiers.md).
Une fois la base de données secondaire créée et amorcée, une réplication des données de la base de données primaire vers la base de données secondaire commence.

> [!NOTE]
> Si la base de données partenaire existe déjà (par exemple, suite à l’arrêt d’une relation de géo-réplication précédente), la commande échoue.
> 

1. Dans le [portail Azure](http://portal.azure.com), accédez à la base de données que vous souhaitez configurer pour la géo-réplication.
2. Dans la page Base de données SQL, sélectionnez **Géoréplication**, puis la région dans laquelle créer la base de données secondaire. Bien que vous puissiez sélectionner n’importe quelle région autre que la région qui héberge la base de données primaire, nous vous recommandons de sélectionner la [région jumelée](../best-practices-availability-paired-regions.md).
   
    ![Configuration de la géo-réplication](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Sélectionnez ou configurez le serveur et le niveau tarifaire pour la base de données secondaire.
   
    ![Configuration de la base de données secondaire](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Si vous le souhaitez, vous pouvez ajouter une base de données secondaire à un pool données élastique. Pour créer la base de données secondaire dans un pool, cliquez sur **Pool élastique**, puis sélectionnez un pool sur le serveur cible. Un pool doit déjà exister sur le serveur cible. Ce workflow ne crée pas un pool.
5. Cliquez sur **créer** pour ajouter la base de données secondaire.
6. La base de données secondaire est créée et le processus d’amorçage commence.
   
    ![Configuration de la base de données secondaire](./media/sql-database-geo-replication-portal/seeding0.png)
7. Lorsque le processus d’amorçage est terminé, la base de données secondaire affiche son état.
   
    ![Amorçage terminé](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Initialisation d’un basculement

La base de données secondaire peut être basculée en base de données primaire.  

1. Dans le [portail Azure](http://portal.azure.com) , accédez à la base de données primaire dans le partenariat de géoréplication.
2. Dans le panneau de la base de données SQL, sélectionnez **Tous les paramètres** > **Géoréplication**.
3. Dans la liste **SECONDAIRES**, sélectionnez la base de données qui doit devenir la nouvelle base primaire et cliquez sur **Basculement**.
   
    ![Basculement](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Cliquez sur **Oui** pour commencer le basculement.

La commande fait immédiatement basculer la base de données secondaire vers le rôle primaire. 

Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (de l’ordre de 0 à 25 secondes) pendant que les rôles sont activés. Si la base de données primaire comporte plusieurs bases de données secondaires, la commande reconfigure automatiquement les autres bases de données secondaires pour qu’elles se connectent à la nouvelle base de données primaire. Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales. 

> [!NOTE]
> Si la base de données primaire est en ligne et valide des transactions lorsque la commande est émise, une perte de données peut se produire.
> 
> 

## <a name="remove-secondary-database"></a>Supprimer une base de données secondaire
Cette opération arrête définitivement la réplication vers la base de données secondaire et modifie le rôle de la base de données secondaire en une base de données normale accessible en lecture et en écriture. Si la connectivité à la base de données secondaire est interrompue, la commande aboutit, mais la base de données secondaire ne passe pas en mode lecture-écriture une fois la connectivité rétablie.  

1. Dans le [portail Azure](http://portal.azure.com), accédez à la base de données primaire dans le partenariat de géo-réplication.
2. Dans la page de la SQL Database, sélectionnez **Géo-réplication**.
3. Dans la liste des bases de données **SECONDAIRES**, sélectionnez la base de données que vous souhaitez supprimer du partenariat de géo-réplication.
4. Cliquez sur **Arrêter la réplication**.
   
    ![Suppression de la base de données secondaire](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Une fenêtre de confirmation s’ouvre. Cliquez sur **Oui** pour supprimer la base de données de partenariat de géo-réplication (définissez-la sur une base de données en lecture-écriture ne faisant pas partie d’une réplication).

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la géo-réplication active, consultez [Géo-réplication active](sql-database-geo-replication-overview.md).
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).


