---
title: "Règles de pare-feu d’Azure SQL Database | Microsoft Docs"
description: "Apprenez à configurer un pare-feu de base de données SQL avec des règles de pare-feu au niveau du serveur et au niveau de la base de données pour gérer les accès."
keywords: "pare-feu de base de données"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 04/10/2017
ms.author: rickbyh
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 71c7eaf2272245bd681387947812f7d5c0f58094
ms.contentlocale: fr-fr
ms.lasthandoff: 08/30/2017

---
# <a name="azure-sql-database-server-level-and-database-level-firewall-rules"></a>Règles de pare-feu au niveau du serveur et de la base de données d’Azure SQL Database 

Microsoft Azure SQL Database fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour aider à protéger vos données, le pare-feu empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.

## <a name="overview"></a>Vue d'ensemble

Initialement, tout accès Transact-SQL à votre serveur SQL Azure est bloqué par le pare-feu. Pour commencer à utiliser votre serveur SQL Azure, vous devez spécifier une ou plusieurs règles de pare-feu au niveau du serveur qui permettent l’accès à votre serveur SQL Azure. Utilisez les règles de pare-feu pour spécifier les plages d’adresses IP Internet qui sont autorisées, et si les applications Azure peuvent essayer de se connecter à votre serveur Azure SQL.

Pour accorder l’accès de manière sélective à l’une des bases de données de votre serveur SQL Azure, vous devez créer une règle de niveau de la base de données pour la base de données requise. Spécifiez, pour la règle de pare-feu au niveau de la base de données, une plage d’adresses IP qui se situe au-delà de la plage d’adresses IP spécifiée dans la règle de pare-feu au niveau du serveur, et assurez-vous que l’adresse IP du client appartienne à la plage spécifiée dans la règle au niveau de la base de données.

Les tentatives de connexion à partir d’Internet et d’Azure doivent franchir le pare-feu avant de pouvoir atteindre votre serveur Azure SQL ou votre base de données SQL, comme illustré dans le diagramme suivant :

   ![Diagramme décrivant la configuration de pare-feu.][1]

