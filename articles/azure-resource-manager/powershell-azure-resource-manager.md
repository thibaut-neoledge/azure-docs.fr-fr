---
title: "Azure PowerShell avec Azure Resource Manager | Microsoft Docs"
description: "Introduction à l’utilisation d’Azure PowerShell pour le déploiement de plusieurs ressources sous la forme d’un groupe de ressources dans Azure."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 08/18/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 109ca4a4672d21969096af26a094390673de25d9
ms.openlocfilehash: 14419f36a9404202d6238d5825fb1ae77d46038a


---
# <a name="using-azure-powershell-with-azure-resource-manager"></a>Utilisation d’Azure PowerShell avec Azure Resource Manager
> [!div class="op_single_selector"]
> * [Portail](resource-group-portal.md)
> * [Interface de ligne de commande Azure](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [API REST](resource-manager-rest-api.md)

Azure Resource Manager implémente une approche moderne de contrôle du cycle de vie des ressources Azure. Au lieu de créer et gérer des ressources à un niveau individuel, commencez à imaginer une solution globale, telle qu’un blog, une galerie de photos, un portail SharePoint ou un wiki. Vous utilisez un modèle, c’est-à-dire une représentation déclarative de la solution, pour définir un groupe de ressources contenant toutes les ressources dont vous avez besoin pour prendre en charge de la solution. Vous pouvez ensuite déployer et gérer ce groupe de ressources sous la forme d’une unité logique.

Ce didacticiel vous apprend à utiliser Azure PowerShell avec Azure Resource Manager. Il vous guide dans le processus de déploiement d'une solution et vous explique comment utiliser cette solution. Vous utiliserez Azure PowerShell et un modèle Resource Manager pour déployer :

* un serveur SQL, pour héberger la base de données ;
* une base de données SQL, pour stocker les données ;
* des règles de pare-feu, pour permettre à l’application Web de se connecter à la base de données ;
* un plan App Service, pour définir les fonctionnalités et le coût de l’application Web ;
* un site Web, pour l’exécution de l’application Web ;
* une configuration Web, pour le stockage de la chaîne de connexion à la base de données.
* Règles d'alerte - pour analyser les performances et les erreurs
* App Insights - pour les paramètres de mise à l’échelle automatique

Pour obtenir Azure PowerShell, consultez la section [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="get-help-for-cmdlets"></a>Aide à l’utilisation des applets de commande
Pour accéder à l'aide détaillée de toute applet de commande présentée dans ce didacticiel, utilisez l'applet de commande Get-Help.

    Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l’aide sur l’applet de commande Get-AzureRmResource, entrez :

    Get-Help Get-AzureRmResource -Detailed

Pour obtenir une liste des applets de commande dans le module Ressources avec un résumé de la rubrique d’aide, entrez :

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

Le résultat ressemble à l’extrait qui suit :

    Name                                   Synopsis
    ----                                   --------
    Find-AzureRmResource                   Searches for resources using the specified parameters.
    Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
    Get-AzureRmADGroup                     Filters active directory groups.
    Get-AzureRmADGroupMember               Get a group members.
    ...

Pour obtenir une aide complète sur une applet de commande, entrez une commande avec la syntaxe suivante :

    Get-Help <cmdlet-name> -Full

## <a name="login-to-your-azure-account"></a>Connexion à votre compte Azure
Avant de travailler sur votre solution, vous devez vous connecter à votre compte.

Pour vous connecter à votre compte Azure, utilisez l’applet de commande **Add-AzureRmAccount** .

    Add-AzureRmAccount

Les applets de commande vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l’applet de commande télécharge vos paramètres de compte pour qu’ils soient reconnus par Azure PowerShell.

Un délai d'expiration est associé à ces paramètres ; il est donc nécessaire de les actualiser ponctuellement. Pour actualiser les paramètres de compte, réexécutez **Add-AzureRmAccount** .

> [!NOTE]
> Les modules Gestionnaire de ressources requièrent l’utilisation de l’applet Add-AzureRmAccount. L'utilisation d'un fichier de paramètres de publication est insuffisante.
>
>

Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement à l’aide de l’applet de commande **Set-AzureRmContext** .

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Avant de déployer des ressources dans votre abonnement, vous devez créer un groupe de ressources qui contiendra ces ressources.

Pour créer un groupe de ressources, utilisez l’applet de commande **New-AzureRmResourceGroup** .

La commande utilise le paramètre **Name** pour attribuer un nom au groupe de ressources et le paramètre **Location** pour indiquer son emplacement. Compte tenu de ce que nous avons effectué dans la section précédente, nous allons utiliser « l’ouest des États-Unis » comme emplacement.

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"

La sortie doit ressembler à ceci :

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Votre groupe de ressources a bien été créé.

## <a name="deploy-your-solution"></a>Déployer votre solution
Cette rubrique n’explique pas comment créer votre modèle, de même qu’elle n’aborde pas la structure du modèle. Pour plus d’informations, consultez les rubriques [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md) et [Guide de création d’un modèle Resource Manager](resource-manager-template-walkthrough.md). Vous allez déployer un modèle [Mettre en service une application Web avec une base de données SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) prédéfini à partir des [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).

À présent que vous disposez de votre groupe de ressources et de votre modèle, vous êtes maintenant prêt à déployer sur le groupe de ressources l’infrastructure définie dans votre modèle. Pour déployer les ressources, utilisez l’applet de commande **New-AzureRmResourceGroupDeployment**. Le modèle spécifie plusieurs valeurs par défaut et que nous utiliserons. Vous n'avez donc pas besoin de fournir les valeurs de ces paramètres. La syntaxe de base ressemble à ce qui suit :

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json

Vous devez spécifier le groupe de ressources et l’emplacement du modèle. Si votre modèle est un fichier local, vous utilisez le paramètre **- TemplateFile** et spécifiez le chemin d’accès au modèle. Vous pouvez affecter au paramètre **-Mode** la valeur **Incremental** ou **Complete**. Par défaut, Resource Manager effectue une mise à jour incrémentielle pendant le déploiement. Il n’est donc pas obligatoire de définir **-Mode** avec la valeur **Incremental** si vous souhaitez effectuer une mise à jour incrémentielle.
Pour comprendre les différences entre ces modes de déploiement, consultez [Déployer une application avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

### <a name="dynamic-template-parameters"></a>Paramètres de modèle dynamiques
Si vous êtes familiarisé avec PowerShell, vous savez que vous pouvez parcourir les paramètres disponibles pour une applet de commande en utilisant le signe moins (-) puis en appuyant sur la touche TAB. Cette fonctionnalité fonctionne également avec les paramètres que vous définissez dans votre modèle. Dès que vous entrez le nom du modèle, l’applet de commande récupère le modèle, l’analyse et ajoute les paramètres du modèle à la commande de façon dynamique. Il est ainsi beaucoup plus facile de spécifier les valeurs des paramètres du modèle.

Au moment d'entrer la commande, vous êtes invité à saisir le paramètre obligatoire manquant, **administratorLoginPassword**. Puis, lorsque vous tapez le mot de passe, la valeur de la chaîne sécurisée est masquée. Cette stratégie évite le risque de fournir un mot de passe en clair.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Si le modèle inclut un paramètre avec un nom correspondant à l’un des paramètres de la commande pour déployer le modèle (par exemple, en incluant un paramètre nommé **ResourceGroupName** dans votre modèle, qui est le même que le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroupdeployment)), vous serez invité à fournir une valeur pour un paramètre avec le suffixe **FromTemplate** (tel que **ResourceGroupNameFromTemplate**). En général, vous devez éviter cette confusion en ne nommant pas les paramètres avec un nom identique à celui des paramètres utilisés pour les opérations de déploiement.

La commande s’exécute et renvoie des messages au fur et à mesure que les ressources sont créées. Vous obtenez à la fin le résultat de votre déploiement.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net

    DeploymentDebugLogLevel :

Il nous a suffi de quelques étapes seulement pour créer et déployer les ressources requises pour un site web complexe.

### <a name="log-debug-information"></a>Informations de débogage de journal
Lorsque vous déployez un modèle, vous pouvez consigner des informations supplémentaires sur la requête et la réponse en spécifiant le paramètre **-DeploymentDebugLogLevel** lors de l’exécution de **New-AzureRmResourceGroupDeployment**. Ces informations peuvent vous aider à corriger des erreurs de déploiement. La valeur par défaut est **Aucun** , ce qui signifie qu’aucun contenu de requête ou de réponse n’est consigné. Vous pouvez spécifier le contenu à consigner à partir de la requête, de la réponse, ou des deux.  Pour plus d’informations sur la résolution des problèmes liés aux déploiements et sur la consignation des informations de débogage, consultez l’article [Résolution des problèmes liés aux déploiements de groupes de ressources avec Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md). L'exemple suivant consigne le contenu de la requête et le contenu de la réponse pour le déploiement.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json

> [!NOTE]
> Lorsque vous définissez le paramètre DeploymentDebugLogLevel, prêtez attention au type d'informations que vous transmettez pendant le déploiement. En enregistrant des informations sur la requête ou la réponse, vous risquez d’exposer des données sensibles récupérées au cours des opérations de déploiement.

## <a name="get-information-about-your-resource-groups"></a>Obtenir des informations sur vos groupes de ressources
Après avoir créé un groupe de ressources, vous pouvez utiliser les applets de commande du module Gestionnaire de ressources pour gérer vos groupes de ressources.

* Pour obtenir un groupe de ressources de votre abonnement, utilisez l’applet de commande **Get-AzureRmResourceGroup** :

        Get-AzureRmResourceGroup -ResourceGroupName TestRG1

    Cette dernière renvoie les informations suivantes :

        ResourceGroupName : TestRG1
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG

        ...

    Si vous ne spécifiez aucun nom de groupe de ressources, l’applet de commande retourne tous les groupes de ressources de votre abonnement.
* Pour obtenir les ressources du groupe, utilisez l’applet de commande **Find-AzureRmResource** et son paramètre **ResourceGroupNameContains**. Si vous ne spécifiez pas de paramètres, Find-AzureRmResource récupère l’ensemble des ressources de votre abonnement Azure.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1

     Qui renvoie une liste de ressources formatées comme suit :

        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
* Vous pouvez utiliser des balises pour organiser logiquement les ressources de votre abonnement, et récupérer des ressources à l’aide des applets de commande **Find-AzureRmResource** et **Find-AzureRmResourceGroup**.

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}

      There is much more you can do with tags. For more information, see [Using tags to organize your Azure resources](resource-group-using-tags.md).

## <a name="add-to-a-resource-group"></a>Ajouter une ressource à un groupe de ressources
Pour ajouter une ressource au groupe de ressources, utilisez l’applet de commande **New-AzureRmResource** . Mais une telle méthode risque de créer une confusion par la suite, car la nouvelle ressource n’existe pas dans votre modèle. Si vous avez redéployé l’ancien modèle, vous allez déployer une solution incomplète. Si vous effectuez fréquemment des déploiements, il vous semblera à la fois plus facile et plus fiable d’ajouter la nouvelle ressource à votre modèle avant de redéployer ce dernier.

## <a name="move-a-resource"></a>Déplacer une ressource
Vous pouvez déplacer des ressources existantes vers un nouveau groupe de ressources. Pour obtenir des exemples, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).

## <a name="export-template"></a>Exportation du modèle
Pour un groupe de ressources existant (déployé via PowerShell ou l'une des autres méthodes telles que le portail), vous pouvez afficher le modèle Resource Manager pour le groupe de ressources. L’exportation du modèle offre deux avantages :

1. Vous pouvez facilement automatiser des déploiements futurs de la solution car l’ensemble de l’infrastructure est définie dans le modèle.
2. Vous pouvez vous familiariser avec la syntaxe de modèle en regardant dans la JSON (JavaScript Object Notation) qui représente votre solution.

Vous pouvez générer, au moyen de PowerShell, un modèle qui représente l’état actuel de votre groupe de ressources ou récupérer le modèle qui a été utilisé pour un déploiement spécifique.

L’exportation du modèle pour un groupe de ressources est utile lorsque vous avez apporté des modifications à un groupe de ressources et que vous devez récupérer la représentation JSON de son état actuel. Toutefois, le modèle généré contient uniquement un nombre minimal de paramètres et aucune variable. La plupart des valeurs dans le modèle sont codées en dur. Avant de déployer le modèle généré, vous pouvez convertir plusieurs valeurs en paramètres afin de personnaliser le déploiement pour différents environnements.

L’exportation du modèle pour un déploiement spécifique est utile lorsque vous avez besoin d’afficher le modèle réel qui a été utilisé pour déployer des ressources. Le modèle comprend tous les paramètres et toutes les variables définis pour le déploiement d’origine. Toutefois, si un membre de votre organisation a apporté des modifications au groupe de ressources qui ne sont pas définies dans le modèle, ce dernier ne représente pas l’état actuel du groupe de ressources.

> [!NOTE]
> La fonctionnalité Modèle d’exportation est en version préliminaire. Tous les types de ressources ne la prennent pas actuellement en charge. Lorsque vous tentez d’exporter un modèle, une erreur indiquant que certaines ressources n’ont pas été exportées peut s’afficher. Le cas échéant, vous pouvez définir manuellement ces ressources dans votre modèle après l’avoir téléchargé.


### <a name="export-template-from-resource-group"></a>Exportation du modèle à partir d’un groupe de ressources
Pour afficher le modèle d’un groupe de ressources, exécutez l’applet de commande **Export-AzureRmResourceGroup** .

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json

### <a name="download-template-from-deployment"></a>Télécharger le modèle depuis le déploiement
Pour télécharger le modèle utilisé pour un déploiement spécifique, exécutez l’applet de commande **Save-AzureRmResourceGroupDeploymentTemplate** .

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## <a name="delete-resources-or-resource-group"></a>Supprimer des ressources ou un groupe de ressources
* Pour supprimer une ressource du groupe de ressources, utilisez l’applet de commande **Remove-AzureRmResource** . Cette applet de commande supprime la ressource, mais pas le groupe de ressources.

    Cette commande supprime le site web TestSite du groupe de ressources TestRG1.

        Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01
* Pour supprimer un groupe de ressources, utilisez l’applet de commande **Remove-AzureRmResourceGroup** . Cette applet de commande supprime le groupe de ressources et ses ressources.

        Remove-AzureRmResourceGroup -Name TestRG1

    Vous êtes invité à confirmer la suppression.

        Confirm
        Are you sure you want to remove resource group 'TestRG1'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## <a name="deployment-script"></a>Script de déploiement
Les exemples de déploiement précédents de cette rubrique ne présentaient que les applets de commande individuelles nécessaires au déploiement des ressources dans Azure. L'exemple suivant montre un script de déploiement qui crée le groupe de ressources et déploie les ressources.

    <#
      .SYNOPSIS
      Deploys a template to Azure

      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour savoir comment déployer des modèles, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).
* Pour obtenir un exemple détaillé de déploiement d’un projet, consultez [Déployer des microservices de manière prévisible dans Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
* Pour résoudre les problèmes liés à l’échec d’un déploiement, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure](resource-manager-troubleshoot-deployments-powershell.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).




<!--HONumber=Dec16_HO2-->


