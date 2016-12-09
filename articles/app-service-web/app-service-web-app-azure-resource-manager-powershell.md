---
title: "Commandes PowerShell basées sur Azure Resource Manager pour Azure Web Apps | Microsoft Docs"
description: "Découvrez comment utiliser les nouvelles commandes PowerShell basées sur Azure Resource Manager pour gérer vos applications Azure Web Apps."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 4231fbba-61e5-4f92-b958-531c066fb87f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d8f76a2857766c481ec6cdec52274b5bd370ccfc


---
# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>Gestion d’Azure Web Apps avec PowerShell à l’aide du modèle Azure Resource Manager
> [!div class="op_single_selector"]
> * [Interface de ligne de commande Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

La version 1.0.0 de Microsoft Azure PowerShell a été enrichie de nouvelles commandes qui permettent à l’utilisateur de gérer Web Apps à l’aide de commandes PowerShell basées sur Azure Resource Manager.

Pour en savoir plus sur la gestion des groupes de ressources, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md). 

Pour obtenir la liste complète des paramètres et options disponibles pour les applets de commande PowerShell, consultez la [référence complète d’applets de commande basées sur Azure Resource Manager pour Web Apps](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>Gestion des plans App Service
### <a name="create-an-app-service-plan"></a>Créer un plan App Service
Pour créer un nouveau plan App Service, utilisez l’applet de commande **New-AzureRmAppServicePlan**.

Voici une description des différents paramètres :

* **Name**: nom du plan App Service.
* **Location**: emplacement du plan de service.
* **ResourceGroupName**: groupe de ressources contenant le nouveau plan App Service.
* **Tier** : niveau de tarification souhaité (la valeur par défaut est Gratuit, les autres options étant Partagé, De base, Standard et Premium.)
* **WorkerSize** : nombre de threads de travail (la valeur par défaut est Petit si le paramètre Tier a été défini sur le niveau De base, Standard ou Premium. Les autres options sont Moyen et Grand.)
* **NumberofWorkers**: nombre de threads de travail dans le plan App Service (la valeur par défaut est 1). 

Exemple d’utilisation de cette applet de commande :

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>Création d’un plan App Service dans un environnement App Service
Pour créer un plan App Service dans un environnement App Service, utilisez la même commande **New-AzureRmAppServicePlan** avec des paramètres supplémentaires pour spécifier le nom de l’ASE et le nom du groupe de ressources de l’ASE.

Exemple d’utilisation de cette applet de commande :

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Pour en savoir plus sur l’environnement App Service, consultez [Présentation de l’environnement App Service](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>Répertorier les plans App Service existants
Pour répertorier les plans App Service existants, utilisez l’applet de commande **Get-AzureRmAppServicePlan** .

Pour répertorier tous les plans App Service associés à votre abonnement, utilisez : 

    Get-AzureRmAppServicePlan

Pour répertorier tous les plans App Service associés à un groupe de ressources spécifique, utilisez :

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Pour obtenir un plan App Service spécifique, utilisez :

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>Configurer un plan App Service existant
Pour modifier les paramètres d’un plan App Service existant, utilisez l’applet de commande **Set-AzureRmAppServicePlan** . Vous pouvez modifier le niveau, la taille des threads de travail et le nombre de threads de travail 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>Mise à l’échelle d’un plan App Service
Pour mettre à l’échelle un plan App Service existant, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>Modification de la taille des threads de travail d’un plan App Service
Pour modifier la taille des threads de travail dans un plan App Service existant, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>Modification du niveau d’un plan App Service
Pour modifier le niveau d’un plan App Service existant, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>Supprimer un plan App Service existant
Pour supprimer un plan App Service existant, toutes les applications web affectées doivent d’abord être déplacées ou supprimées. Ensuite, en utilisant l’applet de commande **Remove-AzureRmAppServicePlan**, vous pouvez supprimer le plan App Service.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>Gestion d’App Service Web Apps
### <a name="create-a-web-app"></a>Créer une application web
Pour créer une application web, utilisez l’applet de commande **New-AzureRmWebApp**.

Voici une description des différents paramètres :

* **Name**: nom de l’application web.
* **AppServicePlan**: nom du plan de service utilisé pour héberger l’application web.
* **ResourceGroupName**: groupe de ressources qui héberge le plan App Service.
* **Location**: emplacement de l’application web.

Exemple d’utilisation de cette applet de commande :

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>Créer une application web dans un environnement App Service
Pour créer une application web dans un environnement App Service (ASE), utilisez la même commande **New-AzureRmWebApp** avec des paramètres supplémentaires pour spécifier le nom de l’ASE et le nom du groupe de ressources auquel il appartient.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Pour en savoir plus sur l’environnement App Service, consultez [Présentation de l’environnement App Service](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>Supprimer une application web existante
Pour supprimer une application web existante, vous pouvez utiliser l’applet de commande **Remove-AzureRmWebApp** , auquel cas vous devez spécifier le nom de l’application web et le nom du groupe de ressources.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>Répertorier les applications web existantes
Pour répertorier les applications web existantes, utilisez l’applet de commande **Get-AzureRmWebApp** .

Pour répertorier toutes les applications web associées à votre abonnement, utilisez :

    Get-AzureRmWebApp

Pour répertorier toutes les applications web associées à un groupe de ressources spécifique, utilisez :

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Pour obtenir une application web spécifique, utilisez :

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>Configurer une application web existante
Pour modifier les paramètres et configurations d’une application web existante, utilisez l’applet de commande **Set-AzureRmWebApp** . Pour obtenir une liste complète des paramètres, consultez le [lien de référence d’applets de commande](https://msdn.microsoft.com/library/mt652487.aspx)

Exemple (1) : utilisez cette applet de commande pour modifier les chaînes de connexion

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Exemple (2) : ajouter ou modifier les paramètres de l’application

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Exemple (3) : définir l’application web pour s’exécuter en mode 64 bits

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>Modifier l’état d’une application web existante
#### <a name="restart-a-web-app"></a>Redémarrer une application web
Pour redémarrer une application web, vous devez spécifier le nom et le groupe de ressources de l’application web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Arrêter une application web
Pour arrêter une application web, vous devez spécifier le nom et le groupe de ressources de l’application web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Démarrer une application web
Pour démarrer une application web, vous devez spécifier le nom et le groupe de ressources de l’application web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gérer les profils de publication Web Apps
Chaque application web dispose d’un profil de publication que vous pouvez utiliser pour publier vos applications ; plusieurs opérations peuvent être exécutées sur les profils de publication.

#### <a name="get-publishing-profile"></a>Obtenir le profil de publication
Pour obtenir le profil de publication d’une application web, utilisez :

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Cette commande renvoie le profil de publication à la ligne de commande tout en générant le profil de publication dans un fichier texte.

#### <a name="reset-publishing-profile"></a>Réinitialiser le profil de publication
Pour réinitialiser le mot de passe de publication pour les déploiements FTP et web d’une application web, utilisez :

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Gérer les certificats d’application web
Pour savoir comment gérer les certificats d’application web, consultez [Liaison de certificats SSL à l’aide de PowerShell](app-service-web-app-powershell-ssl-binding.md)

### <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la prise en charge de PowerShell dans Azure Resource Manager, voir [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md)
* Pour en savoir plus sur les environnements App Service, consultez [Présentation de l’environnement App Service](app-service-app-service-environment-intro.md)
* Pour en savoir plus sur la gestion des certificats SSL App Service à l’aide de PowerShell, consultez [Liaison de certificats SSL à l’aide de PowerShell.](app-service-web-app-powershell-ssl-binding.md)
* Pour obtenir la liste complète des applets de commande PowerShell basées sur Azure Resource Manager pour Azure Web Apps, consultez la [référence complète d’applets de commande basées sur Azure Resource Manager pour Web Apps](https://msdn.microsoft.com/library/mt619237.aspx)
* * Pour en savoir plus sur la gestion d’App Service à l’aide de l’interface de ligne de commande, voir [Utilisation de l’interface de ligne de commande XPlat basée sur Azure Resource Manager pour Azure Web App](app-service-web-app-azure-resource-manager-xplat-cli.md).




<!--HONumber=Nov16_HO3-->


