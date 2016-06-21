<properties
   pageTitle="Restaurer une base de données dans Azure SQL Data Warehouse (API REST) | Microsoft Azure"
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
   ms.date="06/04/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Restaurer une base de données dans Azure SQL Data Warehouse (API REST)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-restore-database-overview.md)
- [Portail](sql-data-warehouse-restore-database-portal.md)
- [PowerShell](sql-data-warehouse-restore-database-powershell.md)
- [REST](sql-data-warehouse-manage-restore-database-rest-api.md)

Tâches API REST permettant de restaurer une base de données active, supprimée ou inaccessible dans Azure SQL Data Warehouse.

Tâches abordées dans cette rubrique :

- restauration d’une base de données active ;
- restauration d’une base de données supprimée.
- restauration d’une base de données inaccessible à partir d’une autre région géographique Azure.

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

## Restauration à partir d’une région géographique Azure

Pour effectuer une restauration géographique :

1. Obtenez la liste des bases de données récupérables à l’aide de l’opération [List Recoverable Databases][].
2. Obtenez la base de données à récupérer à l’aide de l’opération [Get Recoverable Database][].
3. Créez la requête de récupération à l’aide de l’opération [Create Database Recovery Request][].
4. Suivez l’état de la récupération à l’aide de l’opération [Database Operation Status][].

### Configurer votre base de données après avoir effectué une géo-restauration
Voici une liste de contrôle pour vous aider à préparer votre base de données de production récupérée.

1. **Mettre à jour les chaînes de connexion** : vérifiez que les chaînes de connexion de vos outils clients pointent vers la base de données qui vient d’être récupérée.
2. **Modifier les règles de pare-feu** : vérifiez les règles de pare-feu sur le serveur cible. Assurez-vous également que les connexions entre les ordinateurs clients ou Azure et le serveur et la base de données qui vient d’être récupérée sont activées.
3. **Vérifier les connexions de serveur et les utilisateurs des bases de données** : vérifiez que toutes les connexions utilisées par votre application existent sur le serveur qui héberge votre base de données récupérée. Créez à nouveau les connexions manquantes et accordez-leur les autorisations appropriées sur la base de données restaurée. 
4. **Activer l’audit** : si la fonction d’audit est nécessaire pour accéder à votre base de données, vous devez l’activer après la récupération de la base de données.

La base de données récupérée sera compatible avec le chiffrement transparent des données si la base de données source l’est aussi.


## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la continuité des activités de la base de données SQL Azure]: sql-database-business-continuity.md
[Finaliser une base de données récupérée]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Création d’une demande de restauration de base de données]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
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