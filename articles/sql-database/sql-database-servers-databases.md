---
title: "Créer et gérer des serveurs et des bases de données SQL Azure | Microsoft Docs"
description: "Découvrez les concepts de serveur et de base de données Azure SQL Database, ainsi que la création et la gestion des serveurs et des bases de données à l’aide du portail Azure, de PowerShell, de l’interface Azure CLI, de Transact-SQL et de l’API REST."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/05/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: d25ffba701ff772899552bf6a5023fa36e28059d
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---

# <a name="create-and-manage-azure-sql-database-servers-and-databases"></a>Créer et gérer des serveurs et des bases de données Azure SQL Database

Une base de données SQL Azure est une base de données gérée dans Microsoft Azure, créée dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md). Cette base de données possède un ensemble défini de [ressources de calcul et de stockage pour différentes charges de travail](sql-database-service-tiers.md). Une base de données SQL Azure est associée à un serveur logique Azure SQL Database. Ce serveur est créé dans une région Azure spécifique. 

## <a name="an-azure-sql-database-can-be-a-single-pooled-or-partitioned-database"></a>Une base de données SQL Azure peut être une base de données unique, regroupée ou partitionnée

Une base de données SQL Azure peut être :

- Une base de données unique avec son [propre ensemble de ressources](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU)
- Une partie d’un [pool élastique SQL](sql-database-elastic-pool.md) qui [partage un ensemble de ressources](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU)
- Une partie d’un [ensemble de bases de données partitionnées dont la taille des instances a été augmentée](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling). Il peut s’agir de bases de données uniques ou mises en pool
- Une partie d’un ensemble de bases de données participant à un [modèle de conception SaaS partagé au sein d’une architecture mutualisée ](sql-database-design-patterns-multi-tenancy-saas-applications.md), et dont les bases de données peuvent être uniques ou mises en pool (ou les deux) 

> [!TIP]
> Pour les noms de base de données valides, consultez [Database Identifiers](https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). 
>
 
- Le classement par défaut utilisé par la base de données SQL Microsoft Azure est **SQL_LATIN1_GENERAL_CP1_CI_AS**, où **LATIN1_GENERAL** correspond à l’anglais (États-Unis) et **CP1** à la page de code 1252. La propriété **CI** indique que le classement n’est pas sensible à la casse, et **AS** qu’il respecte les accents. Pour plus d’informations sur la définition du classement, consultez [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).
- La base de données SQL Microsoft Azure prend en charge les versions 7.3 et ultérieures du client de protocole TDS (Tabular Data Stream).
- Seules les connexions TCP/IP sont autorisées.

## <a name="what-is-an-azure-sql-logical-server"></a>Qu’est-ce qu’un serveur logique SQL Azure ?

Un serveur logique agit comme un point d’administration central pour plusieurs bases de données, y compris les [connexions](sql-database-elastic-pool.md)de [pools élastiques SQL](sql-database-manage-logins.md), les [règles de pare-feu](sql-database-firewall-configure.md), les [règles d’audit](sql-database-auditing.md), les [stratégies de détection des menaces](sql-database-threat-detection.md) et les [groupes de basculement](sql-database-geo-replication-overview.md). Un serveur logique peut se trouver dans une région différente de celle de son groupe de ressources. Avant de pouvoir créer la base de données SQL Azure, vous devez déjà posséder un serveur logique. Toutes les bases de données sur un serveur sont créées au sein de la même région que le serveur logique. 


> [!IMPORTANT]
> Dans SQL Database, un serveur est une construction logique distincte d’une instance SQL Server que vous connaissez peut-être dans le monde local. Plus précisément, le service SQL Database n’offre aucune garantie en ce qui concerne l’emplacement des bases de données par rapport à leurs serveurs logiques et ne présente aucun accès ni aucune fonctionnalité au niveau de l’instance.
> 

Lorsque vous créez un serveur logique, vous fournissez un compte de connexion du serveur et un mot de passe disposant de droits d’administration pour la base de données MASTER sur ce serveur, ainsi que sur l’ensemble des bases de données qui y sont créées. Ce compte initial est un compte de connexion SQL. Azure SQL Database prend en charge l’authentification SQL et l’authentification Azure Active Directory. Pour en savoir plus sur les connexions et l’authentification, consultez la page [Managing Databases and Logins in Azure SQL Database](sql-database-manage-logins.md) (Gérer les bases de données et les connexions dans Azure SQL Database). L’authentification Windows n’est pas prise en charge. 

> [!TIP]
> Pour connaître les noms de groupe de ressources et de serveur valides, consultez la page [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom).
>

