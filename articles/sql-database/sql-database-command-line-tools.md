<properties 
	pageTitle="Gestion des ressources de la base de données SQL Azure avec PowerShell" 
	description="Gestion de la base de données SQL Azure avec la ligne de commande" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="vinsonyu"/>

# Gestion des ressources de la base de données SQL Azure avec PowerShell


Dans cette rubrique, vous utilisez un script PowerShell pour créer un serveur logique de base de données SQL Azure, une base de données et une règle de pare-feu.

## Étape 1 : Installer le Kit de développement logiciel (SDK) d'Azure

Si ce n'est pas encore fait, utilisez les instructions décrites dans [Installation et configuration d'Azure PowerShell pour installer Azure PowerShell](../powershell-install-configure.md) sur votre ordinateur local. Ouvrez ensuite une invite de commande Azure PowerShell.


## Étape 2 : Configurer des scripts PowerShell
Ce script PowerShell crée un serveur, une base de données et une règle de pare-feu de serveur.


1. Copiez le bloc d'applets de commande PowerShell ci-dessous dans votre éditeur de texte.
		
		
		Add-AzureAccount #Only needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "<Service Admin Login>" -AdministratorLoginPassword "<ServerLoginPassword>" -Location "<Location>" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "<Database1>" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "<FirewallRuleName>" -StartIpAddress "<IP4StartRange>" -EndIpAddress "<IP4EndRange>" -verbose

2. Remplacez tous les éléments entre < >, par les valeurs souhaitées. Pour obtenir la liste des emplacements de serveur de base de données SQL Azure valides, vous pouvez exécuter les applets de commande suivantes dans votre invite de commandes Azure Powershell.

		Switch-AzureMode -Name AzureResourceManager
		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

##Étape 3 : Exécuter le script PowerShell

Passez en revue les applets de commande Azure PowerShell.

Copiez les applets de commande PowerShell configurées à l'étape 2 et collez-les dans votre invite de commande Azure PowerShell. Ceci permet d'émettre les applets de commande sous forme de série de commandes PowerShell et de créer le serveur SQL Azure, la base de données et la règle de pare-feu de serveur.

Si vous comptez créer de nouveau ces ressources SQL Azure ou des ressources similaires, vous pouvez :

- Enregistrer ceci en tant que fichier de script PowerShell (*.ps1)
- Enregistrer ceci en tant runbook d'automation Azure dans la section Automation du portail de gestion Azure 

##Exemples

Ce script PowerShell crée les ressources dans l'ouest des États-Unis

		Add-AzureAccount #Needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "admin" -AdministratorLoginPassword "P@ssword" -Location "West US" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "Database1" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "MyFirewallRule" -StartIpAddress "192.168.1.1" -EndIpAddress "192.168.1.1" -verbose

##Ressources

Pour plus d'informations sur les applets de commande PowerShell SQL Azure, [cliquez ici](https://msdn.microsoft.com/library/dn546726.aspx)

<!---HONumber=58--> 