<properties
	pageTitle="Activer Stretch Database pour une base de données | Microsoft Azure"
	description="Découvrez comment configurer une base de données pour Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Activer Stretch Database pour une base de données

Pour configurer une base de données Stretch Database, sélectionnez **Tâches | Stretch | Activer** pour une base de données dans SQL Server Management Studio pour ouvrir l’Assistant **Activer la base de données pour Stretch**. Vous pouvez également utiliser Transact-SQL pour activer Stretch Database pour une base de données.

Si vous sélectionnez **Tâches | Stretch | Activer** pour une table et que vous n’avez pas encore activé la base de données pour Stretch Database, l’Assistant configure la base de données pour Stretch Database et vous permet de configurer des tables dans le cadre du processus. Suivez les étapes décrites dans cette rubrique au lieu de celles décrites dans [Activer Stretch Database pour une table](sql-server-stretch-database-enable-database.md).

L’activation de Stretch Database sur une table ou une base de données nécessite des autorisations db\_owner. L’activation de Stretch Database sur une base de données nécessite également des autorisations CONTROL DATABASE.

## Avant de commencer

-   Avant de configurer une base de données pour Stretch, nous vous recommandons d’exécuter le Conseiller Stretch Database pour identifier les bases de données et les tables qui sont éligibles pour Stretch. Le Conseiller Stretch Database identifie également les problèmes de blocage. Pour plus d’informations, consultez [Identifier les bases de données et les tables pour Stretch Database](sql-server-stretch-database-identify-databases.md).

-   Consultez [Limites de Stretch Database](sql-server-stretch-database-limitations.md).

-   Stretch Database migre les données vers Azure. Vous devez donc avoir un compte Azure et un abonnement pour la facturation. Pour obtenir un compte Azure, [cliquez ici](http://azure.microsoft.com/pricing/free-trial/).

-   Recueillez les informations dont vous avez besoin pour créer une base de données distante ou sélectionner une base de données distante existante, et pour créer une règle de pare-feu qui autorise votre serveur local à communiquer avec le serveur distant.

## <a name="EnableTSQLServer"></a>Condition préalable : Autorisation pour activer Stretch Database sur le serveur
Avant de pouvoir activer Stretch Database sur une base de données ou une table, vous devez l’activer sur le serveur local. Cette opération nécessite des autorisations sysadmin ou serveradmin.

-   Si vous disposez des autorisations d’administration nécessaires, l’Assistant **Activer la base de données pour Stretch** configure le serveur pour Stretch.

-   Si vous n’avez pas les autorisations nécessaires, un administrateur doit activer l’option manuellement en exécutant **sp\_configure** avant que vous n’exécutiez l’Assistant, ou un administrateur doit exécuter l’Assistant.

Pour activer manuellement Stretch Database sur le serveur, exécutez **sp\_configure** et activez l’option **archive de données distante**. L’exemple suivant active l’option **archive de données distante** en lui affectant la valeur 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO
RECONFIGURE;
GO
```
Pour plus d’informations, consultez [Configure the remote data archive Server Configuration Option](https://msdn.microsoft.com/library/mt143175.aspx) et [sp\_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Utiliser l’Assistant pour activer Stretch Database sur une base de données
Pour plus d’informations sur l’Assistant Activer la base de données pour Stretch, notamment sur les informations que vous devez entrer et les choix que vous devez faire, consultez [Commencez par exécuter l’Assistant Activer une base de données pour Stretch](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Utiliser Transact-SQL pour activer Stretch Database sur une base de données
Avant de pouvoir activer Stretch Database sur des tables spécifiques, vous devez l’activer sur la base de données.

L’activation de Stretch Database sur une table ou une base de données nécessite des autorisations db\_owner. L’activation de Stretch Database sur une base de données nécessite également des autorisations CONTROL DATABASE.

1.  Avant de commencer, choisissez un serveur Azure existant pour les données migrées par Stretch Database, ou créez un serveur.

2.  Sur le serveur Azure, créez une règle de pare-feu avec l’adresse IP (ou plage d’adresses IP) de l’ordinateur SQL Server qui autorise SQL Server à communiquer avec le serveur distant.

3.  Pour configurer une base de données SQL Server pour Stretch Database, il faut que la base de données ait une clé principale de base de données. Celle-ci sécurise les informations d’identification utilisées par Stretch Database pour se connecter à la base de données distante. Pour créer manuellement la clé principale de base de données, consultez [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) et [Créer une clé principale de base de données](https://msdn.microsoft.com/library/aa337551.aspx).

    ```tsql
    USE <database>
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>'
    ```

4.  Quand vous configurez une base de données pour Stretch Database, vous devez fournir les informations d’identification que Stretch Database doit utiliser pour les communications entre l’ordinateur SQL Server local et le serveur Azure distant. Deux options s’offrent à vous.

    -   Vous pouvez fournir des informations d’identification d’administrateur.

        -   Si vous activez Stretch Database en exécutant l’Assistant, vous pouvez créer les informations d’identification à ce moment-là.

        -   Si vous activez Stretch Database en exécutant **ALTER DATABASE**, vous devez créer manuellement les informations d’identification avant d’activer Stretch Database.

        Pour créer manuellement les informations d’identification, consultez [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). La création des informations d’identification nécessite des autorisations ALTER ANY CREDENTIAL.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>'
        GO
        ```

    -   Vous pouvez utiliser un compte de service fédéré pour que l’ordinateur SQL Server communique avec le serveur Azure distant quand les conditions suivantes sont remplies.

        -   Le compte de service sous lequel s’exécute l’instance de SQL Server est un compte de domaine.

        -   Le compte de domaine appartient à un domaine dont l’annuaire Active Directory est fédéré avec Azure Active Directory.

        -   Le serveur Azure distant est configuré pour prendre en charge l’authentification Azure Active Directory.

        -   Le compte de service sous lequel s’exécute l’instance de SQL Server doit être configuré comme compte dbmanager ou sysadmin sur le serveur Azure distant.

5.  Pour configurer une base de données pour Stretch Database, exécutez la commande ALTER DATABASE.

    1.  Pour l’argument SERVER, indiquez le nom d’un serveur Azure existant, y compris la partie `.database.windows.net` du nom (par exemple `MyStretchDatabaseServer.database.windows.net`).

    2.  Spécifiez des informations d’identification d’administrateur existantes avec l’argument CREDENTIAL ou spécifiez FEDERATED\_SERVICE\_ACCOUNT \\= ON. L’exemple suivant fournit des informations d’identification existantes.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO;
    ```

## Étapes suivantes
Activer des tables supplémentaires pour Stretch Database. Surveiller la migration des données et gérer des tables et des bases de données Stretch.

-   [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md) pour activer des tables supplémentaires.

-   [Surveiller Stretch Database](sql-server-stretch-database-monitor.md) pour connaître l’état de migration des données.

-   [Suspendre et reprendre Stretch Database](sql-server-stretch-database-pause.md)

-   [Gérer et dépanner Stretch Database](sql-server-stretch-database-manage.md)

-   [Sauvegarder des bases de données Stretch](sql-server-stretch-database-backup.md)

## Voir aussi

[Identifier les bases de données et les tables pour Stretch Database](sql-server-stretch-database-identify-databases.md)

[Options SET d’ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

<!---HONumber=AcomDC_0615_2016-->