<properties 
	pageTitle="Désinstaller l’outil de tâche de base de données élastique" 
	description="Désinstaller l’outil de tâche de base de données élastique" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="sidneyh"/>

# Désinstaller les composants de tâches de bases de données élastiques

Si une défaillance se produit lorsque vous tentez d'installer le service de tâche de base de données élastique, supprimez le groupe de ressources pour le service.

## Pour installer les composants de service

1. Ouvrez la [version préliminaire du portail Azure](https://ms.portal.azure.com/).
2. Accédez à l'abonnement qui contient la tâche élastique.
3. Cliquez sur **Parcourir** pus sur **Groupes de ressources**.
4. Sélectionnez le groupe de ressources intitulé « __ElasticDatabaseJob ».
5. Supprimez le groupe de ressources.

Ou utilisez ce script PowerShell :

1. Ouvrez une [fenêtre PowerShell Microsoft Azure](../powershell-install-configure.md). 
2. Vérifiez que vous utilisez PowerShell SDK 0.8.10 ou version ultérieure.
3. Exécutez le script :

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job is uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job is now uninstalled."

## Étapes suivantes

Pour réinstaller les tâches de bases de données élastiques, voir [Installation du service de tâche de base de données élastique](sql-database-elastic-jobs-service-installation.md)

Pour une vue d'ensemble du service de tâche élastique, voir [Vue d'ensemble des tâches élastiques](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-job-uninstall/
 

<!---HONumber=62-->