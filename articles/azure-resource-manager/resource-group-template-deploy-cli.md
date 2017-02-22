---
title: "Déploiement des ressources avec le modèle et l’interface de ligne de commande Azure | Microsoft Docs"
description: "Utilisez Azure Resource Manager et l’interface de ligne de commande Azure pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4851e872349fa6483e1f1a340d0968e845a3518
ms.openlocfilehash: ccbb918a3377094395a27a9b7a63f213c5085027


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Interface de ligne de commande Azure](resource-group-template-deploy-cli.md)
> * [Portail](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Cette rubrique explique comment utiliser l’interface de ligne de commande Azure avec les modèles Resource Manager pour déployer vos ressources dans Azure.  Votre modèle peut être un fichier local ou un fichier externe disponible par le biais d’un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature d’accès partagé (SAP) au cours du déploiement.

> [!TIP]
> Pour obtenir de l’aide dans le débogage d’une erreur pendant le déploiement, consultez :
> 
> * [View deployment operations (Afficher les opérations de déploiement)](resource-manager-deployment-operations.md) pour apprendre à récupérer des informations qui vous aideront à résoudre votre erreur
> * [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md) pour apprendre à résoudre les erreurs de déploiement courantes
> 
> 

## <a name="quick-steps-to-deployment"></a>Étapes à suivre pour le déploiement
Pour commencer le déploiement rapidement, utilisez les commandes suivantes :

```
azure group create -n examplegroup -l "West US"
azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g examplegroup -n exampledeployment
```

Ces commandes créent un groupe de ressources et déploient un modèle dans ce groupe de ressources. Le fichier de modèle et le fichier de paramètres sont deux fichiers locaux. Si cela fonctionne, vous avez tout ce dont vous avez besoin pour déployer des ressources. Toutefois, davantage d’options sont disponibles pour spécifier les ressources à déployer. Le reste de cet article décrit toutes les options de déploiement disponibles. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>Déployer
Si vous n’avez pas déjà utilisé Azure CLI avec Azure Resource Manager, consultez [Utilisation d’Azure CLI pour Mac, Linux et Windows avec Azure Resource Management](xplat-cli-azure-resource-manager.md).

1. Connectez-vous à votre compte Azure. Une fois que vous avez entré vos informations d'identification, la commande retourne le résultat de votre connexion.
   
   ```
   azure login
   ```
2. Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement.
   
   ```
   azure account set <YourSubscriptionNameOrId>
   ```
4. Lorsque vous déployez un modèle, vous devez spécifier un groupe de ressources qui contiendra les ressources déployées. Si vous disposez déjà d’un groupe de ressources sur lequel vous souhaitez effectuer le déploiement, vous pouvez ignorer cette étape et utiliser ce groupe de ressources. 
   
     Afin de créer un groupe de ressources, fournissez un nom et un emplacement pour votre groupe de ressources. Vous fournissez un emplacement pour le groupe de ressources, car celui-ci stocke des métadonnées concernant les ressources. Pour des raisons de conformité, vous souhaiterez peut-être indiquer où sont stockées métadonnées. En règle générale, nous vous recommandons de spécifier l’emplacement où réside la plupart de vos ressources. L’utilisation du même emplacement permet de simplifier votre modèle.
   
   ```
   azure group create -n ExampleResourceGroup -l "West US"
   ```

     Un résumé du nouveau groupe de ressources est retourné.
   
5. Validez votre déploiement avant son exécution en exécutant la commande **azure group template validate** . Lorsque vous testez le déploiement, indiquez les paramètres exactement comme vous le feriez lors de l'exécution du déploiement (voir l'étape suivante).
   
   ```
   azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup
   ```
