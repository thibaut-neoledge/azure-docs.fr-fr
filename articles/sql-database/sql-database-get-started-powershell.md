---
title: "Powershell : Prise en main de la base de données SQL Azure | Microsoft Docs"
description: "Découvrez comment créer un serveur logique SQL Database, une règle de pare-feu de niveau serveur et des bases de données à l’aide de PowerShell. Vous apprendrez également à interroger des bases de données à l’aide de PowerShell."
keywords: "créer une base de données sql, configuration de base de données"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 12/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 7f1983e871ea425a095e3a4bf6c7a1b89d6f45a9
ms.openlocfilehash: 51ed691eb206f284cb8bbd682c723f87362caa33


---

# <a name="tutorial-provision-and-access-an-azure-sql-database-using-powershell"></a>Didacticiel : approvisionner une base de données Azure SQL Database et y accéder à l’aide de PowerShell

Ce didacticiel de mise en route vous explique comment utiliser PowerShell pour :

* créer un groupe de ressources Azure ;
* créer un serveur logique Azure SQL ;
* visualiser les propriétés de serveur SQL Azure ;
* créer une règle de pare-feu au niveau du serveur ;
* créer l’exemple de base de données AdventureWorksLT sous la forme d’une base de données unique ;
* visualiser les propriétés de l’exemple de base de données AdventureWorksLT ;
* créer une base de données unique vide.

Ce didacticiel vous apprend également à effectuer les opérations suivantes :

* se connecter au serveur logique et à sa base de données master ;
* afficher les propriétés de la base de données master ;
* se connecter à l’exemple de base de données ;
* afficher les propriétés de la base de données utilisateur.

Lorsque vous aurez terminé ce didacticiel, vous aurez un exemple de base de données et une base de données vide en cours d’exécution dans un groupe de ressources Azure et attachés à un serveur logique. Vous aurez également une règle de pare-feu de niveau serveur configurée pour permettre au principal de niveau serveur de se connecter au serveur à partir d’une adresse IP spécifiée (ou d’une plage d’adresses IP). 

**Durée estimée** : ce didacticiel vous prendra environ 30 minutes (à condition que vous remplissiez déjà les conditions préalables).


> [!TIP]
> Vous pouvez exécuter les mêmes tâches dans un didacticiel de prise en main à l’aide du [Portail Azure](sql-database-get-started.md).
>


## <a name="prerequisites"></a>Composants requis

