<properties
   pageTitle="Déployer une application avec un modèle Azure Resource Manager"
	services="azure-resource-manager"
	description="Utilisez Azure Resource Manager pour déployer une application sur Azure. Un modèle est un fichier JSON qui peut être utilisé à partir du portail, de PowerShell, d’Azure CLI pour Mac, Linux et Windows, ou de REST."
	documentationCenter="na"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
   ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/20/2015"
	ms.author="tomfitz"/>

# Déployer une application avec un modèle Azure Resource Manager

Cette rubrique explique comment utiliser les modèles Azure Resource Manager pour déployer votre application dans Azure. Elle montre comment déployer votre application à l’aide d’Azure PowerShell, d’Azure CLI, de l’API REST ou de la version préliminaire du portail Microsoft Azure.

Les modèles Azure vous permettent d’approvisionner rapidement et facilement vos applications dans Azure via un fichier JSON déclaratif. Dans un seul modèle JSON, vous pouvez déployer plusieurs services, tels que Virtual Machines, Virtual Networks, Storage, App Services et Databases. Utilisez le même modèle pour déployer votre application de manière cohérente à chaque phase de son cycle de vie.

Pour simplifier la gestion de votre application, vous pouvez organiser toutes les ressources qui partagent un cycle de vie commun en un groupe de ressources unique. Les groupes de ressources facilitent le déploiement, la mise à jour et la suppression de toutes les ressources associées ensemble. Dans la plupart des cas, un groupe de ressources est mappé à une seule application ou à une couche d’application (pour les grandes applications). La ressource déployée via un modèle réside dans un groupe de ressources unique, mais peut inclure des dépendances dans d’autres groupes de ressources.

Dans un groupe de ressources, vous pouvez suivre l’exécution d’un déploiement et afficher l’état du déploiement ainsi que toute sortie de l’exécution du modèle.

Lorsque vous déployez une application avec un modèle, vous pouvez fournir des valeurs de paramètre pour personnaliser la façon dont les ressources sont créées. Spécifiez les valeurs de ces paramètres en les incorporant directement ou dans un fichier de paramètres.

## Concepts

- Groupe de ressources : collection d’entités qui partagent un cycle de vie commun
- Modèle Resource Manager : fichier JSON déclaratif qui définit l’état à atteindre d’un déploiement
- Déploiement : opération qui effectue le suivi de l’exécution d’un modèle Azure Resource Manager
- Paramètres : valeurs fournies par l’utilisateur qui exécute le déploiement pour personnaliser les ressources déployées
- Fichier de paramètres : fichier JSON qui stocke les valeurs et les noms de paramètre 

## Scénarios

Avec les modèles Resource Manager, vous pouvez :

- Déployer des applications multicouches complexes, telles que Microsoft SharePoint.
- Déployer vos applications plusieurs fois de manière cohérente.
- Prendre en charge les environnements de développement, test et production.
- Afficher l’état des déploiements.
- Résoudre les échecs de déploiement à l’aide des journaux d’audit de déploiement.

## Déployer avec la version préliminaire du portail

