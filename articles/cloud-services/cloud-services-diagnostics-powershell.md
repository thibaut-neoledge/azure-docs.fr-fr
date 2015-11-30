<properties 
	pageTitle="Activer les diagnostics dans Azure Cloud Services à l’aide de PowerShell | Microsoft Azure" 
	description="Découvrez comment activer les diagnostics pour les services cloud à l’aide de PowerShell" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="sbtron" 
	manager="" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="saurabh"/>


# Activer les diagnostics dans Azure Cloud Services à l’aide de PowerShell

Vous pouvez collecter des données de diagnostic telles que les journaux des applications, les compteurs de performances, etc. à partir d’un service cloud à l’aide de l’extension Diagnostics Azure. Cet article décrit comment activer l’extension Diagnostics Azure pour un service cloud à l’aide de PowerShell. Consultez [Installer et configurer Azure PowerShell Azure](powershell-install-configure.md) pour connaître les conditions requises pour cet article.

## Activer l’extension de diagnostics lors du déploiement d’un service cloud

Cette approche est idéale pour les scénarios d’intégration continue où l’extension de diagnostics peut être activée. Vous pouvez activer l’extension de diagnostics dans le cadre du déploiement du service cloud en transmettant le paramètre *ExtensionConfiguration* à l’applet de commande [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx). Le paramètre *ExtensionConfiguration* prend un tableau de configurations de diagnostics qui peut être créé à l’aide de l’applet de commande [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx).

L’exemple suivant montre comment vous pouvez activer les diagnostics pour un service cloud avec un rôle web et un rôle de travail possédant chacun sa propre configuration de diagnostics.

	$service_name = "MyService"
	$service_package = "CloudService.cspkg"
	$service_config = "ServiceConfiguration.Cloud.cscfg"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -Storage_context $storageContext -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath
	  
	 
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 



## Activer l’extension de diagnostics sur un service cloud existant

Vous pouvez utiliser l’applet de commande [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) pour activer les diagnostics sur un service cloud qui est déjà en cours d’exécution.


	$service_name = "MyService"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey
 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $webrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WebRole" 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WorkerRole"
 

## Obtenir la configuration actuelle de l’extension de diagnostics
Pour obtenir la configuration de diagnostics actuelle pour un service cloud, utilisez l’applet de commande [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) :
	
	Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## Supprimer l’extension de diagnostics
Pour désactiver les diagnostics sur un service cloud, vous pouvez utiliser l’applet de commande [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx).

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Si vous avez activé l’extension de diagnostics à l’aide de *Set-AzureServiceDiagnosticsExtension* ou *New-AzureServiceDiagnosticsExtensionConfig* sans le paramètre *Role*, vous pouvez supprimer l’extension à l’aide de *Remove-AzureServiceDiagnosticsExtension* sans le paramètre *Role*. Si le paramètre *Role* a été utilisé lors de l’activation de l’extension, il doit également être utilisé lors de la suppression de l’extension.

Pour supprimer l’extension de diagnostics de chaque rôle individuel :

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## Étapes suivantes

- Pour obtenir une aide supplémentaire sur l’utilisation des diagnostics Azure et d’autres techniques pour résoudre les problèmes, consultez la page [Activation de Diagnostics dans Azure Cloud Services et Azure Virtual Machines](cloud-services-dotnet-diagnostics.md).
- Le [schéma de configuration des diagnostics](https://msdn.microsoft.com/library/azure/dn782207.aspx) explique les différentes options de configuration xml pour l’extension de diagnostics.
- Pour savoir comment activer l’extension de diagnostics pour les machines virtuelles, consultez [Créer une machine virtuelle Windows avec la surveillance et les diagnostics à l’aide d’un modèle Azure Resource Manager](virtual-machines-extensions-diagnostics-windows-template.md).  

<!---HONumber=Nov15_HO4-->