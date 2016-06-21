<properties
   pageTitle="Restauration d’une base de données dans Azure SQL Data Warehouse (portail Azure) | Microsoft Azure"
   description="Tâches API REST permettant de restaurer une base de données active, supprimée ou inaccessible dans Azure SQL Data Warehouse."
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
   ms.date="06/01/2016"
   ms.author="elfish;barbkess;sonyama;kevin"/>

# Sauvegarde et restauration d’une base de données dans Azure SQL Data Warehouse (API REST)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-overview-manage-database-restore.md)
- [Portail](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

Tâches API REST permettant de restaurer une base de données dans SQL Data Warehouse.

Tâches abordées dans cette rubrique :

- restauration d’une base de données active ;
- restauration d’une base de données supprimée.

## Avant de commencer

**Vérifiez la capacité DTU de votre base de données SQL.** SQL Data Warehouse effectue les restaurations vers une base de données résidant sur votre serveur SQL logique ; il est donc important de veiller à ce que le serveur SQL vers lequel vous exécutez la restauration dispose d’une capacité DTU suffisante pour prendre en charge la nouvelle base de données. Consultez ce blog pour plus d’informations sur [l’affichage et l’augmentation du quota DTU][].

## restauration d’une base de données active ;

Pour restaurer une base de données :

1. Suivez la procédure d’obtention de la liste des points de restauration de la base de données.
2. Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données][].
3. Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données][].

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer la base de données récupérée en suivant le guide [Finaliser une base de données récupérée][].

## restauration d’une base de données supprimée.

Pour restaurer une base de données supprimée :

1.	Obtenez la liste de toutes vos bases de données supprimées pouvant être restaurées en suivant la procédure [Liste des bases de données supprimées pouvant être restaurées][].
2.	Obtenez des informations détaillées sur la base de données que vous voulez restaurer en suivant la procédure [Obtention de base de données supprimée pouvant être restaurée][].
3.	Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données][].
4.	Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données][].

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer la base de données récupérée en suivant le guide [Finaliser une base de données récupérée][].


## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, voir [Vue d’ensemble de la continuité d’activité de la base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la continuité d’activité de la base de données SQL Azure]: sql-database-business-continuity.md
[Finaliser une base de données récupérée]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Création d’une demande de restauration de base de données]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Statut d’opération de base de données]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obtention de base de données supprimée pouvant être restaurée]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Liste des bases de données supprimées pouvant être restaurées]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[l’affichage et l’augmentation du quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0608_2016-->