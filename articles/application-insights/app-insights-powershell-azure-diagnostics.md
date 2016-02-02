<properties
    pageTitle="Utilisation de PowerShell pour l’envoi de diagnostics Azure vers Application Insight | Microsoft Azure"
    description="Automatiser la configuration d’Azure Diagnostics pour envoyer des données vers Application Insights"
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# Utilisation de PowerShell pour l’envoi de diagnostics Azure vers Application Insights

[Microsoft Azure](https://azure.com) peut être [configuré pour envoyer des Diagnostics Azure](app-insights-azure-diagnostics.md) vers [Visual Studio Application Insights](app-insights-overview.md). Les tests de diagnostic concernent Azure Cloud Services et les machines virtuelles Azure. Ils permettent de compléter les données de télémétrie que vous envoyez depuis l’application à l’aide du kit de développement logiciel d’Application Insights. Dans le cadre de l’automatisation du processus de création de nouvelles ressources dans Azure, vous pouvez configurer des diagnostics avec PowerShell.

## Activer l’extension de diagnostics lors du déploiement d’un service cloud

L’applet de commande `New-AzureDeployment` comporte un paramètre `ExtensionConfiguration` qui utilise un tableau de configurations de diagnostics. Ces derniers peuvent être créés à l’aide de l’applet de commande `New-AzureServiceDiagnosticsExtensionConfig`. Par exemple :

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## Activer l’extension de diagnostics sur un service cloud existant

Sur un service existant, utilisez `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## Obtenir la configuration actuelle de l’extension de diagnostics

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## Supprimer l’extension de diagnostics

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Si vous avez activé l’extension des diagnostics à l’aide de `Set-AzureServiceDiagnosticsExtension` ou de `New-AzureServiceDiagnosticsExtensionConfig` sans paramètre Rôle. Vous pouvez ensuite supprimer l’extension à l’aide de `Remove-AzureServiceDiagnosticsExtension` sans paramètre Rôle. Si le paramètre Rôle a été utilisé lors de l’activation de l’extension, il doit également être utilisé au moment de sa suppression.

Pour supprimer l’extension de diagnostics de chaque rôle individuel :

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## Voir aussi

* [Surveiller les applications Azure Cloud Services avec Application Insights](app-insights-cloudservices.md)
* [Envoyer des diagnostics Azure vers Application Insights.](app-insights-azure-diagnostics.md)
* [Automatisation de la configuration des alertes](app-insights-powershell-alerts.md)

<!---HONumber=AcomDC_0128_2016-->