* **Règles de pare-feu au niveau du serveur :** ces règles permettent aux clients d’accéder à l’ensemble de votre serveur Azure SQL, c’est-à-dire à toutes les bases de données dans le même serveur logique. Ces règles sont stockées dans la base de données **principale** . Les règles de pare-feu au niveau serveur peuvent être configurées en utilisant le portail ou avec des déclarations Transact-SQL. Pour créer des règles de pare-feu de niveau serveur à l’aide du portail Azure ou de PowerShell, vous devez être le propriétaire de l’abonnement ou un de ses collaborateurs. Pour créer une règle de pare-feu de niveau serveur à l’aide de Transact-SQL, vous devez vous connecter à l’instance de base de données SQL en utilisant la connexion principale de niveau serveur ou les identifiants de l’administrateur Azure Active Directory (cela signifie qu’un utilisateur doté des autorisations Azure doit au préalable créer la règle de pare-feu de niveau serveur).
* **Règles de pare-feu au niveau de la base de données :** ces règles permettent aux clients d’accéder à certaines bases de données (sécurisées) au sein du même serveur logique. Vous pouvez créer ces règles pour chaque base de données (dont la base de données **MASTER**) et elles sont stockées dans les bases de données individuelles. Les règles de pare-feu pour les bases de données principale et utilisateur peuvent uniquement être créées et configurées en utilisant des instructions Transact-SQL et uniquement après avoir configuré la première règle de pare-feu au niveau du serveur. Si vous spécifiez dans la règle de pare-feu au niveau de la base de données une plage d’adresses IP qui se situe en dehors de la plage spécifiée dans la règle de pare-feu au niveau du serveur, seuls les clients dont les adresses IP appartiennent à la plage de niveau de base de données peuvent accéder à la base de données. Vous pouvez avoir un maximum de 128 règles de pare-feu au niveau de la base de données par base de données. Pour plus d’informations sur la configuration des règles de pare-feu au niveau de la base de données, consultez l’exemple plus loin dans cet article et [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

**Recommandation :** Microsoft recommande d’utiliser, dans la mesure du possible, des règles de pare-feu au niveau de la base de données pour améliorer la sécurité et renforcer la portabilité de la base de données. Utilisez des règles de pare-feu pour les administrateurs au niveau du serveur quand plusieurs bases de données ont les mêmes exigences d’accès et que vous ne souhaitez les configurer une à une.

> [!Note]
> Pour plus d’informations sur les bases de données portables dans le cadre de la continuité d’activité, consultez [Exigences d’authentification pour la récupération d’urgence](sql-database-geo-replication-security-config.md).
>

### <a name="connecting-from-the-internet"></a>Connexion à partir d’Internet

Quand un ordinateur tente de se connecter à votre serveur de base de données à partir d’Internet, le pare-feu vérifie d’abord l’adresse IP d’origine de la demande par rapport aux règles de pare-feu au niveau de la base de données, pour la base de données requise par la connexion :

* Si l’adresse IP de la demande appartient à une des plages spécifiées dans les règles de pare-feu au niveau de la base de données, la connexion est accordée à votre base de données SQL contenant la règle.
* Si l’adresse IP de la demande n’appartient pas à une des plages spécifiées dans la règle de pare-feu au niveau de la base de données, les règles de pare-feu au niveau du serveur sont vérifiées. Si l’adresse IP de la demande appartient à une des plages spécifiées dans les règles de pare-feu au niveau du serveur, la connexion est accordée. Les règles de pare-feu au niveau du serveur s’appliquent à toutes les bases de données SQL sur le serveur Azure SQL.  
* Si l’adresse IP de la demande n’appartient pas aux plages spécifiées dans des règles de pare-feu au niveau du serveur ou de la base de données, la demande de connexion échoue.

> [!NOTE]
> Pour accéder à Azure SQL Database à partir de votre ordinateur local, vérifiez que le pare-feu sur votre réseau et l’ordinateur local autorise les communications sortantes sur le port TCP 1433.
> 

### <a name="connecting-from-azure"></a>Connexion à partir d’Azure
Pour autoriser des applications d’Azure à se connecter à Azure SQL Server, les connexions Azure doivent être activées. Quand une application à partir d’Azure tente de se connecter à votre serveur de base de données, le pare-feu vérifie que les connexions Azure sont autorisées. Un paramètre de pare-feu avec des adresses de début et de fin égales à 0.0.0.0 indique que ces connexions sont autorisées. Si la tentative de connexion n’est pas autorisée, la demande n’atteint pas le serveur Azure SQL Database.

> [!IMPORTANT]
> Cette option configure le pare-feu pour autoriser toutes les connexions à partir d’Azure, notamment les connexions issues des abonnements d’autres clients. Lorsque vous sélectionnez cette option, vérifiez que votre connexion et vos autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.
> 

## <a name="creating-and-managing-firewall-rules"></a>Création et gestion des règles de pare-feu
Le premier paramètre de pare-feu au niveau du serveur peut être créé à l’aide du [portail Azure](https://portal.azure.com/) ou par programmation avec [Azure PowerShell](https://msdn.microsoft.com/library/azure/dn546724.aspx), [l’interface de ligne de commande Azure](/cli/azure/sql/server/firewall-rule#create) ou [l’API REST](https://msdn.microsoft.com/library/azure/dn505712.aspx). Les règles de pare-feu au niveau du serveur suivantes peuvent être créées et gérées à l’aide de ces méthodes, et par le biais de Transact-SQL. 

> [!IMPORTANT]
> Les règles de pare-feu au niveau de la base de données ne peuvent être créées et gérées qu’avec Transact-SQL. 
>

Pour améliorer les performances, les règles de pare-feu au niveau du serveur sont temporairement mises en cache au niveau de la base de données. Pour actualiser le cache, consultez [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). 

> [!TIP]
> Vous pouvez utiliser l’[Audit Azure SQL Database](sql-database-auditing.md) pour vérifier des modifications de pare-feu au niveau serveur et au niveau base de données.
>

### <a name="azure-portal"></a>Portail Azure

Pour définir une règle de pare-feu au niveau du serveur dans le portail Azure, vous pouvez accéder à la page de présentation de votre base de données Azure SQL Database ou de votre serveur logique Azure Database.

> [!TIP]
> Pour obtenir un didacticiel, consultez [Créer une base de données à l’aide du portail Azure](sql-database-get-started-portal.md).
>

**À partir de la page de présentation de la base de données**

1. Pour définir une règle de pare-feu au niveau du serveur à partir de la page de présentation de la base de données, cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils, comme illustré dans l’image suivante : la page **Paramètres de pare-feu** du serveur SQL Database s’ouvre.

      ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils pour ajouter l’adresse IP de l’ordinateur que vous utilisez, puis cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle.

      ![définir la règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

**À partir de la page de présentation du serveur**

La page de présentation de votre serveur s’ouvre, affiche le nom de serveur complet (tel que **mynewserver20170403.database.windows.net**) et fournit des options pour poursuivre la configuration.

1. Pour définir une règle au niveau du serveur à partir de la page de présentation du serveur, cliquez sur **Pare-feu** dans le menu de gauche sous Paramètres, comme illustré dans l’image suivante : 

     ![présentation du serveur logique](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils pour ajouter l’adresse IP de l’ordinateur que vous utilisez, puis cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle.

     ![définir la règle de pare-feu de serveur](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

### <a name="transact-sql"></a>Transact-SQL
| Vue de catalogue ou procédure stockée | Niveau | Description |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Serveur |Affiche les règles de pare-feu au niveau du serveur actuelles |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Serveur |Crée ou met à jour les règles de pare-feu au niveau du serveur |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Serveur |Supprime des règles de pare-feu au niveau du serveur |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Base de données |Affiche les règles de pare-feu actuelles au niveau de la base de données |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Base de données |Crée ou met à jour les règles de pare-feu au niveau de la base de données |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bases de données |Supprime les règles de pare-feu au niveau de la base de données |


Les exemples suivants examinent les règles existantes, activent une plage d’adresses IP sur le serveur Contoso et suppriment une règle de pare-feu :
   
```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```
  
Ensuite, ajoutez une règle de pare-feu.
   
```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Pour supprimer une règle de pare-feu au niveau du serveur, exécutez la procédure stockée sp_delete_firewall_rule. L’exemple suivant supprime la règle nommée ContosoFirewallRule :
   
```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```   

### <a name="azure-powershell"></a>Azure PowerShell
| Applet de commande | Niveau | Description |
| --- | --- | --- |
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) |Serveur |Retourne les règles de pare-feu au niveau du serveur actuelles |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) |Serveur |Crée une règle de pare-feu au niveau du serveur |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) |Serveur |Met à jour les propriétés d’une règle de pare-feu au niveau du serveur existante |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) |Serveur |Supprime des règles de pare-feu au niveau du serveur |


L’exemple suivant définit une règle de pare-feu au niveau du serveur à l’aide de PowerShell :

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Pour obtenir des exemples PowerShell dans le contexte d’un démarrage rapide, consultez [Créer une base de données à l’aide de PowerShell](sql-database-get-started-powershell.md) et [Créer une base de données SQL unique et configurer une règle de pare-feu avec PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
>

### <a name="azure-cli"></a>Interface de ligne de commande Azure
| Applet de commande | Niveau | Description |
| --- | --- | --- |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create) | Crée une règle de pare-feu pour autoriser l’accès à toutes les instances SQL Database sur le serveur à partir de la plage d’adresses IP entrée.|
| [az sql server firewall delete](/cli/azure/sql/server/firewall-rule#delete)| Supprime une règle de pare-feu.|
| [az sql server firewall list](/cli/azure/sql/server/firewall-rule#list)| Répertorie les règles de pare-feu.|
| [az sql server firewall rule show](/cli/azure/sql/server/firewall-rule#show)| Affiche les détails d’une règle de pare-feu.|
| [ax sql server firewall rule update](/cli/azure/sql/server/firewall-rule#update)| Met à jour une règle de pare-feu.

L’exemple suivant définit une règle de pare-feu au niveau du serveur à l’aide de l’interface de ligne de commande Azure : 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Pour obtenir un exemple d’interface de ligne de commande Azure dans le contexte d’un démarrage rapide, consultez [Créer une base de données SQL Azure à l’aide de l’interface de ligne de commande Azure](sql-database-get-started-cli.md) et [Créer une base de données unique et configurer une règle de pare-feu à l’aide de l’interface de ligne de commande Azure](scripts/sql-database-create-and-configure-database-cli.md)
>

### <a name="rest-api"></a>API REST
| API | Niveau | Description |
| --- | --- | --- |
| [Répertorier les règles de pare-feu](https://msdn.microsoft.com/library/azure/dn505715.aspx) |Serveur |Affiche les règles de pare-feu au niveau du serveur actuelles |
| [Créer une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505712.aspx) |Serveur |Crée ou met à jour les règles de pare-feu au niveau du serveur |
| [Obtenir une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505707.aspx) |Serveur |Met à jour les propriétés d’une règle de pare-feu au niveau du serveur existante |
| [Supprimer une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505706.aspx) |Serveur |Supprime des règles de pare-feu au niveau du serveur |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Règle de pare-feu au niveau du serveur par rapport à une règle de pare-feu au niveau de la base de données
Q : Les utilisateurs d’une base de données doivent-ils être totalement isolés d’une autre base de données ?   
  Si oui, accordez l’accès à l’aide de règles de pare-feu au niveau de la base de données. Cela évite d’utiliser des règles de pare-feu au niveau du serveur, qui autorisent l’accès à travers le pare-feu à toutes les bases de données, ce qui réduit la profondeur de vos défenses.   
 
Q : Les utilisateurs à l’adresse IP ont-ils besoin d’accéder à toutes les bases de données ?   
  Utilisez des règles de pare-feu au niveau du serveur pour réduire le nombre de fois où vous devez configurer des règles de pare-feu.   

Q : La personne ou l’équipe qui configure les règles de pare-feu y a-t-elle seulement accès par le biais du Portail Azure, de PowerShell ou de l’API REST ?   
  Vous devez utiliser des règles de pare-feu au niveau du serveur. Les règles de pare-feu au niveau de la base de données ne peuvent être configurées qu’avec Transact-SQL.  

Q : La personne ou l’équipe qui configure les règles de pare-feu a-t-elle l’interdiction de disposer d’une autorisation globale au niveau de la base de données ?   
  Utilisez des règles de pare-feu au niveau du serveur. La configuration des règles de pare-feu au niveau de la base de données avec Transact-SQL requiert au moins l’autorisation `CONTROL DATABASE` au niveau de la base de données.  

Q : La personne ou l’équipe qui configure ou vérifie les règles de pare-feu gère-t-elle de façon centralisée les règles de pare-feu pour un grand nombre de bases de données (par exemple plusieurs centaines) ?   
  Cette sélection dépend de vos besoins et de votre environnement. Les règles de pare-feu au niveau du serveur peuvent être plus faciles à configurer, mais des scripts peuvent configurer les règles au niveau de la base de données. Et, même si vous utilisez des règles de pare-feu au niveau du serveur, vous devrez peut-être vérifier les règles de pare-feu au niveau de la base de données, afin de déterminer si les utilisateurs disposant de l’autorisation `CONTROL` sur la base de données ont créé des règles de pare-feu au niveau de la base de données.   

Q : Puis-je utiliser à la fois des règles de pare-feu au niveau du serveur et des règles de pare-feu au niveau de la base de données ?   
  Oui. Certains utilisateurs, par exemple les administrateurs, peuvent avoir besoin des règles de pare-feu au niveau du serveur. D’autres utilisateurs, tels que les utilisateurs d’une application de base de données, peuvent avoir besoin de règles de pare-feu au niveau de la base de données.   

## <a name="troubleshooting-the-database-firewall"></a>Dépannage du pare-feu de base de données
Considérez les points suivants quand l’accès au service Microsoft Azure SQL Database est anormal :

* **Configuration du pare-feu local :** pour que votre ordinateur puisse accéder à Azure SQL Database, vous devez créer une exception de pare-feu sur votre ordinateur pour le port TCP 1433. Vous devrez peut-être ouvrir des ports supplémentaires si vous effectuez des connexions dans la limite du cloud Azure. Pour plus d’informations, consultez la section **SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
* **Traduction d’adresses réseau (NAT) :** en raison du protocole NAT, l’adresse IP utilisée par votre ordinateur pour se connecter à la base de données SQL Azure peut être différente de l’adresse IP affichée dans les paramètres de configuration IP de votre ordinateur. Pour afficher l’adresse IP qu’utilise votre ordinateur pour se connecter à Azure, connectez-vous au portail et accédez à l’onglet **Configurer** sur le serveur qui héberge votre base de données. Dans la section **Adresses IP autorisées**, l’**adresse IP du client actif** s’affiche. Cliquez sur **Ajouter** **aux adresses IP autorisées** pour que cet ordinateur puisse accéder au serveur.
* **Les modifications apportées à la liste d’approbation n’ont pas encore pris effet :** jusqu’à cinq minutes peuvent s’écouler avant que les modifications apportées à la configuration du pare-feu de la base de données SQL Azure SQL ne soient effectives.
* **La connexion n’est pas autorisée ou un mot de passe incorrect a été utilisé :** si une connexion n’a pas d’autorisations sur le serveur de la base de données SQL Azure ou que le mot de passe est incorrect, la connexion au serveur de la base de données SQL Azure est refusée. Créer un paramètre de pare-feu permet uniquement aux clients de tenter de se connecter à votre serveur ; chaque client doit fournir les informations d’identification de sécurité nécessaires. Pour plus d’informations sur la préparation des connexions, consultez Gestion des bases de données, des connexions et des utilisateurs dans Azure SQL Database.
* **Adresse IP dynamique :** si vous avez une connexion Internet avec adressage IP dynamique et que le pare-feu demeure infranchissable, vous pouvez essayer une des solutions suivantes :
  
  * Demandez à votre fournisseur de services Internet (ISP) la plage d’adresses IP affectée à vos ordinateurs clients qui accèdent au serveur Azure SQL Database, puis ajoutez cette plage en tant que règle de pare-feu.
  * Obtenez un adressage IP statique à la place pour vos ordinateurs clients, puis ajoutez les adresses IP en tant que règles de pare-feu.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir un démarrage rapide sur la création d’une base de données et d’une règle de pare-feu au niveau du serveur, consultez [Créer une base de données Azure SQL Database](sql-database-get-started-portal.md).
- Pour obtenir de l’aide afin de vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, consultez [Exemples de code de démarrage rapide client pour Base de données SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Pour plus d’informations sur d’autres ports que vous devrez peut-être ouvrir, consultez la section **SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- Pour obtenir une vue d’ensemble de la sécurité Azure SQL Database, consultez [Sécurisation de votre base de données](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

