<properties
	pageTitle="Restaurer des bases de données Stretch | Microsoft Azure"
	description="Découvrez comment restaurer des bases de données Stretch."
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
	ms.date="06/03/2016"
	ms.author="douglasl"/>

# Restaurer des bases de données Stretch

Restaurez une base de données sauvegardée lorsque c’est nécessaire pour effectuer une récupération à partir de nombreux types d’échecs, d’erreurs et de sinistres.

Pour plus d’informations sur la sauvegarde, consultez [Sauvegarder des bases de données Stretch](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] La sauvegarde n’est qu’une partie d’une solution complète de haute disponibilité et de continuité d’activité. Pour plus d’informations sur la haute disponibilité, consultez [Solutions de haute disponibilité](https://msdn.microsoft.com/library/ms190202.aspx).

## Restaurer vos données SQL Server
Pour récupérer à partir d’une défaillance ou un endommagement matériel, restaurez la base de données Stretch SQL Server à partir d’une sauvegarde. Vous pouvez continuer à utiliser les méthodes de restauration SQL Server que vous utilisez actuellement. Pour plus d’informations, consultez [Vue d’ensemble de la récupération et de la restauration](https://msdn.microsoft.com/library/ms191253.aspx).

Après avoir restauré la base de données SQL Server, vous devez exécuter la procédure stockée **sys.sp\_rda\_reauthorize\_db** pour restaurer la connexion entre la base de données Stretch SQL Server et la base de données Azure distante. Pour plus d’informations, consultez [Restaurer la connexion entre la base de données SQL Server et la base de données Azure distante](#Restore-the-connection-between-the-SQL-Server-database-and-the-remote-Azure-database).

## Restaurer vos données Azure distantes

### Restaurer une base de données Azure active
Le service SQL Server Stretch Database sur Azure crée des instantanés de toutes les données actives au moins toutes les huit heures, à l’aide d’instantanés Azure Storage. Ces instantanés sont conservés pendant 7 jours. Ainsi, vous pouvez restaurer les données à 21 instants différents au cours des 7 derniers jours, jusqu’au moment de la prise du dernier instantané.

Pour restaurer une base de données Azure active à un point antérieur dans le temps à l’aide du portail Azure, procédez comme suit.

1. Connectez-vous au portail Azure.
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Bases de données SQL**
3. Accédez à votre base de données et sélectionnez-la.
4. En haut du panneau de la base de données, cliquez sur **Restaurer**.
5. Spécifiez un nouveau **nom de base de données**, sélectionnez un **point de restauration**, puis cliquez sur **Créer**.
6. Le processus de restauration de la base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**.

### Récupérer une base de données Azure supprimée
Avant qu’une base de données ne soit supprimée, le service SQL Server Stretch Database sur Azure en effectue une capture instantanée et la conserve pendant sept jours. Une fois cette opération achevée, il ne conserve plus les instantanés de la base de données active. Cela vous permet de restaurer une base de données supprimée dans son état au moment de sa suppression.

Pour restaurer une base de données Azure supprimée au moment de sa suppression à l’aide du portail Azure, procédez comme suit.

1. Connectez-vous au portail Azure.
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Serveur SQL Server**.
3. Accédez à votre serveur et sélectionnez-le.
4. Dans le panneau du serveur, faites défiler jusqu’à Opérations, puis cliquez sur la vignette **Bases de données supprimées**.
5. Sélectionnez la base de données supprimée que vous souhaitez restaurer.
5. Spécifiez un nouveau **nom pour la base de données** et cliquez sur **Créer**.
6. Le processus de restauration de la base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**.

### Restaurer une base de données Azure dans une autre région Azure  
Le service SQL Server Stretch Database sur Azure copie les instantanés de façon asynchrone dans une autre région Azure géographique pour faciliter leur récupération en cas de défaillance dans une région. Si vous ne pouvez pas accéder à votre base de données en raison d’une défaillance dans une région Azure, vous pouvez la restaurer à l’aide de l’un des instantanés géo-redondants.

>   [AZURE.NOTE] La récupération de la base de données Azure dans une autre région Azure requiert la modification de la chaîne de connexion dans les applications clientes après la récupération et peut entraîner une perte de données permanente. Effectuez ce type de récupération uniquement lorsque l’interruption est susceptible de durer longtemps.

Pour récupérer une base de données Azure à un point antérieur dans le temps dans une autre région Azure à l’aide du portail Azure, procédez comme suit.

1. Connectez-vous au portail Azure.
2. Sur le côté gauche de l’écran, sélectionnez **+NOUVEAU**, puis **Données et stockage** et **SQL Data Warehouse**
3. Sélectionnez **SAUVEGARDE** comme source, puis la sauvegarde géo-redondante à partir de laquelle vous souhaitez effectuer la récupération
4. Spécifiez le reste des propriétés de la base de données, puis cliquez sur **Créer**
5. Le processus de restauration de la base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**

Après avoir restauré la base de données Azure dans une autre région, vous devez exécuter les procédures stockées **sys.sp\_rda\_deauthorize\_db** et **sys.sp\_rda\_reauthorize\_db** pour restaurer la connexion entre la base de données Stretch SQL Server et la base de données Azure distante. Pour plus d’informations, consultez [Restaurer la connexion entre la base de données SQL Server et la base de données Azure distante](#Restore-the-connection-between-the-SQL-Server-database-and-the-remote-Azure-database).

## Restaurer la connexion entre la base de données SQL Server et la base de données Azure distante

1.  Si vous souhaitez vous connecter à une base de données Azure restaurée avec un nom différent ou dans une région différente, exécutez la procédure stockée [sys.sp\_rda\_deauthorize\_db](https://msdn.microsoft.com/library/mt703716.aspx) pour vous déconnecter de la base de données Azure précédente.  

2.  Exécutez la procédure stockée [sys.sp\_rda\_reauthorize\_db](https://msdn.microsoft.com/library/mt131016.aspx) pour reconnecter la base de données Stretch locale à la base de données Azure.

	-   Fournissez les informations d’identification de portée de base de données sous forme de valeur varchar (128) ou sysname. (N’utilisez pas varchar(max).) Vous pouvez rechercher le nom d’identification dans la vue **sys.database\_scoped\_credentials**.  

	-   Indiquez s’il est nécessaire d’effectuer une copie des données distantes et de s’y connecter (recommandé).

	```tsql  
	DECLARE @credentialName nvarchar(128);   
	SET @credentialName = N'<existing_database_scoped_credential_name>';   
	EXEC sp_rda_reauthorize_db @credential = @credentialName, @with_copy = 1;  

	```  

## Voir aussi

[Gérer et dépanner Stretch Database](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Sauvegarder et restaurer des bases de données SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)

<!---HONumber=AcomDC_0608_2016-->