Devinez quoi ? Chaque application que vous créez avec la [version préliminaire du portail](https://portal.azure.com/) est soutenue par un modèle Azure Resource Manager. En créant simplement une machine virtuelle, un réseau virtuel, un compte de stockage, un service d’application ou une base de données via le portail, vous profitez déjà des avantages d’Azure Resource Manager sans rien faire de plus. Sélectionnez simplement l'icône **Nouveau** et vous pourrez progresser vers le déploiement d'une application avec Azure Resource Manager.

![Nouveau](./media/resource-group-template-deploy/new.png)

Pour plus d'informations sur l'utilisation du portail avec Azure Resource Manager, consultez [Utilisation du portail Azure en version préliminaire pour gérer vos ressources Azure](resource-group-portal.md).


## Déployer avec PowerShell

Si vous n’avez pas déjà utilisé Azure PowerShell avec Resource Manager, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

1. Connectez-vous à votre compte Azure. Une fois que vous avez entré vos informations d'identification, la commande retourne les informations relatives à votre compte.

        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. Si vous avez plusieurs abonnements, fournissez l'ID d'abonnement que vous souhaitez utiliser pour le déploiement.

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Basculez sur le module Azure Resource Manager.

        PS C:\> Switch-AzureMode AzureResourceManager

4. Si vous n'avez pas de groupe de ressources, créez-en un. Indiquez le nom du groupe de ressources et l'emplacement dont vous avez besoin pour votre solution. Un résumé du nouveau groupe de ressources est retourné.

        PS C:\> New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Pour créer un déploiement pour votre groupe de ressources, exécutez la commande **New-AzureResourceGroupDeployment** et indiquez les paramètres nécessaires. Les paramètres comprennent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d'accès ou l'URL du modèle que vous avez créé et tous les autres paramètres nécessaires à votre scénario.
   
     Vous disposez des options suivantes pour fournir les valeurs des paramètres :
   
     - Utiliser des paramètres incorporés.

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - Utiliser un objet de paramètres.

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - Utilisation d’un fichier de paramètres. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](./#parameter-file).

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     Une fois le groupe de ressources déployé, un résumé du déploiement apparaît.

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. Pour obtenir des informations sur les échecs de déploiement.

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Pour obtenir des informations détaillées sur les échecs de déploiement.

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
        
### Vidéo

Voici une vidéo expliquant comment utiliser les modèles Resource Manager avec PowerShell.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Installer Azure CLI pour Mac, Linux et Windows

Si vous n’avez pas déjà utilisé Azure CLI avec Azure Resource Manager, consultez [Utilisation d’Azure CLI pour Mac, Linux et Windows avec Azure Resource Management](../xplat-cli-azure-resource-manager.md).

1. Connectez-vous à votre compte Azure. Une fois que vous avez entré vos informations d'identification, la commande retourne le résultat de votre connexion.

        azure login
  
        ...
        info:    login command OK

2. Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement.

        azure account set <YourSubscriptionNameOrId>

3. Basculez vers le module Azure Resource Manager. Vous recevrez la confirmation du nouveau mode.

        azure config mode arm
   
        info:     New mode is arm

4. Si vous n'avez pas de groupe de ressources, créez-en un. Indiquez le nom du groupe de ressources et l'emplacement dont vous avez besoin pour votre solution. Un résumé du nouveau groupe de ressources est retourné.

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Pour créer un déploiement pour votre groupe de ressources, exécutez la commande suivante et indiquez les paramètres nécessaires. Les paramètres comprennent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d'accès ou l'URL du modèle que vous avez créé et tous les autres paramètres nécessaires à votre scénario.
   
     Vous disposez des options suivantes pour fournir les valeurs des paramètres :

     - Utiliser des paramètres incorporés et un modèle local.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - Utiliser des paramètres incorporés et un lien vers un modèle.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - Utiliser un fichier de paramètres. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](./#parameter-file).
    
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Une fois le groupe de ressources déployé, un résumé du déploiement apparaît.
  
           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. Pour obtenir des informations sur votre dernier déploiement.

         azure group log show -l ExampleResourceGroup

7. Pour obtenir des informations détaillées sur les échecs de déploiement.
      
         azure group log show -l -v ExampleResourceGroup

## Déployer avec l’API REST
1. Définissez [des en-têtes et des paramètres communs](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), y compris des jetons d’authentification.
2. Si vous n’avez pas de groupe de ressources, créez-en un. Fournissez votre ID abonnement, le nom du groupe de ressources et l’emplacement dont vous avez besoin pour votre solution. Pour plus d’informations, consultez [Créer un groupe de ressources](https://msdn.microsoft.com/library/azure/dn790525.aspx).

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
           <common headers>
           {
             "location": "West US",
             "tags": {
               "tagname1": "tagvalue1"
             }
           }
   
3. Créez un déploiement de groupe de ressources. Fournissez votre ID abonnement, le nom du groupe de ressources à déployer, le nom du déploiement et l’emplacement de votre modèle. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](./#parameter-file). Pour plus d’informations sur l’API REST et créer un groupe de ressources, consultez [Créer un déploiement de modèle](https://msdn.microsoft.com/library/azure/dn790564.aspx).
    
         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
            <common headers>
            {
              "properties": {
                "templateLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                  "contentVersion": "1.0.0.0",
                },
                "mode": "Incremental",
                "parametersLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                  "contentVersion": "1.0.0.0",
                }
              }
            }
   
4. Obtenez l’état du déploiement du modèle. Pour plus d’informations, consultez [Obtenir des informations sur le déploiement d’un modèle](https://msdn.microsoft.com/library/azure/dn790565.aspx).

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## Fichier de paramètres

Si vous utilisez un fichier de paramètres pour passer les valeurs de paramètre à votre modèle au cours du déploiement, vous devez créer un fichier JSON avec un format similaire à l’exemple suivant.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            }
       }
    }

La taille du fichier de paramètre ne peut pas être supérieure à 64 Ko.

## Étapes suivantes
- Pour obtenir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](../arm-template-deployment.md).
- Pour obtenir un exemple détaillé de déploiement d’une application, consultez [Mise en service et déploiement de microservices de manière prévisible dans Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Pour en savoir plus sur les sections du modèle Azure Resource Manager, consultez la section [Création de modèles](../resource-group-authoring-templates.md).
- Pour obtenir la liste des fonctions que vous pouvez utiliser dans un modèle Azure Resource Manager, consultez [Fonctions de modèle](../resource-group-template-functions.md).

 

<!---HONumber=August15_HO9-->