<properties
   pageTitle="Déployer des ressources avec le modèle Resource Manager | Microsoft Azure"
   services="azure-resource-manager"
   description="Utilisez Azure Resource Manager pour déployer des ressources sur Azure. Un modèle est un fichier JSON qui peut être utilisé à partir du portail, de PowerShell, d’Azure CLI pour Mac, Linux et Windows, ou de REST."
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
   ms.date="12/23/2015"
   ms.author="tomfitz"/>

# Déployer une application avec un modèle Azure Resource Manager

Cette rubrique explique comment utiliser les modèles Azure Resource Manager pour déployer votre application dans Azure. Elle montre comment déployer votre application à l’aide d’Azure PowerShell, de l’interface de ligne de commande Azure, de l’API REST ou du portail Azure.

Pour une introduction à Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](../resource-group-overview.md). Pour en savoir plus sur la création de modèles, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

Lorsque vous déployez une application avec un modèle, vous pouvez fournir des valeurs de paramètre pour personnaliser la façon dont les ressources sont créées. Spécifiez les valeurs de ces paramètres en les incorporant directement ou dans un fichier de paramètres.

## Déploiements incrémentiels et complets

Par défaut, Resource Manager gère les déploiements sous la forme de mises à jour incrémentielles du groupe de ressources. Dans le cadre d’un déploiement incrémentiel, Resource Manager :

- **conserve telles quelles** les ressources qui existent dans le groupe de ressources, mais qui ne sont pas spécifiées dans le modèle ;
- **ajoute** les ressources qui sont spécifiées dans le modèle, mais qui n’existent pas dans le groupe de ressources ; 
- **ne réapprovisionne pas** les ressources qui existent dans le groupe de ressources à l’état défini dans le modèle.

Vous pouvez utiliser Azure PowerShell ou l’API REST pour spécifier une mise à jour complète du groupe de ressources. Pour l’instant, l’interface de ligne de commande Azure ne prend pas en charge les déploiements complets. Dans le cadre d’un déploiement complet, Resource Manager :

- **supprime** les ressources qui existent dans le groupe de ressources, mais qui ne sont pas spécifiées dans le modèle ;
- **ajoute** les ressources qui sont spécifiées dans le modèle, mais qui n’existent pas dans le groupe de ressources ; 
- **ne réapprovisionne pas** les ressources qui existent dans le groupe de ressources à l’état défini dans le modèle.
 
Vous spécifiez le type de déploiement via la propriété **Mode**, comme indiqué dans les exemples ci-dessous pour PowerShell et pour l’API REST.

## Déployer avec PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


1. Connectez-vous à votre compte Azure. Une fois que vous avez entré vos informations d'identification, la commande retourne les informations relatives à votre compte.

    Azure PowerShell 1.0 :

         PS C:\> Login-AzureRmAccount

         Evironment : AzureCloud
         Account    : someone@example.com
         ...


2. Si vous possédez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement avec la commande **Select-AzureRmSubscription**.

        PS C:\> Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

3. Si vous ne disposez pas d’un groupe de ressources existant, créez-en un avec la commande **New-AzureRmResourceGroup**. Indiquez le nom du groupe de ressources et l'emplacement dont vous avez besoin pour votre solution. Un résumé du nouveau groupe de ressources est retourné.

        PS C:\> New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Pour créer un déploiement pour votre groupe de ressources, exécutez la commande **New-AzureRmResourceGroupDeployment** et fournissez les paramètres nécessaires. Les paramètres comprennent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d'accès ou l'URL du modèle que vous avez créé et tous les autres paramètres nécessaires à votre scénario. Le paramètre **Mode** n’est pas spécifié, ce qui signifie que la valeur par défaut **Incremental** est utilisée.
   
     Vous disposez des options suivantes pour fournir les valeurs des paramètres :
   
     - Utiliser des paramètres incorporés.

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - Utiliser un objet de paramètres.

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - Utilisation d’un fichier de paramètres. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](./#parameter-file).

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     Une fois le groupe de ressources déployé, un résumé du déploiement apparaît.

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

     Pour exécuter un déploiement complet, définissez le paramètre **Mode** sur la valeur **Complete**. Notez que vous êtes invité à confirmer que vous voulez utiliser le mode complet, qui peut impliquer la suppression de ressources.

          PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -Mode Complete -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> 
          Confirm
          Are you sure you want to use the complete deployment mode? Resources in the resource group 'ExampleResourceGroup' which are not
          included in the template will be deleted.
          [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

6. Pour obtenir des informations sur les échecs de déploiement.

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -Name ExampleDeployment
        
        
### Vidéo

Voici une vidéo expliquant comment utiliser les modèles Resource Manager avec PowerShell.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Installer Azure CLI pour Mac, Linux et Windows

Si vous n’avez pas déjà utilisé Azure CLI avec Azure Resource Manager, consultez [Utilisation d’Azure CLI pour Mac, Linux et Windows avec Azure Resource Management](xplat-cli-azure-resource-manager.md).

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

     - Utiliser des paramètres incorporés et un modèle local. Chaque paramètre présente le format suivant : `"ParameterName": { "value": "ParameterValue" }`. L’exemple ci-dessous montre les paramètres avec des caractères d’échappement.

             azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - Utiliser des paramètres incorporés et un lien vers un modèle.

             azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

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
   
3. Créez un déploiement de groupe de ressources. Fournissez votre ID abonnement, le nom du groupe de ressources à déployer, le nom du déploiement et l’emplacement de votre modèle. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](./#parameter-file). Pour plus d’informations sur l’API REST et créer un groupe de ressources, consultez [Créer un déploiement de modèle](https://msdn.microsoft.com/library/azure/dn790564.aspx). Notez que le **mode** est défini sur **Incrémentiel**. Pour exécuter un déploiement complet, définissez **mode** sur **Complet**.
    
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

## Déployer avec Visual Studio

Avec Visual Studio, vous pouvez créer un projet de groupe de ressources et le déployer vers Azure via l’interface utilisateur. Vous sélectionnez le type de ressources à inclure dans votre projet et ces ressources sont automatiquement ajoutées au modèle de gestionnaire de ressources. Le projet fournit également un script PowerShell pour déployer le modèle.

Pour une introduction à l’utilisation de Visual Studio avec les groupes de ressources, consultez [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Déploiement avec le portail

Devinez quoi ? Chaque application que vous créez par le biais du [portail](https://portal.azure.com/) s’appuie sur un modèle Azure Resource Manager. En créant simplement une machine virtuelle, un réseau virtuel, un compte de stockage, un service d’application ou une base de données via le portail, vous profitez déjà des avantages d’Azure Resource Manager sans rien faire de plus. Sélectionnez simplement l’icône **Nouveau** pour déployer une application avec Azure Resource Manager.

![Nouveau](./media/resource-group-template-deploy/new.png)

Pour plus d’informations sur l’utilisation du portail avec Azure Resource Manager, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](azure-portal/resource-group-portal.md).


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
- Pour obtenir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](arm-template-deployment.md).
- Pour obtenir un exemple détaillé de déploiement d’une application, consultez [Approvisionner et déployer des microservices de manière prévisible dans Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Pour obtenir de l’aide sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](solution-dev-test-environments-preview-portal.md).
- Pour en savoir plus sur les sections du modèle Azure Resource Manager, consultez [Création de modèles](resource-group-authoring-templates.md).
- Pour obtenir la liste des fonctions que vous pouvez utiliser dans un modèle Azure Resource Manager, consultez [Fonctions des modèles](resource-group-template-functions.md).

 

<!---HONumber=AcomDC_1223_2015-->