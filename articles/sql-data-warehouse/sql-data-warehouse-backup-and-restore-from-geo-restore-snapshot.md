<properties
   pageTitle="Récupérer une base de données après une défaillance dans SQL Data Warehouse | Microsoft Azure"
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
   ms.date="03/23/2016"
   ms.author="sahajs;barbkess"/>

# Récupérer une base de données après une défaillance dans SQL Data Warehouse

La géo-restauration permet de restaurer une base de données à partir d’une sauvegarde géo-redondante pour créer une autre base de données. La base de données peut être créée sur n’importe quel serveur dans n’importe quelle région Azure. Comme elle utilise une sauvegarde géo-redondante en tant que source, vous pouvez la mettre à profit pour récupérer une base de données même si celle-ci est inaccessible en raison d’une défaillance. Outre la récupération après une défaillance, la géo-restauration peut également servir pour d’autres scénarios, tels que la migration ou la copie de la base de données vers un autre serveur ou une autre région.


## Quand initier la récupération ?
L’opération de récupération requiert la modification de la chaîne de connexion SQL et peut entraîner une perte de données définitive. Par conséquent, elle doit être effectuée uniquement quand la défaillance est susceptible de durer plus longtemps que le RTO de votre application. Pour indiquer que la récupération est justifiée, utilisez les points de données suivants :

- Problème de connectivité permanent à la base de données.
- Votre portail Azure affiche une alerte concernant un incident dans la région avec un large impact.


## Récupération à l’aide de la géo-restauration
La récupération d’une base de données crée une autre base de données à partir de la dernière sauvegarde géo-redondante. Il est important de s'assurer que le serveur sur lequel vous effectuez la récupération a une capacité DTU suffisante pour la nouvelle base de données. Vous pouvez demander une augmentation de ce quota en [contactant le support][].


### Portail Azure
1. Connectez-vous au [portail Azure][].
2. Sur le côté gauche de l’écran, sélectionnez **+NOUVEAU**, puis **Données et stockage** et **SQL Data Warehouse**.
3. Sélectionnez **SAUVEGARDE** comme source, puis la sauvegarde géo-redondante à partir de laquelle vous souhaitez effectuer la récupération.
4. Spécifiez le reste des propriétés de base de données, puis cliquez sur **Créer**
5. Le processus de restauration de base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**


### PowerShell
Utilisez Azure PowerShell pour exécuter par programmation la récupération d’une base de données. Pour télécharger le module Azure PowerShell, exécutez [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Vous pouvez vérifier la version en exécutant Get-Module -ListAvailable -Name Azure. Cet article est basé sur Microsoft Azure PowerShell 1.0.4.

Pour récupérer une base de données, utilisez l’applet de commande [Start-AzureSqlDatabaseRecovery][].

1. Ouvrez Windows PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement qui contient la base de données à restaurer.
4. Obtenez la base de données à récupérer.
5. Créez la demande de récupération de la base de données.
6. Surveillez la progression de la récupération.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$Database = Get-AzureRmSqlRecoverableDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Recover database
$RecoveryRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourSourceServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -TargetServerName "<YourTargetServerName>"

# Monitor progress of recovery operation
Get-AzureSqlDatabaseOperation -ServerName "<YourTargetServerName>" –OperationGuid $RecoveryRequest.RequestID

```

Notez que si votre serveur est nommé foo.database.windows.net, vous devez utiliser « foo »comme nom du serveur dans les applets de commande Powershell ci-dessus.

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
4. **Activer l’audit** : si la fonction d’audit est nécessaire pour accéder à votre base de données, vous devez l’activer après la récupération de la base de données.

La base de données récupérée sera compatible avec le chiffrement transparent des données si la base de données source l’est aussi.


## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité d’autres éditions de Base de données SQL Azure, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][].


<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la continuité des activités de la base de données SQL Azure]: sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Start-AzureSqlDatabaseRecovery]: https://msdn.microsoft.com/library/azure/dn720224.aspx
[List Recoverable Databases]: http://msdn.microsoft.com/library/azure/dn800984.aspx
[Get Recoverable Database]: http://msdn.microsoft.com/library/azure/dn800985.aspx
[Create Database Recovery Request]: http://msdn.microsoft.com/library/azure/dn800986.aspx
[Database Operation Status]: http://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Other Web references-->
[portail Azure]: https://portal.azure.com/
[contactant le support]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0330_2016-->