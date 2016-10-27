<properties
   pageTitle="Restauration d’un Azure SQL Data Warehouse (API REST) | Microsoft Azure"
   description="Tâches d’API REST permettant de restaurer un Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>


# <a name="restore-an-azure-sql-data-warehouse-(rest-api)"></a>Restauration d’un Azure SQL Data Warehouse (API REST)

> [AZURE.SELECTOR]
- [Vue d'ensemble][]
- [Portail][]
- [PowerShell][]
- [REST][]

Dans cet article, vous allez apprendre à restaurer un Azure SQL Data Warehouse à l’aide de l’API REST.

## <a name="before-you-begin"></a>Avant de commencer

**Vérifiez votre capacité de DTU.**  Chaque SQL Data Warehouse est hébergé par un serveur SQL (par exemple, myserver.database.windows.net) qui dispose d’un quota DTU par défaut.  Avant de pouvoir restaurer un SQL Data Warehouse, vérifiez que le quota DTU restant sur le serveur SQL est suffisant pour la base de données en cours de restauration. Pour savoir comment calculer la capacité DTU nécessaire ou pour demander davantage de capacité DTU, consultez [Request a DTU quota change][](Demander une modification du quota DTU).

## <a name="restore-an-active-or-paused-database"></a>Restauration d’une base de données active ou en pause

Pour restaurer une base de données :

1. Suivez la procédure d’obtention de la liste des points de restauration de la base de données.
2. Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données][] .
3. Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données][] .

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer votre base de données restaurée en suivant les instructions de la section [Configurer votre base de données après récupération][].

## <a name="restore-a-deleted-database"></a>restauration d’une base de données supprimée.

Pour restaurer une base de données supprimée :

1.  Obtenez la liste de toutes vos bases de données supprimées pouvant être restaurées en suivant la procédure [Liste des bases de données supprimées pouvant être restaurées][] .
2.  Obtenez des informations détaillées sur la base de données que vous voulez restaurer en suivant la procédure [Obtention de base de données supprimée pouvant être restaurée][] .
3.  Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données][] .
4.  Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données][] .

>[AZURE.NOTE] Pour configurer votre base de données une fois la restauration terminée, consultez la page [Configurer votre base de données après récupération][]. 


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la continuité des activités de la base de données SQL Azure]: ./sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurer votre base de données après récupération]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Installation et configuration d’Azure PowerShell]: ./powershell-install-configure.md
[Vue d'ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Création d’une demande de restauration de base de données]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Statut d’opération de base de données]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obtention de base de données supprimée pouvant être restaurée]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Liste des bases de données supprimées pouvant être restaurées]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[portail Azure]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps



<!--HONumber=Oct16_HO2-->


