<properties
   pageTitle="Présentation de la configuration d’un pare-feu sur un serveur SQL | Microsoft Azure"
   description="Apprenez à configurer un pare-feu de base de données SQL avec des règles de pare-feu au niveau du serveur et au niveau de la base de données pour gérer les accès."
   keywords="pare-feu de base de données"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# Configuration des règles de pare-feu de la base de données SQL Azure - Vue d’ensemble


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-firewall-configure.md)
- [Portail Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour aider à protéger vos données, le pare-feu empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.

Pour configurer votre pare-feu, vous créez des règles de pare-feu qui spécifient les plages d’adresses IP acceptables. Vous pouvez créer des règles de pare-feu au niveau du serveur et de la base de données.

- **Règles de pare-feu au niveau du serveur :** ces règles permettent aux clients d’accéder à l’ensemble de votre serveur Azure SQL, c’est-à-dire à toutes les bases de données dans le même serveur logique. Ces règles sont stockées dans la base de données **principale**. Les règles de pare-feu au niveau serveur peuvent être configurées en utilisant le portail ou avec des déclarations Transact-SQL.
- **Règles de pare-feu au niveau de la base de données :** ces règles permettent aux clients d’accéder aux bases de données individuelles au sein de votre serveur Azure SQL Database. Vous pouvez créer ces règles pour chaque base de données et elles sont stockées dans les bases de données individuelles. (Vous pouvez créer des règles de pare-feu au niveau de la base de données pour la base de données **maître**.) Ces règles peuvent être utiles pour restreindre l’accès à certaines bases de données (sécurisées) dans le même serveur logique. Les règles de pare-feu au base de données serveur peuvent seulement être configurées avec des déclarations Transact-SQL.

**Recommandation :** Microsoft recommande d’utiliser, dans la mesure du possible, des règles de pare-feu au niveau de la base de données pour améliorer la sécurité et renforcer la portabilité de la base de données. Utilisez des règles de pare-feu pour les administrateurs au niveau du serveur quand plusieurs bases de données ont les mêmes exigences d’accès et que vous ne souhaitez les configurer une à une.


## Présentation du pare-feu

Initialement, tout accès Transact-SQL à votre serveur SQL Azure est bloqué par le pare-feu. Pour commencer à utiliser votre serveur SQL Azure, vous devez accéder au portail Azure et spécifier une ou plusieurs règles de pare-feu au niveau du serveur qui permettent l’accès à votre serveur SQL Azure. Utilisez les règles de pare-feu pour spécifier les plages d’adresses IP Internet qui sont autorisées, et si les applications Azure peuvent essayer de se connecter à votre serveur Azure SQL.

Pour accorder l’accès de manière sélective à l’une des bases de données de votre serveur SQL Azure, vous devez créer une règle de niveau de la base de données pour la base de données requise. Spécifiez, pour la règle de pare-feu au niveau de la base de données, une plage d’adresses IP qui se situe au-delà de la plage d’adresses IP spécifiée dans la règle de pare-feu au niveau du serveur, et assurez-vous que l’adresse IP du client appartienne à la plage spécifiée dans la règle au niveau de la base de données.

Les tentatives de connexion à partir d’Internet et d’Azure doivent franchir le pare-feu avant de pouvoir atteindre votre serveur Azure SQL ou votre base de données SQL, comme illustré dans le diagramme suivant.

   ![Diagramme décrivant la configuration de pare-feu.][1]

## Connexion à partir d’Internet

Quand un ordinateur tente de se connecter à votre serveur de base de données à partir d’Internet, le pare-feu vérifie tout d’abord l’adresse IP d’origine de la demande par rapport à la totalité des règles de pare-feu :

- Si l’adresse IP de la demande appartient à une des plages spécifiées dans les règles de pare-feu au niveau du serveur, la connexion est accordée à votre serveur Azure SQL Database.

- Si l’adresse IP de la demande n’appartient pas à une des plages spécifiées dans la règle de pare-feu au niveau du serveur, les règles de pare-feu au niveau de la base de données sont vérifiées. Si l’adresse IP de la demande appartient à une des plages spécifiées dans les règles de pare-feu au niveau de la base de données, la connexion est accordée uniquement à la base de données qui possède une règle au niveau de la base de données correspondante.

- Si l’adresse IP de la demande n’appartient pas aux plages spécifiées dans une règle de pare-feu au niveau du serveur ou de la base de données, la demande de connexion échoue.

> [AZURE.NOTE] Pour accéder à Azure SQL Database à partir de votre ordinateur local, vérifiez que le pare-feu sur votre réseau et l’ordinateur local autorise les communications sortantes sur le port TCP 1433.


## Connexion à partir d’Azure

Quand une application à partir d’Azure tente de se connecter à votre serveur de base de données, le pare-feu vérifie que les connexions Azure sont autorisées. Un paramètre de pare-feu avec des adresses de début et de fin égales à 0.0.0.0 indique que ces connexions sont autorisées. Si la tentative de connexion n’est pas autorisée, la demande n’atteint pas le serveur Azure SQL Database.

> [AZURE.IMPORTANT] Cette option configure le pare-feu pour autoriser toutes les connexions à partir d’Azure, notamment les connexions issues des abonnements d’autres clients. Lorsque vous sélectionnez cette option, vérifiez que votre connexion et vos autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.

Vous pouvez activer des connexions à partir d’Azure de deux manières :

- Sur le [portail Microsoft Azure](https://portal.azure.com/), cochez la case **Autoriser les services Azure à accéder au serveur** quand vous créez un serveur.

- Sur le [portail Classic](http://go.microsoft.com/fwlink/p/?LinkID=161793), dans l’onglet **Configurer** sur un serveur, sous la section **Services autorisés**, cliquez sur **Oui** pour **Services Microsoft Azure**.

## Création de la première règle de pare-feu au niveau du serveur

Le premier paramètre de pare-feu au niveau du serveur peut être créé à l’aide du [portail Azure](https://portal.azure.com/) ou par programmation à l’aide de l’API REST ou d’Azure PowerShell. Les règles de pare-feu au niveau du serveur suivantes peuvent être créées et gérées à l’aide de ces méthodes, ainsi que par le biais de Transact-SQL. Pour améliorer les performances, les règles de pare-feu au niveau du serveur sont temporairement mises en cache au niveau de la base de données. Pour actualiser le cache, consultez [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Pour plus d’informations sur les règles de pare-feu au niveau du serveur, consultez [Configurer un pare-feu sur un serveur Azure SQL à l’aide du portail Azure](sql-database-configure-firewall-settings.md).

## Création de règles de pare-feu au niveau de la base de données

Après avoir configuré le premier pare-feu au niveau du serveur, vous pouvez limiter l’accès à certaines bases de données. Si vous spécifiez dans la règle de pare-feu au niveau de la base de données une plage d’adresses IP qui se situe en dehors de la plage spécifiée dans la règle de pare-feu au niveau du serveur, seuls les clients dont les adresses IP appartiennent à la plage de niveau de base de données peuvent accéder à la base de données. Vous pouvez avoir un maximum de 128 règles de pare-feu au niveau de la base de données par base de données. Les règles de pare-feu au niveau de la base données pour les bases de données principale et utilisateur peuvent être créées et gérées via Transact-SQL. Pour plus d’informations sur la configuration des règles de pare-feu au niveau de la base de données, consultez [sp\_set\_database\_firewall\_rule (bases de données SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

## Gestion par programmation des règles de pare-feu

Outre avec le portail Azure, les règles de pare-feu peuvent être gérées par programmation à l’aide de Transact-SQL, de l’API REST et d’Azure PowerShell. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles pour chaque méthode.


### Transact-SQL

| Vue de catalogue ou procédure stockée | Niveau | Description |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys\_firewall\_rules](https://msdn.microsoft.com/library/dn269980.aspx) | Serveur | Affiche les règles de pare-feu au niveau du serveur actuelles |
| [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) | Serveur | Crée ou met à jour les règles de pare-feu au niveau du serveur |
| [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) | Serveur | Supprime des règles de pare-feu au niveau du serveur |
| [sys.database\_firewall\_rules](https://msdn.microsoft.com/library/dn269982.aspx) | Base de données | Affiche les règles de pare-feu actuelles au niveau de la base de données |
| [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) | Base de données | Crée ou met à jour les règles de pare-feu au niveau de la base de données |
| [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Bases de données | Supprime les règles de pare-feu au niveau de la base de données |

### API REST

| API | Niveau | Description |
|----------------------|--------|------------------------------------------------------------------|
| [Répertorier les règles de pare-feu](https://msdn.microsoft.com/library/azure/dn505715.aspx) | Serveur | Affiche les règles de pare-feu au niveau du serveur actuelles |
| [Créer une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Serveur | Crée ou met à jour les règles de pare-feu au niveau du serveur |
| [Obtenir une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505707.aspx) | Serveur | Met à jour les propriétés d’une règle de pare-feu au niveau du serveur existante |
| [Supprimer une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Serveur | Supprime des règles de pare-feu au niveau du serveur |


### Azure PowerShell

| Applet de commande | Niveau | Description |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) | Serveur | Retourne les règles de pare-feu au niveau du serveur actuelles |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) | Serveur | Crée une règle de pare-feu au niveau du serveur |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) | Serveur | Met à jour les propriétés d’une règle de pare-feu au niveau du serveur existante |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Serveur | Supprime des règles de pare-feu au niveau du serveur |

> [AZURE.NOTE] il peut y avoir un délai maximal de cinq minutes pour que les modifications apportées au pare-feu soient appliquées.

## Dépannage du pare-feu de base de données

Considérez les points suivants quand l’accès au service Microsoft Azure SQL Database est anormal :

- **Configuration du pare-feu local :** pour que votre ordinateur puisse accéder à Azure SQL Database, vous devez créer une exception de pare-feu sur votre ordinateur pour le port TCP 1433. Vous devrez peut-être ouvrir des ports supplémentaires si vous effectuez des connexions dans la limite du cloud Azure. Pour plus d’informations, voir la section **V12 de SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Traduction d’adresses réseau (NAT) :** en raison du protocole NAT, l’adresse IP utilisée par votre ordinateur pour se connecter à la base de données SQL Azure peut être différente de l’adresse IP affichée dans les paramètres de configuration IP de votre ordinateur. Pour afficher l’adresse IP qu’utilise votre ordinateur pour se connecter à Azure, connectez-vous au portail et accédez à l’onglet **Configurer** sur le serveur qui héberge votre base de données. Dans la section **Adresses IP autorisées**, l’**adresse IP client en cours** s’affiche. Cliquez sur **Ajouter** aux **Adresses IP autorisées** pour que cet ordinateur puisse accéder au serveur.

- **Les modifications apportées à la liste d’approbation n’ont pas encore pris effet :** jusqu’à cinq minutes peuvent s’écouler avant que les modifications apportées à la configuration du pare-feu de la base de données SQL Azure SQL ne soient effectives.

- **La connexion n’est pas autorisée ou un mot de passe incorrect a été utilisé :** si une connexion n’a pas d’autorisations sur le serveur de la base de données SQL Azure ou que le mot de passe est incorrect, la connexion au serveur de la base de données SQL Azure est refusée. Créer un paramètre de pare-feu permet uniquement aux clients de tenter de se connecter à votre serveur ; chaque client doit fournir les informations d’identification de sécurité nécessaires. Pour plus d’informations sur la préparation des connexions, consultez Gestion des bases de données, des connexions et des utilisateurs dans Azure SQL Database.

- **Adresse IP dynamique :** si vous avez une connexion Internet avec adressage IP dynamique et que le pare-feu demeure infranchissable, vous pouvez essayer une des solutions suivantes :

 - Demandez à votre fournisseur de services Internet (ISP) la plage d’adresses IP affectée à vos ordinateurs clients qui accèdent au serveur Azure SQL Database, puis ajoutez cette plage en tant que règle de pare-feu.

 - Obtenez un adressage IP statique à la place pour vos ordinateurs clients, puis ajoutez les adresses IP en tant que règles de pare-feu.

## Étapes suivantes

Pour obtenir des articles sur la création de règles de pare-feu au niveau du serveur et au niveau de la base de données, consultez :

- [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
- [Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données sur une base de données SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md)
- [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

Pour obtenir un didacticiel sur la création d’une base de données, consultez [Créer une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md). Pour obtenir de l’aide afin de vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, consultez [Exemples de code de démarrage rapide client pour Base de données SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Pour comprendre comment naviguer dans les bases de données, consultez [Gérer la sécurité d’accès et de connexion aux bases de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).



## Ressources supplémentaires

- [Sécurisation de votre base de données](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

<!---HONumber=AcomDC_0914_2016-->