6. Afin de déployer des ressources dans votre groupe de ressources, exécutez la commande de **création d’un déploiement de groupe azure** et fournissez les paramètres nécessaires. Les paramètres comprennent un nom pour votre déploiement, le nom de votre groupe de ressources, le chemin d’accès ou l’URL du modèle et tous les autres paramètres nécessaires à votre scénario. Si le paramètre **Mode** n’est pas spécifié, la valeur par défaut **Incremental** est utilisée. Pour exécuter un déploiement complet, définissez le paramètre **Mode** sur la valeur **Complete (Terminé)**. Soyez prudent lorsque vous utilisez le mode complet, car vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.
   
     Pour déployer un modèle local, utilisez le paramètre **template-file** :
   
   ```
   azure group deployment create --resource-group examplegroup --template-file "c:\MyTemplates\example.json"
   ```
   
     Pour déployer un modèle externe, utilisez le paramètre **template-uri** :
   
   ```
   azure group deployment create --resource-group examplegroup --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json"
   ```
   
     Les deux exemples précédents n’incluent pas de valeurs de paramètre. Vous découvrez les options de transmission des valeurs de paramètre dans la section [Parameters](#parameters). À ce stade, vous êtes invité à fournir des valeurs de paramètre avec la syntaxe suivante :

   ```
   info:    Executing command group deployment create
   info:    Supply values for the following parameters
   firstParameters:  <type here>
   ```

      Une fois les ressources déployées, un résumé du déploiement apparaît. Le résumé inclut un **ProvisioningState**, qui indique si le déploiement a réussi.

   ```
   + Initializing template configurations and parameters
   + Creating a deployment
   info:    Created template deployment "example"
   + Waiting for deployment to complete
   +
   +
   data:    DeploymentName     : example
   data:    ResourceGroupName  : examplegroup
   data:    ProvisioningState  : Succeeded
   ```
7. Si vous souhaitez consigner des informations supplémentaires sur le déploiement qui peuvent vous aider à résoudre des erreurs de déploiement, utilisez le paramètre **debug-setting** . Vous pouvez demander à ce que le contenu de la demande et/ou de la réponse soit consigné avec l’opération de déploiement.
   
   ```
   azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g examplegroup -n exampleDeployment
   ```

## <a name="deploy-template-from-storage-with-sas-token"></a>Déployer un modèle à partir du stockage avec un jeton SAP
Vous pouvez ajouter vos modèles à un compte de stockage et les lier au cours du déploiement avec un jeton SAP.

> [!IMPORTANT]
> Une fois les étapes ci-dessous suivies, l’objet blob contenant le modèle n’est accessible qu’au propriétaire du compte. Toutefois, lorsque vous créez un jeton SAP pour l’objet blob, celui-ci est accessible à toute personne ayant cet URI. Si un autre utilisateur intercepte l’URI, il pourra accéder au modèle. L’utilisation d’un jeton SAP est un bon moyen de limiter l’accès à vos modèles, mais vous ne devez pas inclure de données sensibles comme des mots de passe directement dans le modèle.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Ajouter un modèle privé au compte de stockage
Les étapes suivantes configurent un compte de stockage pour les modèles :

1. Créez un groupe de ressources
   
   ```
   azure group create -n "ManageGroup" -l "westus"
   ```
2. Créez un compte de stockage. Le nom du compte de stockage doit être unique dans Azure : fournissez donc votre propre nom pour le compte.
   
   ```
   azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates
   ```
3. Définissez des variables pour le compte de stockage et la clé.
   
   ```
   export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
   export AZURE_STORAGE_ACCESS_KEY={storage_account_key}
   ```
4. Créez un conteneur. L’autorisation est définie sur **Désactivée** , ce qui signifie que le conteneur n’est accessible qu’au propriétaire.
   
   ```
   azure storage container create --container templates -p Off 
   ```
5. Ajoutez votre modèle au conteneur.
   
   ```
   azure storage blob upload --container templates -f c:\MyTemplates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>Fournir un jeton SAP au cours du déploiement
Pour déployer un modèle dans un compte de stockage privé, récupérez un jeton SAP et incluez-le dans l’URI du modèle.

1. Créez un jeton SAP avec des autorisations de lecture et une date d’expiration pour limiter l’accès. Définissez le délai d’expiration de façon à laisser suffisamment de temps pour terminer le déploiement. Récupérez l’URI complet du modèle, y compris le jeton SAP.
   
   ```
   expiretime=$(date -I'minutes' --date "+30 minutes")
   fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")
   ```
2. Déployez le modèle en fournissant l’URI qui inclut le jeton SAP.
   
   ```
   azure group deployment create --template-uri $fullurl -g ExampleResourceGroup
   ```

Pour accéder à un exemple d’utilisation d’un jeton SAP avec des modèles liés, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>parameters

Vous disposez des options suivantes pour fournir les valeurs des paramètres : 
   
- Utiliser des paramètres incorporés. Chaque paramètre est au format suivant : `"ParameterName": { "value": "ParameterValue" }`. L’exemple suivant montre les paramètres avec des caractères d’échappement.

   ```   
   azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment
   ```
- Utiliser un fichier de paramètres.

  ```    
  azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g ExampleResourceGroup -n ExampleDeployment
  ```
      

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Étapes suivantes
* Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
* Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](solution-dev-test-environments.md).
* Pour plus d’informations sur l’utilisation d’une référence Key Vault pour transmettre des valeurs sécurisées, consultez [Transmettre des valeurs sécurisées pendant le déploiement](resource-manager-keyvault-parameter.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
* Pour consulter une série en quatre parties sur l’automatisation du déploiement, consultez [Automatisation des déploiements d’applications sur des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cette série couvre l’architecture, l’accès, la sécurité, la disponibilité, la mise à l’échelle et le déploiement des applications.




<!--HONumber=Jan17_HO2-->


