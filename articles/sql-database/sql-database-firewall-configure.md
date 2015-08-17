<properties
   pageTitle="Pare-feu Azure SQL Database | Microsoft Azure"
   description="Comment configurer votre pare-feu Microsoft Azure SQL Database."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/04/2015"
   ms.author="rickbyh"/>

# Pare-feu Azure SQL Database

Microsoft Azure SQL Database fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour aider à protéger vos données, le pare-feu Azure SQL Database empêche tout accès à votre serveur Azure SQL Database jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l'accès en fonction de l'adresse IP d'origine de chaque demande.

Pour configurer votre pare-feu, vous créez des règles de pare-feu qui spécifient les plages d’adresses IP acceptables. Vous pouvez créer des règles de pare-feu au niveau du serveur et de la base de données.

- **Règles de pare-feu au niveau du serveur :** ces règles permettent aux clients d’accéder à l’ensemble de votre serveur Azure SQL Database, c’est-à-dire à toutes les bases de données dans le même serveur logique. Ces règles sont stockées dans la base de données **principale**.
- **Règles de pare-feu au niveau de la base de données :** ces règles permettent aux clients d’accéder aux bases de données individuelles au sein de votre serveur Azure SQL Database. Ces règles sont créées par base de données et sont stockées dans les différentes bases de données (y compris la base de données **principale**). Ces règles peuvent être utiles pour restreindre l’accès à certaines bases de données (sécurisées) dans le même serveur logique.

**Recommandation :** Microsoft recommande d’utiliser, dans la mesure du possible, des règles de pare-feu au niveau de la base de données pour renforcer la portabilité de la base de données. Utilisez des règles de pare-feu au niveau du serveur quand plusieurs bases de données ont les mêmes exigences d’accès et que vous ne souhaitez les configurer une à une.

**À propos des fédérations :** l’implémentation actuelle des fédérations sera retirée en même temps que les niveaux de service Web et Business. Considérez le déploiement de solutions de partitionnement personnalisées pour optimiser les performances, la flexibilité et l'évolutivité. Pour plus d’informations sur le partitionnement personnalisé, consultez [Montée en puissance parallèle de bases de données SQL Azure](https://msdn.microsoft.com/library/dn495641.aspx).

