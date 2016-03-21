<properties
	pageTitle="Sauvegarder et restaurer des bases de données Stretch | Microsoft Azure"
	description="Découvrez comment sauvegarder et restaurer des bases de données Stretch."
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
	ms.date="02/26/2016"
	ms.author="douglasl"/>


# Sauvegarder et restaurer des bases de données Stretch 

Pour sauvegarder et restaurer des bases de données Stretch, vous pouvez continuer à appliquer les méthodes que vous appliquez actuellement. Pour plus d’informations sur la sauvegarde et la restauration SQL Server, consultez [Sauvegarde et restauration de bases de données SQL](https://msdn.microsoft.com/library/ms187048.aspx).

Une sauvegarde d’une base de données Stretch est une sauvegarde partielle qui n’inclut pas les données migrées vers le serveur distant.

La fonctionnalité Stretch Database fournit une prise en charge complète de la limite de restauration dans le temps. Une fois que vous avez restauré votre base de données SQL Server à une limite dans le temps et autorisé la connexion à Azure, Stretch Database rapproche les données distantes à la même limite dans le temps. Pour plus d’informations sur la limite de restauration dans le temps dans SQL Server, consultez [Restaurer une base de données SQL Server jusqu’à une limite dans le temps (mode de récupération complète)](https://msdn.microsoft.com/library/ms179451.aspx). Pour plus d’informations sur la procédure stockée à exécuter après une restauration pour réautoriser la connexion à Azure, consultez [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx).

## <a name="Reconnect"></a>Restaurer une base de données Stretch à partir d’une sauvegarde

1.  Restaurez la base de données à partir d’une sauvegarde.

2.  Créez une clé principale de base de données pour la base de données Stretch. Pour plus d’informations, consultez [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx)

3.  Créez des informations d’identification de portée de base de données pour la base de données Stretch. Pour plus d’informations, consultez [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

4.  Exécutez la procédure stockée [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) pour reconnecter la base de données Stretch locale à Azure. Fournissez les informations d’identification de portée de base de données créées à l’étape précédente sous forme de valeur varchar (128) ou sysname. (N’utilisez pas varchar(max).)

    ```tsql
    Declare @credentialName nvarchar(128);
    SET @credentialName = N’<database_scoped_credential_name_created_previously>’;
    EXEC sp_rda_reauthorize_db @credentialName;
    ```

## <a name="MoreInfo"></a>Plus d’informations sur la sauvegarde et la restauration
Les sauvegardes sur une base de données où Stretch Database est activée contiennent uniquement les données locales et les données éligibles à la limite dans le temps où la sauvegarde s’exécute. Ces sauvegardes contiennent également des informations sur le point de terminaison distant où se trouvent les données distantes de la base de données. On appelle cela une « sauvegarde partielle ». Les sauvegardes complètes qui contiennent toutes les données de la base de données, à la fois locales et distantes, ne sont pas prises en charge.

![Schéma de sauvegarde Stretch Database][StretchBackupImage1]

Quand vous restaurez une sauvegarde d’une base de données avec Stretch Database activée, cette opération restaure les données locales et les données éligibles dans la base de données comme prévu. (Les données éligibles sont des données qui n’ont pas encore été déplacées, mais qui seront déplacées vers Azure conformément à la configuration Stretch Database des tables.) Après l’opération de restauration, la base de données contient les données locales et éligibles à partir du point où la sauvegarde a été exécutée, mais elle n’a pas les informations d’identification et les artefacts nécessaires pour se connecter au point de terminaison distant.

![Stretch Database après la sauvegarde][StretchBackupImage2]

Vous devez exécuter la procédure stockée **sys.sp\_rda\_reauthorize\_db** pour rétablir la connexion entre la base de données locale et son point de terminaison distant. Seul un db\_owner peut effectuer cette opération. Cette procédure stockée nécessite également le nom d’utilisateur et le mot de passe administrateur du point de terminaison distant. Cela signifie que vous devez fournir la connexion et le mot de passe de l’administrateur pour le serveur Azure cible.

![Stretch Database après la sauvegarde][StretchBackupImage3]

Une fois que vous avez rétabli la connexion, Stretch Database tente de rapprocher les données éligibles dans la base de données locale et les données distantes en créant une copie des données distantes sur le point de terminaison distant et en les liant à la base de données locale. Ce processus est automatique et ne nécessite aucune intervention de l’utilisateur. Une fois ce rapprochement effectué, la base de données locale et le point de terminaison distant sont dans un état cohérent.

![Stretch Database après la sauvegarde][StretchBackupImage4]

## Voir aussi
[Gérer et résoudre les problèmes liés à Stretch Database](sql-server-stretch-database-manage.md) [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) [Sauvegarder et restaurer des bases de données SQL](https://msdn.microsoft.com/library/ms187048.aspx)

<!--Image references-->
[StretchBackupImage1]: ./media/sql-server-stretch-database-backup/StretchDBBackup1.png
[StretchBackupImage2]: ./media/sql-server-stretch-database-backup/StretchDBBackup2.png
[StretchBackupImage3]: ./media/sql-server-stretch-database-backup/StretchDBBackup3.png
[StretchBackupImage4]: ./media/sql-server-stretch-database-backup/StretchDBBackup4.png

<!------HONumber=AcomDC_0309_2016-->