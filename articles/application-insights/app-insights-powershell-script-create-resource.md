<properties 
	pageTitle="Script PowerShell de création d’une ressource Application Insights" 
	description="Automatisez la création des ressources Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2016" 
	ms.author="awills"/>

#  Script PowerShell de création d’une ressource Application Insights

*Application Insights est à l'état de version préliminaire.*

Lorsque vous voulez surveiller une nouvelle application, ou une nouvelle version d’une application, avec [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/), vous définissez une nouvelle ressource dans Microsoft Azure. Cette ressource est l’endroit où les données de télémétrie de votre application sont analysées et affichées.

Vous pouvez automatiser la création d’une nouvelle ressource à l’aide de PowerShell.

Par exemple, si vous développez une application pour appareil mobile, il est probable qu’à tout moment, plusieurs versions publiées de votre application soient utilisées par vos clients. Vous ne voulez pas que les résultats de télémétrie de différentes versions soient mélangés. Par conséquent, vous obtenez votre processus de génération pour créer une nouvelle ressource pour chaque build.

## Script de création d’une ressource Application Insights

Consultez les spécifications d’applet de commande appropriées :

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*Script PowerShell*

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## Que faire avec l’iKey ?

Chaque ressource est identifiée par sa clé d’instrumentation (iKey). L’iKey est une sortie du script de création de ressources. Votre script de génération doit fournir l’iKey au kit de développement logiciel (SDK) Application Insights intégrée à votre application.

Il existe deux façons de mettre l’iKey à disposition du kit de développement logiciel (SDK) :
  
* Dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) : 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Ou dans le [code d’initialisation](app-insights-api-custom-events-metrics.md) : 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## Voir aussi

* [Créer des ressources Application Insights et de test Web templates à partir de modèles (en anglais)](app-insights-powershell.md)
* [Configurer la surveillance de diagnostics Azure avec PowerShell (en anglais](app-insights-powershell-azure-diagnostics.md) 
* [Définition d’alertes à l’aide de PowerShell](app-insights-powershell-alerts.md)

 

<!---HONumber=AcomDC_0224_2016-->