* Vous avez besoin d’un compte Azure. Vous pouvez [ouvrir un compte Azure gratuit](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de l’abonnement à Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Vous devez être connecté à l’aide d’un compte qui est membre du rôle Propriétaire de l’abonnement ou du rôle Collaborateur. Pour plus d’informations sur l’utilisation du contrôle d’accès en fonction du rôle (RBAC), consultez [Prise en main de la gestion des accès dans le portail Azure](../active-directory/role-based-access-control-what-is.md).

* Vous devez disposer du fichier d’exemple de base de données AdventureWorksLT .bacpac dans le service Stockage Blob Azure.

### <a name="download-the-adventureworkslt-sample-database-bacpac-file-and-save-it-in-azure-blob-storage"></a>Télécharger le fichier d’exemple de base de données AdventureWorksLT .bacpac et l’enregistrer dans Stockage Blob Azure

Ce didacticiel crée une base de données AdventureWorksLT en important un fichier .bacpac à partir du service Stockage Azure. La première étape consiste à obtenir une copie du fichier AdventureWorksLT.bacpac, puis à la charger dans Stockage Blob.
Les étapes ci-après préparent l’importation de l’exemple de base de données :

1. [Téléchargez le fichier AdventureWorksLT.bacpac](https://sqldbbacpacs.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac) et enregistrez-le avec l’extension de fichier .bacpac.
2. [Créez un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) : vous pouvez créer le compte de stockage avec les paramètres par défaut.
3. Créez un **conteneur** en accédant au compte de stockage, sélectionnez **Objets blob**, puis cliquez sur **+Conteneur**.
4. Chargez le fichier .bacpac dans le conteneur d’objets blob de votre compte de stockage. Pour effectuer cette opération, vous pouvez utiliser le bouton **Télécharger** en haut de la page du conteneur ou [utiliser la commande AzCopy](../storage/storage-use-azcopy.md#blob-upload). 
5. Après l’enregistrement du fichier AdventureWorksLT.bacpac, vous devrez disposer de l’URL et de la clé du compte de stockage pour l’extrait de code d’importation dans la suite de ce didacticiel. 
   * Sélectionnez votre fichier bacpac et copiez l’URL. Cette URL sera semblable à https://{nom-du-compte-de-stockage}.blob.core.windows.net/{nom-du-conteneur}/AdventureWorksLT.bacpac. Dans la page du compte de stockage, cliquez sur **Clés d’accès**, puis copiez **key1**.


[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="create-a-new-logical-sql-server-using-azure-powershell"></a>Créer un serveur SQL logique à l’aide d’Azure PowerShell

Vous devez disposer d’un groupe de ressources destiné à contenir le serveur. La première étape consiste donc soit à créer un groupe de ressources et un serveur ([New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroup), [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver)), soit à obtenir des références à un groupe de ressources et à un serveur existants ([Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/get-azurermresourcegroup), [Get-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserver)).
Les extraits de code ci-après créent un groupe de ressources et un serveur SQL Azure s’ils n’existent pas encore :

Pour découvrir la liste des emplacements Azure valides et le format de chaîne approprié, consultez la section [Extraits de code d’assistance](#helper-snippets) ci-dessous.
```
# Create new, or get existing resource group
############################################

$resourceGroupName = "{resource-group-name}"
$resourceGroupLocation = "{resource-group-location}"

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-Output "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-Output "Resource group $resourceGroupName already exists:"
}
$myResourceGroup



# Create a new, or get existing server
######################################

$serverName = "{server-name}"
$serverVersion = "12.0"
$serverLocation = $resourceGroupLocation
$serverResourceGroupName = $resourceGroupName

$serverAdmin = "{server-admin}"
$serverAdminPassword = "{server-admin-password}"

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-Output "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-Output "SQL server $serverName already exists:"
}
$myServer

```


## <a name="view-the-logical-sql-server-properties-using-azure-powershell"></a>Visualiser les propriétés de serveur SQL logique à l’aide d’Azure PowerShell

```
#$serverResourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin
```


## <a name="create-a-server-level-firewall-rule-using-azure-powershell"></a>Créer une règle de pare-feu au niveau du serveur à l’aide d’Azure PowerShell

Vous devez connaître votre adresse IP publique pour définir la règle de pare-feu. Vous pouvez obtenir votre adresse IP à l’aide d’un navigateur de votre choix (demandez « quelle est mon adresse IP »). Pour plus d’informations, consultez l’article relatif aux [règles de pare-feu](sql-database-firewall-configure.md).

Le code ci-après utilise les applets de commande [Get-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserverfirewallrule) et [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserverfirewallrule) pour obtenir une référence ou pour créer une règle. Si la règle existe déjà, cet extrait de code obtient uniquement une référence à cette règle et ne met pas à jour les adresses IP de début et de fin. Vous pouvez toujours modifier la clause **else** afin d’utiliser l’applet de commande [Set-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverfirewallrule) pour une fonctionnalité de création ou de mise à jour.

> [!NOTE] 
> Vous pouvez ouvrir le pare-feu SQL Database sur le serveur pour une adresse IP unique ou un ensemble d’adresses. Ouvrir le pare-feu permet aux administrateurs et utilisateurs SQL de se connecter à toute base de données sur le serveur pour laquelle ils disposent d’informations d’identification valides.
>

```
#$serverName = "{server-name}"
#$serverResourceGroupName = "{resource-group-name}"
$serverFirewallRuleName = "{server-firewall-rule-name}"
$serverFirewallStartIp = "{server-firewall-rule-startIp}"
$serverFirewallEndIp = "{server-firewall-rule-endIp}"

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule
```


## <a name="connect-to-sql-server-using-azure-powershell"></a>Se connecter au serveur SQL à l’aide d’Azure PowerShell

Exécutons une requête rapide vers la base de données MASTER pour vérifier que nous pouvons nous connecter au serveur. L’extrait de code ci-après utilise le [fournisseur .NET Framework pour SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) pour se connecter et interroger la base de données MASTER du serveur. Il génère une chaîne de connexion basée sur les variables que nous avons utilisées dans les extraits de code précédents. Remplacez les espaces réservés par le nom d’administrateur et le mot de passe du serveur SQL que vous avez utilisés pour créer le serveur aux étapes précédentes.


```
#$serverName = "{server-name}"
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"



$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```


## <a name="create-new-adventureworkslt-sample-database-using-azure-powershell"></a>Créer un exemple de base de données AdventureWorksLT à l’aide d’Azure PowerShell

L’extrait de code ci-après importe un fichier bacpac de l’exemple de base de données AdventureWorksLT à l’aide de l’applet de commande [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabaseimport). Le fichier bacpac se trouve dans Stockage Blob Azure. Après avoir exécuté l’applet de commande d’importation, vous pouvez surveiller la progression de l’opération d’importation à l’aide de l’applet de commande [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabaseimportexportstatus).
La propriété $storageUri est la propriété d’URL du fichier bacpac que vous avez chargé vers le portail précédemment, et doit être semblable à ceci : https://{compte-de-stockage}.blob.core.windows.net/{conteneur}/AdventureWorksLT.bacpac.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$databaseName = "AdventureWorksLT"
$databaseEdition = "Basic"
$databaseServiceLevel = "Basic"

$storageKeyType = "StorageAccessKey"
$storageUri = "{storage-uri}" # URL of bacpac file you uploaded to your storage account
$storageKey = "{storage-key}" # key1 in the Access keys setting of your storage account

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000


Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus
```



## <a name="view-database-properties-using-azure-powershell"></a>Visualiser les propriétés de la base de données à l’aide d’Azure PowerShell

Après avoir créé la base de données, visualisez certaines de ses propriétés.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
#$databaseName = "{database-name}"

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName


Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status
```

## <a name="connect-and-query-the-sample-database-using-azure-powershell"></a>Se connecter et interroger l’exemple de base de données à l’aide d’Azure PowerShell

Exécutons une requête rapide vers la base de données AdventureWorksLT pour vérifier que nous pouvons nous connecter. L’extrait de code ci-après utilise le [fournisseur .NET Framework pour SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) pour se connecter et interroger la base de données. Il génère une chaîne de connexion basée sur les variables que nous avons utilisées dans les extraits de code précédents. Remplacez l’espace réservé par le mot de passe d’administrateur du serveur SQL.

```
#$serverName = {server-name}
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
#$databaseName = {database-name}

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```

## <a name="create-a-new-blank-database-using-azure-powershell"></a>Créer une base de données vide à l’aide d’Azure PowerShell

```
#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```


## <a name="complete-azure-powershell-script-to-create-a-server-firewall-rule-and-database"></a>Exécuter un script Azure PowerShell pour créer un serveur, une règle de pare-feu et une base de données



```
# Sign in to Azure and set the subscription to work with
########################################################

$SubscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myResourceGroupLocation = "{resource-group-location}"

$myServerName = "{server-name}"
$myServerVersion = "12.0"
$myServerLocation = $myResourceGroupLocation
$myServerResourceGroupName = $myResourceGroupName
$myServerAdmin = "{server-admin}"
$myServerAdminPassword = "{server-admin-password}" 

$myServerFirewallRuleName = "{server-firewall-rule-name}"
$myServerFirewallStartIp = "{start-ip}"
$myServerFirewallEndIp = "{end-ip}"

$myDatabaseName = "AdventureWorksLT"
$myDatabaseEdition = "Basic"
$myDatabaseServiceLevel = "Basic"

$myStorageKeyType = "StorageAccessKey"
# Get these values from your Azure storage account:
$myStorageUri = "{http://your-storage-account.blob.core.windows.net/your-container/AdventureWorksLT.bacpac}"
$myStorageKey = "{your-storage-key}"


# Create new, or get existing resource group
############################################

$resourceGroupName = $myResourceGroupName
$resourceGroupLocation = $myResourceGroupLocation

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-host "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-host "Resource group $resourceGroupName already exists:"
}
$myResourceGroup


# Create a new, or get existing server
######################################

$serverName = $myServerName
$serverVersion = $myServerVersion
$serverLocation = $myServerLocation
$serverResourceGroupName = $myServerResourceGroupName

$serverAdmin = $myServerAdmin
$serverAdminPassword = $myServerAdminPassword

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-host "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-host "SQL server $serverName already exists:"
}
$myServer


# View server properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin


# Create or update server firewall rule
#######################################

$serverFirewallRuleName = $myServerFirewallRuleName
$serverFirewallStartIp = $myServerFirewallStartIp
$serverFirewallEndIp = $myServerFirewallEndIp

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule


# Connect to the server and master database
###########################################
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create the AdventureWorksLT database from a bacpac
####################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$databaseName = $myDatabaseName
$databaseEdition = $myDatabaseEdition
$databaseServiceLevel = $myDatabaseServiceLevel

$storageKeyType = $myStorageKeyType
$storageUri = $myStorageUri
$storageKey = $myStorageKey

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000

Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus


# View database properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseName

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status


# Query the database
####################

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create a blank database
#########################

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```



> [!TIP]
> Vous pouvez économiser de l’argent pendant votre apprentissage en supprimant des bases de données que vous n’utilisez pas. Les bases de données de l’édition De base peuvent être restaurées dans les 7 jours. Toutefois, ne supprimez pas de serveur. Si vous le faites, vous ne pourrez pas récupérer le serveur ou l’une de ses bases de données supprimées.

## <a name="helper-snippets"></a>Extraits de code d’assistance

```
# Get a list of Azure regions where you can create SQL resources

$sqlRegions = (Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" })
foreach ($region in $sqlRegions)
{
   $region.Location
}

# Clean up
# Delete a resource group (and all contained resources)
Remove-AzureRmResourceGroup -Name {resource-group-name}
```

> [!TIP]
> Vous pouvez économiser de l’argent pendant votre apprentissage en supprimant des bases de données que vous n’utilisez pas. Pour les bases de données de l’édition de base, vous pouvez les restaurer dans les sept jours. Toutefois, ne supprimez pas le serveur. Si vous le faites, vous ne pourrez pas récupérer le serveur ou l’une de ses bases de données supprimées.
>

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce premier didacticiel de mise en route et que vous avez créé une base de données avec certains exemples de données, vous souhaiterez peut-être explorer les nombreux autres didacticiels afin de mettre en œuvre ce que vous avez appris dans ce didacticiel. 

- Pour un didacticiel de prise en main de l’authentification SQL Server, voir [Authentification et autorisation SQL](sql-database-control-access-sql-authentication-get-started.md).
- Pour un didacticiel de prise en main de l’authentification Azure Active Directory, voir [Authentification et autorisation Azure AD](sql-database-control-access-aad-authentication-get-started.md).
* Si vous souhaitez interroger l’exemple de base de données dans le portail Azure, consultez [Public preview: Interactive query experience for SQL databases (Version préliminaire publique : expérience interactive pour les requêtes portant sur les bases de données SQL)](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/).
* Si vous connaissez Excel, découvrez comment [vous connecter à une base de données SQL dans Azure avec Excel](sql-database-connect-excel.md).
* Si vous êtes prêt à commencer le codage, choisissez votre langage de programmation dans [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md).
* Si vous souhaitez déplacer vos bases de données SQL Server locales vers Azure, consultez la page [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).
* Si vous souhaitez charger des données dans une nouvelle table à partir d’un fichier CSV avec l’outil de ligne de commande BCP, consultez la page [Chargement de données dans une base de données SQL à partir d’un fichier CSV à l’aide de BCP](sql-database-load-from-csv-with-bcp.md).
* Si vous souhaitez commencer à créer des tables et d’autres objets, consultez la rubrique « Création d’une table » dans [Création d’une table](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Ressources supplémentaires
[Définition de la base de données SQL](sql-database-technical-overview.md)



<!--HONumber=Feb17_HO1-->


