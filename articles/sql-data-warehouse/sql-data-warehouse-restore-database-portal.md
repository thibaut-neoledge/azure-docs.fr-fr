<properties
   pageTitle="Restauration d’un Azure SQL Data Warehouse (Portail) | Microsoft Azure"
   description="Tâches du portail Azure permettant de restaurer un Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/28/2016"
   ms.author="sonyama;barbkess;nicw"/>

# Restauration d’un Azure SQL Data Warehouse (Portail)

> [AZURE.SELECTOR]
- [Vue d'ensemble][]
- [Portail][]
- [PowerShell][]
- [REST][]

Dans cet article, vous allez apprendre à restaurer un Azure SQL Data Warehouse à l’aide du portail Azure.

## Avant de commencer

**Vérifiez votre capacité de DTU.** Chaque SQL Data Warehouse est hébergé par un serveur SQL logique. Ce serveur logique a une limite de capacité mesurée en DTU. Avant de pouvoir restaurer un SQL Data Warehouse, il est important de s’assurer que le serveur SQL logique qui héberge votre base de données a une capacité de DTU suffisante pour la base de données en cours de restauration. Consultez ce blog pour plus d’informations sur [l’affichage et l’augmentation du quota de DTU][].


## Restauration d’une base de données active ou en pause

Pour restaurer une base de données :

1. Connectez-vous au [portail Azure][].
2. Sur le côté gauche de l’écran, sélectionnez l’option **Parcourir**, puis choisissez **Serveurs SQL Server**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Accédez à votre serveur et sélectionnez-le.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Rechercher l’entrepôt SQL Data Warehouse à partir duquel effectuer la restauration et sélectionnez-le.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. En haut du panneau de l’entrepôt de données, cliquez sur **Restaurer**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Spécifiez un nouveau **nom pour la base de données**.
7. Sélectionnez le dernier **point de restauration**.
    1. Assurez-vous de sélectionner le dernier point de restauration. Étant donné que les points de restauration sont affichés au format UTC, parfois, l’option par défaut affichée n’est pas le dernier point de restauration.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Cliquez sur **OK**
9. Le processus de restauration de la base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**.

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer la base de données récupérée en suivant le guide [Finaliser une base de données récupérée][].


## restauration d’une base de données supprimée.

Pour restaurer une base de données supprimée :

1. Connectez-vous au [portail Azure][].
2. Sur le côté gauche de l’écran, sélectionnez l’option **Parcourir**, puis choisissez **Serveurs SQL Server**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Accédez à votre serveur et sélectionnez-le.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Faites défiler jusqu’à la section Opérations dans le panneau de votre serveur.
5. Cliquez sur la vignette **Bases de données supprimées**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Sélectionnez la base de données supprimée que vous souhaitez restaurer.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Spécifiez un nouveau **nom pour la base de données**.
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Cliquez sur **OK**
9. Le processus de restauration de la base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**.

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer la base de données récupérée en suivant le guide [Finaliser une base de données récupérée][].


## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la continuité des activités de la base de données SQL Azure]: ./sql-database-business-continuity.md
[Vue d'ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Finaliser une base de données récupérée]: ./sql-database-recovered-finalize.md

<!--MSDN references-->

<!--Blog references-->
[l’affichage et l’augmentation du quota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[portail Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0629_2016-->