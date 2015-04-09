<properties 
 pageTitle="Gestion du Cache Redis Azure avec Azure PowerShell" 
 description="Découvrez comment effectuer des tâches administratives pour le Cache Redis Azure à l'aide d'Azure PowerShell." 
 services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="multiple" 
   ms.date="03/06/2015"
   ms.author="riande"/>

# Gestion du Cache Redis Azure avec Azure PowerShell

Ce didacticiel permet de démarrer rapidement avec la création, la mise à jour et la suppression d'un Cache Redis Azure.

## Configuration requise ##

Pour utiliser Windows PowerShell avec le gestionnaire des ressources, vous devez disposer des composants suivants :

- Windows PowerShell, version 3.0 ou 4.0. Pour trouver la version de Windows PowerShell, tapez : `$PSVersionTable` et vérifiez que la valeur de  `PSVersion` est 3.0 ou 4.0. Pour installer une version compatible, consultez [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).
	
- Azure PowerShell, version 0.8.0 ou ultérieure. Pour installer la dernière version et l'associer à votre abonnement Azure, consultez [Installation et configuration d'Azure PowerShell](powershell-install-configure.md).

Ce didacticiel s'adresse à ceux qui débutent avec Windows PowerShell. Pour plus d'informations sur Windows PowerShell, consultez [Prise en main de Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Pour accéder à l'aide détaillée de toute applet de commande présentée dans ce didacticiel, utilisez l'applet de commande Get-Help. 

	Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l'aide sur l'applet de commande Add-AzureAccount, tapez :

	Get-Help Add-AzureAccount -Detailed

## Script Azure PowerShell simple pour le Cache Redis  ##

Le script suivant montre la création, la mise à jour et la suppression d'un Cache Redis Azure.

		# Redis cache operations require mode set to AzureResourceManager.
		Switch-AzureMode AzureResourceManager
		$VerbosePreference = "Continue" 
		
		# Create a new cache.
		$cacheName = "MovieCache91117"
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
			$cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {       
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName         
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -MaxMemoryPolicy AllKeysLRU -Name $cacheName -ResourceGroupName $resourceGroupName
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force 

## Étapes suivantes
Pour en savoir plus sur l'utilisation de Microsoft Azure PowerShell :
 
- [Applets de commande du gestionnaire de ressources Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409) : découvrez comment utiliser les applets de commande dans le module AzureResourceManager.
- [Utilisation des groupes de ressources pour gérer vos ressources Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups) : découvrez comment créer et gérer des groupes de ressources dans le portail de gestion Azure.
- [Blog Azure](http://blogs.msdn.com/windowsazure) découvrez les nouvelles fonctionnalités d'Azure.
- [Blog Windows PowerShell](http://blogs.msdn.com/powershell) : découvrez les nouvelles fonctionnalités de Windows PowerShell.
- [Blog " Hey, Scripting Guy! " Blog](http://blogs.technet.com/b/heyscriptingguy/) : bénéficiez des conseils et astuces de la communauté Windows PowerShell.




<!--HONumber=49-->