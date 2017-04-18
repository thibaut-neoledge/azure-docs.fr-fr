---
title: "Azure PowerShell : Créer une base de données SQL | Microsoft Docs"
description: "Découvrez comment créer un serveur logique SQL Database, une règle de pare-feu au niveau du serveur et des bases de données dans le Portail Azure."
keywords: "didacticiel sur la base de données sql, créer une base de données sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 04/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7f75b57c5d409ad9c4c79c48e4b7ee0021e7846b
ms.lasthandoff: 04/12/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Créer une base de données SQL Azure unique à l’aide de PowerShell

PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide décrit comment utiliser PowerShell pour déployer une base de données SQL Azure dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) dans un [serveur logique Azure SQL Database](sql-database-features.md).

Pour suivre ce didacticiel, assurez-vous que vous avez installé la dernière version [d’Azure PowerShell](/powershell/azureps-cmdlets-docs). 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) et suivez les instructions à l’écran.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope`.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>Création d'un serveur logique

Créez un [serveur logique Azure SQL Database](sql-database-features.md) avec la commande [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). Un serveur logique contient un groupe de bases de données gérées en tant que groupe. L’exemple suivant illustre la création d’un serveur nommé de façon aléatoire dans votre groupe de ressources avec un identifiant d’administrateur nommé `ServerAdmin` et un mot de passe `ChangeYourAdminPassword1`. Remplacez les valeurs prédéfinies par ce que vous souhaitez.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurer une règle de pare-feu du serveur

Créez une [règle de pare-feu au niveau du serveur Azure SQL Database](sql-database-firewall-configure.md) avec la commande [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule). Une règle de pare-feu au niveau du serveur permet à une application externe, telle que SQL Server Management Studio ou l’utilitaire SQLCMD, de se connecter à une base de données SQL via le pare-feu du service SQL Database. Dans l’exemple suivant, le pare-feu n’est ouvert qu’à d’autres ressources Azure. Pour activer la connectivité externe, remplacez l’adresse IP par une adresse correspondant à votre environnement. Pour ouvrir toutes les adresses IP, utilisez 0.0.0.0 comme adresse IP de début et 255.255.255.255 comme adresse de fin.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pourrez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

## <a name="create-a-blank-database"></a>Créer une base de données vide

Créez une base de données SQL vide avec un [niveau de performance S0](sql-database-service-tiers.md) sur le serveur avec la commande [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase). L’exemple suivant illustre la création d’une base de données nommée `mySampleDatabase`. Remplacez cette valeur prédéfinie par ce que vous souhaitez.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous souhaitez continuer à utiliser d’autres démarrages rapides ou les didacticiels, ne nettoyez pas les ressources créées dans ce démarrage rapide. Sinon, utilisez la commande suivante pour supprimer toutes les ressources créées par ce démarrage rapide.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Étapes suivantes

- Pour vous connecter et envoyer des requêtes à l’aide de SQL Server Management Studio, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).
- Pour vous connecter et exécuter des requêtes avec Visual Studio Code, consultez la page [Se connecter et effectuer des requêtes avec Visual Studio Code](sql-database-connect-query-vscode.md).
- Pour vous connecter et exécuter des requêtes avec .NET, consultez la page [Se connecter et effectuer des requêtes avec .NET](sql-database-connect-query-dotnet.md).
- Pour vous connecter et exécuter des requêtes avec PHP, consultez la page [se connecter et effectuer des requêtes avec PHP](sql-database-connect-query-php.md).
- Pour vous connecter et exécuter des requêtes avec Node.js, consultez la page [se connecter et effectuer des requêtes avec Node.js](sql-database-connect-query-nodejs.md).
- Pour vous connecter et exécuter des requêtes avec Java, consultez la page [se connecter et effectuer des requêtes avec Java](sql-database-connect-query-java.md).
- Pour vous connecter et exécuter des requêtes avec Python, consultez la page [se connecter et effectuer des requêtes avec Python](sql-database-connect-query-python.md).
- Pour vous connecter et exécuter des requêtes avec Ruby, consultez la page [se connecter et effectuer des requêtes avec Ruby](sql-database-connect-query-ruby.md).