Un serveur logique de base de données Azure :

- Est créé dans un abonnement Azure, mais peut être déplacé avec les ressources qu’il contient vers un autre abonnement
- Est la ressource parente pour les bases de données, les pools élastiques et les entrepôts de données
- Fournit un espace de noms pour les bases de données, les pools élastiques et les entrepôts de données
- Est un conteneur logique qui possède une forte sémantique de durée de vie : si vous supprimez un serveur, le serveur logique supprimera les bases de données, les pools élastiques et les entrepôts de données qu’il contient
- Fait partie d’un [contrôle d’accès en fonction du rôle Azure (RBAC)](/active-directory/role-based-access-control-what-is.md) : les bases de données, les pools élastiques et les entrepôts de données d’un serveur héritent des droits d’accès du serveur
- Est un élément de poids dans l’identité des bases de données, des pools élastiques et des entrepôts de données à des fins de gestion des ressources Azure (voir le schéma d’URL pour les bases de données et les pools)
- Colocalise les ressources d’une région
- Fournit un point de terminaison de connexion pour l’accès aux bases de données (<serverName>. database.windows.net)
- Fournit l’accès aux métadonnées concernant l’accès aux ressources contenues via les DMV en vous connectant à une base de données MASTER 
- Fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexions, pare-feu, audit, détection des menaces, etc. 
- Est limité par un quota dans l’abonnement parent (six serveurs par abonnement par défaut ; [consultez les Limites d’abonnement ici](../azure-subscription-service-limits.md))
- Fournit l’étendue du quota de base de données et du quota DTU pour les ressources qu’il contient (par exemple, 45 000 DTU)
- Est la portée du contrôle de version pour les fonctionnalités activées sur les ressources qu’il contient 
- Les connexions principales au niveau du serveur peuvent gérer toutes les bases de données sur un serveur
- Peut contenir des connexions semblables à celles des instances de SQL Server en local qui ont accès à une ou plusieurs bases de données sur le serveur et qui peuvent se voir octroyer des droits d’administration limités. Pour plus d’informations, consultez [Connexions](sql-database-manage-logins.md).

## <a name="azure-sql-databases-protected-by-sql-database-firewall"></a>Bases de données SQL Azure protégées par un pare-feu SQL Database

