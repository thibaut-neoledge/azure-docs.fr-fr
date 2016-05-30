<properties
   pageTitle="Récupérer une base de données après une défaillance dans SQL Data Warehouse | Microsoft Azure"
   description="Procédure de récupération d’une base de données après une défaillance dans SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/20/2016"
   ms.author="sahajs;barbkess"/>

# Récupérer une base de données après une défaillance dans SQL Data Warehouse

La géorestauration permet de restaurer une base de données à partir d’une sauvegarde géoredondante. La base de données restaurée peut être créée sur n’importe quel serveur dans n’importe quelle région Azure. Comme la géo-restauration utilise une sauvegarde géo-redondante en tant que source, vous pouvez la mettre à profit pour récupérer une base de données même si celle-ci est inaccessible en raison d’une défaillance. Outre la récupération après une défaillance, la géorestauration peut également servir pour d’autres scénarios, tels que la migration ou la copie d’une base de données vers un autre serveur ou une autre région.

## Quand initier la récupération ?
L’opération de récupération requiert la modification de la chaîne de connexion SQL et peut entraîner une perte de données définitive. Par conséquent, elle doit être effectuée uniquement quand la défaillance est susceptible de durer plus longtemps que le RTO de votre application. Pour indiquer que la récupération est justifiée, utilisez les points de données suivants :

- Problème de connectivité permanent à la base de données.
- Votre portail Azure affiche une alerte concernant un incident dans la région avec un large impact.

## Récupération à l’aide de la géo-restauration
La récupération d’une base de données crée une autre base de données à partir de la dernière sauvegarde géo-redondante. Il est important de s'assurer que le serveur sur lequel vous effectuez la restauration a une capacité DTU suffisante pour la nouvelle base de données. Consultez ce billet de blog pour plus d’informations sur [l’affichage et l’augmentation du quota DTU][].

### Portail Azure
1. Connectez-vous au [portail Azure][]
2. Sur le côté gauche de l’écran, sélectionnez **+NOUVEAU**, puis **Données et stockage** et **SQL Data Warehouse**.
3. Sélectionnez **SAUVEGARDE** comme source, puis la sauvegarde géo-redondante à partir de laquelle vous souhaitez effectuer la récupération.
4. Spécifiez le reste des propriétés de base de données, puis cliquez sur **Créer**
5. Le processus de restauration de base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**

### PowerShell

Pour récupérer une base de données, utilisez l’applet de commande [Restore-AzureRmSqlDatabase][].

> [AZURE.NOTE]  Pour utiliser Azure PowerShell avec SQL Data Warehouse, vous devez installer Azure PowerShell version 1.0.3 ou supérieure. Vous pouvez vérifier la version en exécutant **Get-Module -ListAvailable -Name Azure**. La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][]. Pour plus d’informations sur l’installation de la dernière version, consultez [Installation et configuration d’Azure PowerShell][].

1. Ouvrez Windows PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement qui contient la base de données à restaurer.
4. Obtenez la base de données à récupérer.
5. Créez la demande de récupération de la base de données.
6. Vérifiez l’état de la base de données affectée par la géo-restauration.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

>[AZURE.NOTE] Pour le serveur nommé foo.database.windows.net, vous devez utiliser « foo » comme nom du serveur dans les applets de commande PowerShell ci-dessus.

### API REST
Utilisez REST pour exécuter la récupération de base de données par programmation.

1. Obtenez la liste des bases de données récupérables à l'aide de l'opération [List Recoverable Databases][].
2. Obtenez la base de données à récupérer à l'aide de l'opération [Get Recoverable Database][].
3. Créez la requête de récupération à l'aide de l'opération [Create Database Recovery Request][].
4. Suivez l’état de la récupération à l’aide de l’opération [Database Operation Status][].

## Configurer votre base de données après récupération
Voici une liste de contrôle pour vous aider à préparer votre base de données de production récupérée.

1. **Mettre à jour les chaînes de connexion** : vérifiez que les chaînes de connexion de vos outils clients pointent vers la base de données qui vient d’être récupérée.
2. **Modifier les règles de pare-feu** : vérifiez les règles de pare-feu sur le serveur cible. Assurez-vous également que les connexions entre les ordinateurs clients ou Azure et le serveur et la base de données qui vient d’être récupérée sont activées.
3. **Vérifier les connexions de serveur et les utilisateurs des bases de données** : vérifiez que toutes les connexions utilisées par votre application existent sur le serveur qui héberge votre base de données récupérée. Créez à nouveau les connexions manquantes et accordez-leur les autorisations appropriées sur la base de données restaurée. 
4. **Activer l’audit** : si la fonction d’audit est requise pour l’accès à votre base de données, vous devez l’activer après la récupération de la base de données.

La base de données récupérée sera compatible avec le chiffrement transparent des données si la base de données source l’est aussi.

## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, consultez [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Installation et configuration d’Azure PowerShell]: ../powershell/powershell-install-configure.md
[Vue d’ensemble de la continuité des activités de la base de données SQL Azure]: ../sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[List Recoverable Databases]: https://msdn.microsoft.com/library/azure/dn800984.aspx
[Get Recoverable Database]: https://msdn.microsoft.com/library/azure/dn800985.aspx
[Create Database Recovery Request]: https://msdn.microsoft.com/library/azure/dn800986.aspx
[Database Operation Status]: https://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Blog references-->
[l’affichage et l’augmentation du quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[portail Azure]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->