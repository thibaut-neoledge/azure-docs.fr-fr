---
title: "Déployer des ressources avec le modèle et PowerShell | Microsoft Docs"
description: "Utilisez Azure Resource Manager et Azure PowerShell pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5b8b293b5b37365088a3df55581be7b7bf76691c
ms.openlocfilehash: 7bc3421e00215ca4629ea11811c98e581377b24a
ms.lasthandoff: 01/18/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Interface de ligne de commande Azure](resource-group-template-deploy-cli.md)
> * [Portail](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Cette rubrique explique comment utiliser Azure PowerShell avec les modèles Resource Manager pour déployer vos ressources dans Azure. Votre modèle peut être un fichier local ou un fichier externe disponible par le biais d’un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature d’accès partagé (SAP) au cours du déploiement.

> [!TIP]
> Pour obtenir de l’aide dans le débogage d’une erreur pendant le déploiement, consultez :
> 
> * [View deployment operations (Afficher les opérations de déploiement)](resource-manager-deployment-operations.md) pour apprendre à récupérer des informations qui vous aideront à résoudre votre erreur
> * [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md) pour apprendre à résoudre les erreurs de déploiement courantes
> 
> 

## <a name="quick-steps-to-deployment"></a>Étapes à suivre pour le déploiement
Pour commencer le déploiement rapidement, utilisez les commandes suivantes :

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json -TemplateParameterFile c:\MyTemplates\example.params.json
```

Ces commandes créent un groupe de ressources et déploient un modèle dans ce groupe de ressources. Le fichier de modèle et le fichier de paramètres sont deux fichiers locaux. Si cela fonctionne, vous avez tout ce dont vous avez besoin pour déployer des ressources. Toutefois, davantage d’options sont disponibles pour spécifier les ressources à déployer. Le reste de cet article décrit toutes les options de déploiement disponibles. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>Déployer
1. Connectez-vous à votre compte Azure.

   ```powershell
   Login-AzureRmAccount
   ```

    Un résumé de votre compte est renvoyé.
    
   ```powershell
   Environment           : AzureCloud
   Account               : someone@example.com
   TenantId              : {guid}
   SubscriptionId        : {guid}
   SubscriptionName      : Example Subscription
   CurrentStorageAccount :
   ```

2. Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement avec la commande **Set-AzureRmContext**. 
   
   ```powershell
   Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
   ```
3. Lorsque vous déployez un modèle, vous devez spécifier un groupe de ressources qui contiendra les ressources déployées. Si vous disposez déjà d’un groupe de ressources sur lequel vous souhaitez effectuer le déploiement, vous pouvez ignorer cette étape et utiliser ce groupe de ressources. 
   
     Afin de créer un groupe de ressources, fournissez un nom et un emplacement pour votre groupe de ressources. Vous fournissez un emplacement pour le groupe de ressources, car celui-ci stocke des métadonnées concernant les ressources. Pour des raisons de conformité, vous souhaiterez peut-être indiquer où sont stockées métadonnées. En règle générale, nous vous recommandons de spécifier l’emplacement où réside la plupart de vos ressources. L’utilisation du même emplacement permet de simplifier votre modèle.
   
   ```powershell
   New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   ```
   
     Un résumé du nouveau groupe de ressources est retourné.
4. Avant d’exécuter votre déploiement, vous pouvez valider vos paramètres de déploiement. L’applet de commande **Test-AzureRmResourceGroupDeployment** vous permet de détecter les problèmes avant de créer des ressources réelles. L’exemple suivant indique comment valider un déploiement.
   
   ```powershell
   Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>
   ```
5. Pour déployer des ressources pour votre groupe de ressources, exécutez la commande **New-AzureRmResourceGroupDeployment** et fournissez les paramètres nécessaires. Les paramètres comprennent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL du modèle et tous les autres paramètres nécessaires à votre scénario. Si le paramètre **Mode** n’est pas spécifié, la valeur par défaut **Incremental** est utilisée. Pour exécuter un déploiement complet, définissez le paramètre **Mode** sur la valeur **Complete (Terminé)**. Soyez prudent lorsque vous utilisez le mode complet, car vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.
   
     Pour déployer un modèle local, utilisez le paramètre **TemplateFile** :
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json
   ```
   
     Pour déployer un modèle externe, utilisez le paramètre **TemplateUri** :
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json
   ```
   
     Les deux exemples précédents n’incluent pas de valeurs de paramètre. Vous découvrez les options de transmission des valeurs de paramètre dans la section [Parameters](#parameters). À ce stade, vous êtes invité à fournir des valeurs de paramètre avec la syntaxe suivante :

   ```powershell  
   cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
   Supply values for the following parameters:
   (Type !? for Help.)
   firstParameter: <type here>
   ```
     
     Une fois les ressources déployées, un résumé du déploiement apparaît. Le résumé inclut un **ProvisioningState**, qui indique si le déploiement a réussi.

   ```powershell   
   DeploymentName    : ExampleDeployment
   ResourceGroupName : ExampleResourceGroup
   ProvisioningState : Succeeded
   Timestamp         : 4/14/2015 7:00:27 PM
   Mode              : Incremental
   ```
      
6. Si vous souhaitez consigner des informations supplémentaires sur le déploiement qui peuvent vous aider à résoudre des erreurs de déploiement, utilisez le paramètre **DeploymentDebugLogLevel**. Vous pouvez demander à ce que le contenu de la demande et/ou de la réponse soit consigné avec l’opération de déploiement.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
   ```
   
     Pour plus d’informations sur la résolution des problèmes liés aux déploiements, consultez [Voir les opérations de déploiement](resource-manager-deployment-operations.md).

## <a name="deploy-private-template-with-sas-token"></a>Déploiement d’un modèle privé avec un jeton SAP
Vous pouvez ajouter vos modèles à un compte de stockage et les lier au cours du déploiement avec un jeton SAP.

> [!IMPORTANT]
> Une fois les étapes ci-dessous suivies, l’objet blob contenant le modèle n’est accessible qu’au propriétaire du compte. Toutefois, lorsque vous créez un jeton SAP pour l’objet blob, celui-ci est accessible à toute personne ayant cet URI. Si un autre utilisateur intercepte l’URI, il pourra accéder au modèle. L’utilisation d’un jeton SAP est un bon moyen de limiter l’accès à vos modèles, mais vous ne devez pas inclure de données sensibles comme des mots de passe directement dans le modèle.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Ajouter un modèle privé au compte de stockage
Les étapes suivantes configurent un compte de stockage pour les modèles :

1. Créez un groupe de ressources
   
   ```powershell
   New-AzureRmResourceGroup -Name ManageGroup -Location "West US"
   ```
2. Créez un compte de stockage. Le nom du compte de stockage doit être unique dans Azure : fournissez donc votre propre nom pour le compte.
   
   ```powershell
   New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"
   ```
3. Définissez le compte de stockage actuel.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
4. Créez un conteneur. L’autorisation est définie sur **Désactivée** , ce qui signifie que le conteneur n’est accessible qu’au propriétaire.
   
   ```powershell
   New-AzureStorageContainer -Name templates -Permission Off
   ```
5. Ajoutez votre modèle au conteneur.
   
   ```powershell
   Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>Fournir un jeton SAP au cours du déploiement
Pour déployer un modèle dans un compte de stockage privé, récupérez un jeton SAP et incluez-le dans l’URI du modèle.

1. Si vous avez modifié le compte de stockage actuel, définissez comme compte de stockage actuel celui qui contient vos modèles.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
2. Créez un jeton SAP avec des autorisations de lecture et une date d’expiration pour limiter l’accès. Récupérez l’URI complet du modèle, y compris le jeton SAP.
   
   ```powershell
   $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
   ```
3. Déployez le modèle en fournissant l’URI qui inclut le jeton SAP.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri
   ```

Pour accéder à un exemple d’utilisation d’un jeton SAP avec des modèles liés, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>parameters

Vous disposez des options suivantes pour fournir les valeurs des paramètres : 
   
- Paramètres en ligne. Incluez des noms de paramètres individuels dans l’applet de commande (par exemple, **-myParameterName**).

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"
   ```
- Objet de paramètres. Incluez le paramètre **- TemplateParameterObject**.

   ```powershell   
   $parameters = @{"<ParameterName>"="<Parameter Value>"}
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters
   ```
- Fichier de paramètres local. Incluez le paramètre **- TemplateParameterFile**.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile c:\MyTemplates\example.params.json
   ```
- Fichier de paramètres externe. Incluez le paramètre **- TemplateParameterUri**.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.params.json
   ```
      
[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)] 

Si votre modèle inclut un paramètre utilisant le même nom que l’un des paramètres dans la commande PowerShell, vous êtes invité à fournir une valeur pour ce paramètre. Azure PowerShell présente le paramètre de votre modèle en incluant le suffixe **FromTemplate**. Par exemple, un paramètre nommé **ResourceGroupName** dans votre modèle est en conflit avec le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En général, vous devez éviter cette confusion en ne nommant pas les paramètres avec un nom identique à celui des paramètres utilisés pour les opérations de déploiement.

## <a name="parameter-precedence"></a>Priorité des paramètres

Vous pouvez utiliser des paramètres inline et un fichier de paramètres local pendant la même opération de déploiement. Par exemple, vous pouvez spécifier certaines valeurs dans le fichier de paramètres local et ajouter d’autres valeurs inline pendant le déploiement. Si vous fournissez des valeurs pour un paramètre à la fois dans le fichier de paramètres local et inline, la valeur inline est prioritaire.

Cependant, lorsque vous utilisez un fichier de paramètres externe, vous ne pouvez pas passer d’autres valeurs inline ou tirées d’un fichier local. Lorsque vous spécifiez un fichier de paramètres dans le paramètre **TemplateParameterUri**, tous les paramètres inline sont ignorés. Fournissez toutes les valeurs de paramètre dans le fichier externe. Si votre modèle inclut une valeur sensible que vous ne pouvez pas inclure dans le fichier de paramètres, ajoutez cette valeur dans un coffre de clés, ou fournissez de manière dynamique toutes des valeurs de paramètre inline.

## <a name="next-steps"></a>Étapes suivantes
* Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
* Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](solution-dev-test-environments.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
* Pour consulter une série en quatre parties sur l’automatisation du déploiement, consultez [Automatisation des déploiements d’applications sur des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cette série couvre l’architecture, l’accès, la sécurité, la disponibilité, la mise à l’échelle et le déploiement des applications.


