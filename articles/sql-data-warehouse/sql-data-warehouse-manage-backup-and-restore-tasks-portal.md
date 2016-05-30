<properties
   pageTitle="Sauvegarde et restauration dans Azure SQL Data Warehouse (portail) | Microsoft Azure"
   description="Tâches du portail Azure permettant de restaurer une base de données active, supprimée ou inaccessible dans Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Sauvegarde et restauration d’une base de données dans Azure SQL Data Warehouse (portail)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [Portail](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

Comment sauvegarder et restaurer une base de données dans SQL Data Warehouse à l’aide du portail Azure.

Tâches abordées dans cette rubrique :

- restauration d’une base de données active ;
- restauration d’une base de données supprimée.
- restauration d’une base de données inaccessible à partir d’une autre région géographique Azure.

[AZURE.INCLUDE [Stratégie de rétention des sauvegardes SQL Data Warehouse](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Avant de commencer

Vérifiez la capacité DTU de votre base de données SQL. SQL Data Warehouse effectue les restaurations vers une base de données résidant sur votre serveur SQL logique ; il est donc important de veiller à ce que le serveur SQL vers lequel vous exécutez la restauration dispose d’une capacité DTU suffisante pour prendre en charge la nouvelle base de données. Consultez ce blog pour plus d’informations sur [l’affichage et l’augmentation du quota DTU][].

## restauration d’une base de données active ;

Pour restaurer une base de données à partir d’un instantané :

1. Connectez-vous au [portail Azure][].
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Bases de données SQL**
3. Accédez à votre base de données et sélectionnez-la.
4. En haut du panneau de la base de données, cliquez sur **Restaurer**.
5. Spécifiez un nouveau **nom de base de données**, sélectionnez un **point de restauration**, puis cliquez sur **Créer**.
6. Le processus de restauration de base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**.


## restauration d’une base de données supprimée.

Pour restaurer une base de données supprimée :

1. Connectez-vous au [portail Azure][].
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Serveur SQL Server**.
3. Accédez à votre serveur et sélectionnez-le.
4. Dans le panneau du serveur, faites défiler jusqu’à Opérations, puis cliquez sur la vignette **Bases de données supprimées**.
5. Sélectionnez la base de données supprimée que vous souhaitez restaurer.
5. Spécifiez un nouveau **nom pour la base de données** et cliquez sur **Créer**.
6. Le processus de restauration de base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**.


## Géo-restaurer un entrepôt de données à partir d’une sauvegarde géo-redondante

Pour effectuer une restauration géographique :

1. Connectez-vous au [portail Azure][].
2. Sur le côté gauche de l’écran, sélectionnez **+NOUVEAU**, puis **Données et stockage** et **SQL Data Warehouse**.
3. Sélectionnez **SAUVEGARDE** comme source, puis sélectionnez la sauvegarde géo-redondante à partir de laquelle vous souhaitez effectuer la récupération.
4. Spécifiez le reste des propriétés de base de données, puis cliquez sur **Créer**
5. Le processus de restauration de base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**

## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la continuité des activités de la base de données SQL Azure]: sql-database-business-continuity.md
[Finalize a recovered database]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->

<!--Blog references-->
[l’affichage et l’augmentation du quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[portail Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->