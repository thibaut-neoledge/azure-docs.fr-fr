<properties
	pageTitle="Commandes PowerShell basées sur Azure Resource Manager pour Azure Web Apps | Microsoft Azure"
	description="Découvrez comment utiliser les nouvelles commandes PowerShell basées sur Azure Resource Manager pour gérer vos applications Azure Web Apps."
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="aelnably"/>

# Gestion d’Azure Web Apps avec PowerShell à l’aide du modèle Azure Resource Manager#

La version 1.0.0 de Microsoft Azure PowerShell a été enrichie de nouvelles commandes qui permettent à l’utilisateur de gérer Web Apps à l’aide de commandes PowerShell basées sur Azure Resource Manager.

Pour en savoir plus sur la gestion des groupes de ressources, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

Pour obtenir la liste complète des paramètres et options disponibles pour les applets de commande PowerShell Azure Resource Manager pour Web Apps, consultez la [référence complète d’applets de commande basées sur Azure Resource Manager pour Web Apps](https://msdn.microsoft.com/library/mt619237.aspx)

## Gestion des plans App Service ##

### Créer un plan App Service ###
Pour créer un nouveau plan App Service, utilisez l’applet de commande **New-AzureRmAppServicePlan**.

Voici une description des différents paramètres :

- 	**Name** : nom du plan App Service.
- 	**Location** : emplacement du plan de service.
- 	**ResourceGroupName** : groupe de ressources contenant le nouveau plan App Service.
- 	**Tier** : niveau de tarification souhaité (la valeur par défaut est Gratuit, les autres options étant Partagé, De base, Standard et Premium.)
- 	**WorkerSize** : nombre de threads de travail (la valeur par défaut est Petit si le paramètre Tier a été défini sur le niveau De base, Standard ou Premium. Les autres options sont Moyen et Grand.)
- 	**NumberofWorkers** : nombre de threads de travail dans le plan App Service (la valeur par défaut est 1). 

Exemple d’utilisation de cette applet de commande :

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### Création d’un plan App Service dans un environnement App Service ###
Pour créer un plan App Service dans un environnement App Service, vous pouvez utiliser la même commande **New-AzureRmAppServicePlan** avec des paramètres supplémentaires pour spécifier le nom de l’ASE et le nom du groupe de ressources auquel il appartient.

Exemple d’utilisation de cette applet de commande :

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Pour en savoir plus sur l’environnement App Service, consultez [Présentation de l’environnement App Service](app-service-app-service-environment-intro.md)

### Répertorier les plans App Service existants ###

Pour répertorier les plans App Service existants, utilisez l’applet de commande **Get-AzureRmAppServicePlan**.

Pour répertorier tous les plans App Service associés à votre abonnement, utilisez :

    Get-AzureRmAppServicePlan

Pour répertorier tous les plans App Service associés à un groupe de ressources spécifique, utilisez :

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Pour obtenir un plan App Service spécifique, utilisez :

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### Configurer un plan App Service existant ###

Pour modifier les paramètres d’un plan App Service existant, utilisez l’applet de commande **Set-AzureRmAppServicePlan**. Vous pouvez modifier le niveau, la taille des threads de travail et le nombre de threads de travail

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### Mise à l’échelle d’un plan App Service ####

Pour mettre à l’échelle un plan App Service existant, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### Modification de la taille des threads de travail d’un plan App Service ####

Pour modifier la taille des threads de travail dans un plan App Service existant, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### Modification du niveau d’un plan App Service ####

Pour modifier le niveau d’un plan App Service existant, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### Supprimer un plan App Service existant ###

Pour supprimer un plan App Service existant, vous devez d’abord déplacer ou supprimer toutes les applications web affectées, après quoi vous pouvez supprimer le plan App Service à l’aide de l’applet de commande **Remove-AzureRmAppServicePlan**.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## Gestion d’App Service Web Apps ##

### Créer une application web ###

Pour créer une nouvelle application web, utilisez l’applet de commande **New-AzureRmWebApp**.

Voici une description des différents paramètres :

- **Name** : nom de l’application web.
- **AppServicePlan** : nom du plan de service utilisé pour héberger l’application web.
- **ResourceGroupName** : groupe de ressources qui héberge le plan App Service.
- **Location** : emplacement de l’application web.

Exemple d’utilisation de cette applet de commande :

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### Créer une application web dans un environnement App Service ###

Pour créer une nouvelle application web dans un environnement App Service (ASE), vous pouvez utiliser la même commande **New-AzureRmWebApp** avec des paramètres supplémentaires pour spécifier le nom de l’ASE et le nom du groupe de ressources auquel il appartient.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Pour en savoir plus sur l’environnement App Service, consultez [Présentation de l’environnement App Service](app-service-app-service-environment-intro.md)

### Supprimer une application web existante ###

Pour supprimer une application web existante, vous pouvez utiliser l’applet de commande **Remove-AzureRmWebApp**, auquel cas vous devez spécifier le nom de l’application web et le nom du groupe de ressources.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### Répertorier les applications web existantes ###

Pour répertorier les applications web existantes, utilisez l’applet de commande **Get-AzureRmWebApp**.

Pour répertorier toutes les applications web associées à votre abonnement, utilisez :

    Get-AzureRmWebApp

Pour répertorier toutes les applications web associées à un groupe de ressources spécifique, utilisez :

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Pour obtenir une application web spécifique, utilisez :

    Get-AzureRmWebApp -Name ContosoWebApp

### Configurer une application web existante ###

Pour modifier les paramètres et configurations d’une application web existante, utilisez l’applet de commande **Set-AzureRmWebApp**. Pour obtenir une liste complète des paramètres, consultez le [lien de référence d’applets de commande](https://msdn.microsoft.com/library/mt652487.aspx)

Exemple (1) : utilisez cette applet de commande pour modifier les chaînes de connexion

	$connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
	Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Exemple (2) : ajouter un exemple pour les paramètres d’application

	$appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
	Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Exemple (3) : définir l’application web pour s’exécuter en mode 64 bits

	Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### Modifier l’état d’une application web existante ###

#### Redémarrer une application web ####

Pour redémarrer une application web, vous devez spécifier le nom et le groupe de ressources de l’application web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### Arrêter une application web ####

Pour arrêter une application web, vous devez spécifier le nom et le groupe de ressources de l’application web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### Démarrer une application web ####

Pour démarrer une application web, vous devez spécifier le nom et le groupe de ressources de l’application web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### Gérer les profils de publication Web Apps ###

Chaque application web dispose d’un profil de publication que vous pouvez utiliser pour publier vos applications ; un certain nombre d’opérations peuvent être exécutées sur les profils de publication.

#### Obtenir le profil de publication ####

Pour obtenir le profil de publication d’une application web, utilisez :

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Notez que cela renverra le profil de publication à la ligne de commande tout en générant le profil de publication dans un fichier texte.

#### Réinitialiser le profil de publication ####

Pour réinitialiser le mot de passe de publication pour les déploiements FTP et web d’une application web, utilisez :

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### Gérer les certificats d’application web ###

Pour savoir comment gérer les certificats d’application web, consultez [Liaison de certificats SSL à l’aide de PowerShell](app-service-web-app-powershell-ssl-binding.md)



### Étapes suivantes ###
- Pour plus d’informations sur la prise en charge de PowerShell dans Azure Resource Manager, voir [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).
- Pour en savoir plus sur les environnements App Service, consultez [Présentation de l’environnement App Service](app-service-app-service-environment-intro.md).
- Pour en savoir plus sur la gestion des certificats SSL App Service à l’aide de PowerShell, consultez [Liaison de certificats SSL à l’aide de PowerShell.](app-service-web-app-powershell-ssl-binding.md)
- Pour obtenir la liste complète des applets de commande PowerShell basées sur Azure Resource Manager pour Azure Web Apps, consultez la [référence complète d’applets de commande basées sur Azure Resource Manager pour Web Apps](https://msdn.microsoft.com/library/mt619237.aspx).

<!---HONumber=AcomDC_0615_2016-->