Pour aider à protéger vos données, le [pare-feu SQL Database](sql-database-firewall-configure.md) empêche tout accès à votre serveur de base de données ou à l’une de ses bases de données en dehors de votre connexion au serveur, directement par le biais de votre connexion à un abonnement Azure. Pour activer la connectivité supplémentaire, vous devez [créer une ou plusieurs règles de pare-feu](sql-database-firewall-configure.md#creating-and-managing-firewall-rules). Pour créer et gérer les pools élastiques SQL, voir [Pools élastiques](sql-database-elastic-pool.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Gérer les serveurs, les bases de données et les pare-feu SQL Azure à l’aide du portail Azure

Vous pouvez créer le groupe de ressources de la base de données SQL Azure en avance ou lors de la création du serveur lui-même. Il existe plusieurs méthodes pour accéder à un nouveau formulaire de serveur SQL : en créant un nouveau serveur SQL ou dans le cadre de la création d’une base de données. 

### <a name="create-a-blank-sql-server-logical-server"></a>Créer un serveur SQL vide (serveur logique)

Pour créer un serveur Azure SQL Database (sans base de données) à l’aide du [portail Azure](https://portal.azure.com), accédez à un formulaire de serveur SQL vide (serveur logique). La capture d’écran suivante montre une méthode qui permet d’ouvrir un formulaire pour créer un serveur logique SQL vide. 

   ![formulaire de création de serveur logique rempli](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

Si vous accédez à ce formulaire à l’aide d’une autre méthode, les informations sur le formulaire sont identiques.

### <a name="create-a-blank-or-sample-sql-database"></a>Créer un exemple de base de données SQL ou une base de données SQL vide

Pour créer une base de données SQL Azure à l’aide du [portail Azure](https://portal.azure.com), accédez à un formulaire SQL Database vide et renseignez les informations demandées. Vous pouvez créer le groupe de ressources de la base de données SQL Azure et le serveur logique en avance ou lors de la création de la base de données elle-même. Vous pouvez créer une base de données vide ou créer un exemple de base de données reposant sur Adventure Works LT. 

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [IMPORTANT] Pour en savoir plus sur la sélection du niveau tarifaire de votre base de données, consultez la page [Niveaux de service](sql-database-service-tiers.md).
>

### <a name="manage-an-existing-sql-server"></a>Gérer un serveur SQL existant

Pour gérer un serveur existant, accédez au serveur à l’aide de plusieurs méthodes, à partir d’une page de base de données SQL spécifique, de la page **Serveurs SQL** ou de la page **Toutes les ressources**, par exemple. La capture d’écran suivante montre comment commencer à définir un pare-feu au niveau du serveur à partir de la page **Vue d’ensemble** d’un serveur. 

   ![présentation du serveur logique](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

Pour gérer une base de données existante, accédez à la page **Bases de données SQL** page, puis cliquez sur la base de données que vous souhaitez gérer. La capture d’écran suivante montre comment commencer à définir un pare-feu au niveau du serveur pour une base de données, à partir de la page **Vue d’ensemble** d’une base de données. 

   ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Pour configurer les propriétés de niveau de performance d’une base de données, consultez la page [Niveaux de service](sql-database-service-tiers.md).
>

> [!TIP]
> Pour obtenir un didacticiel de démarrage rapide du portail Azure, consultez la page[Création d’une base de données SQL Azure à l’aide du portail Azure](sql-database-get-started-portal.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Gérer les serveurs, les bases de données et les pare-feux SQL Azure à l’aide de PowerShell

Pour créer et gérer le serveur, les bases de données et les pare-feux SQL Azure avec Azure PowerShell, utilisez les applets de commande PowerShell suivants. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-azurerm-ps). Pour créer et gérer les pools élastiques SQL, voir [Pools élastiques](sql-database-elastic-pool.md).

| Applet de commande | Description |
| --- | --- |
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Crée un groupe de ressources]
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Crée un serveur|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Renvoie des informations concernant les serveurs|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.sql/set-azurermsqlserver)|Modifie les propriétés d’un serveur|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Supprime un serveur|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Crée une règle de pare-feu au niveau du serveur |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Obtient les règles de pare-feu d’un serveur|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Modifie une règle de pare-feu sur un serveur|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Supprime une règle de pare-feu d’un serveur|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crée une base de données |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtient une ou plusieurs bases de données|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Définit les propriétés d’une base de données, ou déplace une base de données existante dans un pool élastique|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Supprime une base de données|

> [!TIP]
> Pour obtenir un didacticiel de démarrage rapide de PowerShell, consultez la page [Créer une base de données SQL Azure unique à l’aide de PowerShell](sql-database-get-started-portal.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Gérer les serveurs, les bases de données et les pare-feu SQL Azure à l’aide de l’interface de ligne de commande Azure

Pour créer et gérer un serveur, des bases de données et des pare-feux SQL Azure avec [Azure CLI](/cli/azure/overview), utilisez les commandes [Azure CLI SQL Database](/cli/azure/sql/db) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows. Pour créer et gérer les pools élastiques SQL, voir [Pools élastiques](sql-database-elastic-pool.md).

| Applet de commande | Description |
| --- | --- |
|[az group create](/cli/azure/group#create)|Crée un groupe de ressources|
|[az sql server create](/cli/azure/sql/server#create)|Crée un serveur|
|[az sql server list](/cli/azure/sql/server#list)|Répertorie les serveurs|
|[az sql server list-usages](/cli/azure/sql/server#list-usages)|Renvoie les données d’utilisation d’un serveur|
|[az sql server show](/cli/azure/sql/server#show)|Obtient un serveur|
|[az sql server update](/cli/azure/sql/server#update)|Met à jour un serveur|
|[az sql server delete](/cli/azure/sql/server#delete)|Supprime un serveur.|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#create)|Crée la règle de pare-feu d’un serveur|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#list)|Répertorie les règles de pare-feu sur un serveur|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#show)|Affiche les détails d’une règle de pare-feu|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule#update)|Met à jour une règle de pare-feu|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#delete)|Supprime une règle de pare-feu|
|[az sql db create](/cli/azure/sql/db#create) |Crée une base de données|
|[az sql db list](/cli/azure/sql/db#list)|Répertorie toutes les bases de données et les entrepôts de données d’un serveur, ou toutes les bases de données d’un pool élastique|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|Répertorie les objectifs de service disponibles et les limites de stockage|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|Renvoie les données d’utilisation de la base de données|
|[az sql db show](/cli/azure/sql/db#show)|Obtient une base de données ou un entrepôt de données|
|[az sql db update](/cli/azure/sql/db#update)|Met à jour une base de données|
|[az sql db delete](/cli/azure/sql/db#delete)|Supprime une base de données|

> [!TIP]
> Pour obtenir un didacticiel de démarrage rapide d’Azure CLI, consultez la page [Créer une base de données SQL Azure unique à l’aide d’Azure CLI](sql-database-get-started-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Gérer les serveurs, les bases de données et les pare-feux SQL Azure à l’aide de Transact-SQL

Pour créer et gérer le serveur, les bases de données et les pare-feux SQL Azure avec Transact-SQL, utilisez les commandes T-SQL suivantes. Vous pouvez entrer ces commandes à l’aide du portail Azure, de [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), de [Visual Studio Code](https://code.visualstudio.com/docs), ou de tout autre programme pouvant se connecter à un serveur Azure SQL Database et transmettre des commandes Transact-SQL. Pour gérer des pools élastiques SQL, consultez la page [Pools élastiques](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Il est impossible de créer, mettre à jour ou supprimer un serveur à l’aide de Transact-SQL.
>

| Commande | Description |
| --- | --- |
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Crée ou met à jour les paramètres de pare-feu au niveau du serveur pour votre serveur SQL Database. Cette procédure stockée est uniquement disponible dans la base de données MASTER pour la connexion principale au niveau du serveur. Une règle de pare-feu au niveau du serveur peut uniquement être créée à l’aide de Transact-SQL, après que la première règle de pare-feu au niveau du serveur a été créée par un utilisateur disposant des autorisations au niveau d’Azure|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Renvoie des informations sur les paramètres de pare-feu au niveau du serveur associés à votre Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Supprime les paramètres de pare-feu au niveau du serveur de votre serveur SQL Database. Cette procédure stockée est uniquement disponible dans la base de données MASTER pour la connexion principale au niveau du serveur.|
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crée une base de données. Vous devez être connecté à la base de données MASTER pour créer une base de données.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifie une base de données SQL Azure. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modifie un entrepôt de données Azure SQL Data Warehouse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Supprime une base de données.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Crée ou met à jour les règles de pare-feu au niveau de la base de données pour votre base de données pour votre Azure SQL Database ou votre entrepôt de données SQL Data Warehouse. Les règles de pare-feu d’une base de données peuvent être configurées pour la base de données MASTER, ainsi que pour les bases de données utilisateur sur SQL Database. Les règles de pare-feu d’une base de données sont utiles lors de l’utilisation d’une base de données utilisateurs contenue. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Renvoie des informations sur les paramètres de pare-feu au niveau de la base de données associés à votre Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Supprime les paramètres de pare-feu au niveau de la base de données de votre Azure SQL Database ou de votre entrepôt de données SQL Data Warehouse. |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Renvoie l’édition (niveau de service), l’objectif de service (niveau tarifaire) et, le cas échéant, le nom du pool élastique Azure SQL Database ou d’un entrepôt de données Azure SQL Data Warehouse. Si vous êtes connecté à la base de données MASTER d’un serveur Azure SQL Database, renvoie les informations au sujet de toutes les bases de données. Pour Azure SQL Data Warehouse, vous devez être connecté à la base de données MASTER.|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Répertorie le nombre, le type et la durée des bases de données sur un serveur Azure SQL Database.|

> [!TIP]
> Pour obtenir un didacticiel de démarrage rapide à l’aide de SQL Server Management Studio sur Microsoft Windows, consultez la page [Azure SQL Database : utilisez SQL Server Management Studio pour vous connecter et exécuter des requêtes](sql-database-connect-query-ssms.md). Pour obtenir un didacticiel de démarrage rapide à l’aide de Visual Studio Code sur Mac OS, Linux ou Windows, consultez la page [Azure SQL Database : utilisez Visual Studio Code pour vous connecter et interroger des données](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Gérer les serveurs, les bases de données et les pare-feux SQL Azure à l’aide de l’API REST

Pour créer et gérer un serveur, des bases de données et des pare-feux SQL Azure à l’aide de l’API REST, consultez la page [Azure SQL Database REST API](/rest/api/sql/) (API REST d’Azure SQL Database).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le regroupement de bases de données à l’aide de pools élastiques SQL, consultez la page [Pools élastiques](sql-database-elastic-pool.md).
- Pour en savoir plus sur le service Azure SQL Database, consultez [Qu’est-ce que le service Azure SQL Database ?](sql-database-technical-overview.md).
- Pour en savoir plus sur la migration d’une base de données SQL Server, consultez la section [Migrer une base de données SQL](sql-database-cloud-migrate.md).
- Pour plus d’informations sur les fonctionnalités prises en charge, consultez la page [Fonctionnalités](sql-database-features.md).