> [AZURE.NOTE]Si vous créez une fédération de bases de données dans Azure SQL Database dans laquelle la base de données racine contient des règles de pare-feu au niveau de la base de données, les règles ne sont pas copiées vers les bases de données membres de la fédération. Si vous avez besoin de règles de pare-feu au niveau de la base de données pour les membres de la fédération, vous devez recréer les règles pour ces derniers. Toutefois, si vous fractionnez un membre de la fédération contenant une règle de pare-feu au niveau de la base de données en nouveaux membres de fédération à l’aide de l’instruction ALTER FEDERATION … SPLIT, les nouveaux membres de destination ont les mêmes règles de pare-feu au niveau de la base de données que le membre de fédération source. Pour plus d’informations sur les fédérations, consultez [Fédérations dans Azure SQL Database](https://msdn.microsoft.com/library/hh597452.aspx).

## Vue d'ensemble

Initialement, tout accès à votre serveur de base de données SQL Azure est bloqué par le pare-feu. Pour commencer à utiliser votre serveur de base de données SQL Azure, vous devez accéder au portail de gestion et spécifier une ou plusieurs règles de pare-feu au niveau du serveur qui permettent l’accès à votre serveur Azure SQL Database. Utilisez les règles de pare-feu pour spécifier les plages d’adresses IP Internet qui sont autorisées, et si les applications Azure peuvent essayer de se connecter à votre serveur Azure SQL Database.

Toutefois, si vous souhaitez accorder l’accès de manière sélective à une des bases de données de votre serveur Azure SQL Database, vous devez créer une règle au niveau de la base de données pour la base de données concernée avec une plage d’adresses IP au-delà de la plage d’adresses IP spécifiée dans la règle de pare-feu au niveau du serveur et vérifier que l’adresse IP du client se situe dans la plage spécifiée dans la règle au niveau de la base de données.

Les tentatives de connexion à partir d’Internet et d’Azure doivent franchir le pare-feu avant de pouvoir atteindre votre base de données ou serveur Azure SQL Database, comme illustré dans le diagramme suivant.

   ![sqldb-firewall][1]

## Connexion à partir d’Internet

Quand un ordinateur tente de se connecter à votre serveur de base de données à partir d’Internet, le pare-feu vérifie l’adresse IP d’origine de la demande par rapport à la totalité des règles de pare-feu au niveau du serveur et, si nécessaire, au niveau de la base de données :

- Si l’adresse IP de la demande appartient à une des plages spécifiées dans les règles de pare-feu au niveau du serveur, la connexion est accordée à votre serveur Azure SQL Database.

- Si l’adresse IP de la demande n’appartient pas à une des plages spécifiées dans la règle de pare-feu au niveau du serveur, les règles de pare-feu au niveau de la base de données sont vérifiées. Si l’adresse IP de la demande appartient à une des plages spécifiées dans les règles de pare-feu au niveau de la base de données, la connexion est accordée uniquement à la base de données qui possède une règle au niveau de la base de données correspondante.

- Si l’adresse IP de la demande n’appartient pas aux plages spécifiées dans une règle de pare-feu au niveau du serveur ou de la base de données, la demande de connexion échoue.

> [AZURE.NOTE]Pour accéder à Azure SQL Database à partir de votre ordinateur local, vérifiez que le pare-feu sur votre réseau et l’ordinateur local autorise les communications sortantes sur le port TCP 1433.
 

## Connexion à partir d’Azure

Quand une application à partir d’Azure tente de se connecter à votre serveur de base de données, le pare-feu vérifie que les connexions Azure sont autorisées. Un paramètre de pare-feu avec des adresses de début et de fin égales à 0.0.0.0 indique que ces connexions sont autorisées. Si la tentative de connexion n’est pas autorisée, la demande n’atteint pas le serveur Azure SQL Database.

Vous pouvez activer des connexions à partir d’Azure dans le [portail de gestion](http://go.microsoft.com/fwlink/p/?LinkID=161793) de deux manières :

- Cochez la case **Autorisez les services Microsoft Azure à accéder au serveur** quand vous créez un serveur.

- Sous l’onglet **Configurer** sur un serveur, dans la section **Services autorisés**, cliquez sur **Oui** pour **Services Microsoft Azure**.

## Création de la première règle de pare-feu au niveau du serveur

Le premier paramètre de pare-feu au niveau du serveur peut être créé à l’aide du [portail de gestion](http://go.microsoft.com/fwlink/p/?LinkID=161793) ou par programmation à l’aide de l’API REST ou d’Azure PowerShell. Les règles de pare-feu au niveau du serveur suivantes peuvent être créées et gérées à l’aide de ces méthodes, ainsi que par le biais de Transact-SQL. Pour plus d’informations sur les règles de pare-feu au niveau du serveur, consultez [Procédure : configuration des paramètres de pare-feu (Azure SQL Database)](article:sql-database-configure-firewall-settings).

## Création de règles de pare-feu au niveau de la base de données

Après avoir configuré le premier pare-feu au niveau du serveur, vous pouvez limiter l’accès à certaines bases de données. Si vous spécifiez dans la règle de pare-feu au niveau de la base de données une plage d’adresses IP qui se situe en dehors de la plage spécifiée dans la règle de pare-feu au niveau du serveur, seuls les clients dont les adresses IP appartiennent à la plage de niveau de base de données peuvent accéder à la base de données. Vous pouvez avoir un maximum de 128 règles de pare-feu au niveau de la base de données par base de données. Les règles de pare-feu au niveau de la base données pour les bases de données principale et utilisateur peuvent être créées et gérées via Transact-SQL. Pour plus d’informations, consultez [Configuration des paramètres du pare-feu (Azure SQL Database)](article:sql-database-configure-firewall-settings).

## Gestion par programmation des règles de pare-feu

Outre avec le portail de gestion Azure, les règles de pare-feu peuvent être gérées par programmation à l’aide de Transact-SQL, de l’API REST et d’Azure PowerShell. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles pour chaque méthode.


### Transact-SQL

| Vue de catalogue ou procédure stockée | Level | Description |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall\\\_rules] (https://msdn.microsoft.com/library/dn269980.aspx) | Serveur | Affiche les règles de pare-feu au niveau du serveur actuelles || [sp\\\_set\\\_firewall\\\_rule] (https://msdn.microsoft.com/library/dn270017.aspx) | Serveur | Crée ou met à jour les règles de pare-feu au niveau du serveur || [sp\\\_delete\\\_firewall\\\_rule] (https://msdn.microsoft.com/library/dn270024.aspx) | Serveur | Supprime les règles de pare-feu au niveau du serveur || [sys.database\\\_firewall\\\_rules] (https://msdn.microsoft.com/library/dn269982.aspx) | Base de données | Affiche les règles de pare-feu au niveau de la base de données actuelles || [sp\_set\\\_database\\\_firewall\\\_rule] (https://msdn.microsoft.com/library/dn270010.aspx) | Base de données | Crée ou met à jour les règles de pare-feu au niveau de la base de données || [sp\\\_delete\\\_database\\\_firewall\_rule] (https://msdn.microsoft.com/library/dn270030.aspx) | Bases de données | Supprime les règles de pare-feu au niveau de la base de données |

### API REST

| API | Level | Description |
|----------------------|--------|------------------------------------------------------------------|
| [Répertorier les règles de pare-feu](https://msdn.microsoft.com/library/azure/dn505715.aspx) | Serveur | Affiche les règles de pare-feu au niveau du serveur actuelles |
| [Créer une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Serveur | Crée ou met à jour les règles de pare-feu au niveau du serveur |
| [Obtenir une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505707.aspx) | Serveur | Met à jour les propriétés d’une règle de pare-feu au niveau du serveur existante |
| [Supprimer une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Serveur | Supprime des règles de pare-feu au niveau du serveur |


### Azure PowerShell

| Applet de commande | Level | Description |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) | Serveur | Retourne les règles de pare-feu au niveau du serveur actuelles |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) | Serveur | Crée une règle de pare-feu au niveau du serveur |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) | Serveur | Met à jour les propriétés d’une règle de pare-feu au niveau du serveur existante |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Serveur | Supprime des règles de pare-feu au niveau du serveur |

> [AZURE.NOTE]il peut y avoir un délai maximal de cinq minutes pour que les modifications apportées au pare-feu soient appliquées.

## Dépannage du pare-feu

Considérez les points suivants quand l’accès au service Microsoft Azure SQL Database est anormal :

- **Configuration du pare-feu local :** pour que votre ordinateur puisse accéder à Azure SQL Database, vous devez créer une exception de pare-feu sur votre ordinateur pour le port TCP 1433. 

- **Traduction d’adresses réseau (NAT) :** en raison de NAT, l’adresse IP utilisée par votre ordinateur pour se connecter à Azure SQL Database peut être différente de l’adresse IP affichée dans les paramètres de configuration IP de votre ordinateur. Pour afficher l’adresse IP qu’utilise votre ordinateur pour se connecter à Azure, connectez-vous au portail de gestion et accédez à l’onglet **Configurer** sur le serveur qui héberge votre base de données. Dans la section **Adresses IP autorisées**, l’**Adresse IP client en cours** s’affiche. Cliquez sur **Ajouter aux adresses IP autorisées** pour que cet ordinateur puisse accéder au serveur.

- **Les modifications apportées à la liste d’approbation n’ont pas encore pris effet :** jusqu’à cinq minutes peuvent s’écouler avant que les modifications apportées à la configuration du pare-feu Azure SQL Database ne soient effectives.

- **La connexion n’est pas autorisée ou un mot de passe incorrect a été utilisé :** si une connexion n’a pas d’autorisations sur le serveur Azure SQL Database ou que le mot de passe est incorrect, la connexion au serveur Azure SQL Database est refusée. Créer un paramètre de pare-feu permet uniquement aux clients de tenter de se connecter à votre serveur ; chaque client doit fournir les informations d’identification de sécurité nécessaires. Pour plus d’informations sur la préparation des connexions, consultez Gestion des bases de données, des connexions et des utilisateurs dans Azure SQL Database.

- **Adresse IP dynamique :** si vous avez une connexion Internet avec adressage IP dynamique et que le pare-feu demeure infranchissable, vous pouvez essayer une des solutions suivantes :

 - Demandez à votre fournisseur de services Internet (ISP) la plage d’adresses IP affectée à vos ordinateurs clients qui accèdent au serveur Azure SQL Database, puis ajoutez cette plage en tant que règle de pare-feu.

 - Obtenez un adressage IP statique à la place pour vos ordinateurs clients, puis ajoutez les adresses IP en tant que règles de pare-feu.

## Voir aussi

[Configuration des paramètres du pare-feu (Base de données SQL Azure)](article:sql-database-configure-firewall-settings)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

<!---HONumber=August15_HO6-->