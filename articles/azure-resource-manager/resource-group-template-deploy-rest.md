---
title: "Déploiement de  ressources avec le modèle et l’API REST | Microsoft Docs"
description: "Utilisez Azure Resource Manager et l’API REST Resource Manager pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: cd28ec107c292c20cde11ed635950b42acd5d720


---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Interface de ligne de commande Azure](resource-group-template-deploy-cli.md)
> * [Portail](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Cet article explique comment utiliser l’API REST Resource Manager avec les modèles Resource Manager pour déployer vos ressources dans Azure.  

> [!TIP]
> Pour obtenir de l’aide dans le débogage d’une erreur pendant le déploiement, consultez :
> 
> * [Afficher les opérations de déploiement avec l’API REST](resource-manager-troubleshoot-deployments-rest.md) pour apprendre à récupérer des informations qui vous aideront à résoudre votre erreur
> * [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md) pour apprendre à résoudre les erreurs de déploiement courantes
> 
> 

Votre modèle peut être un fichier local ou un fichier externe disponible par le biais d’un URI. Lorsque votre modèle se trouve dans un compte de stockage, vous pouvez restreindre l’accès au modèle et fournir un jeton de signature d’accès partagé (SAP) au cours du déploiement.

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Déployer avec l’API REST
1. Définissez [des en-têtes et des paramètres communs](https://docs.microsoft.com/rest/api/index), y compris des jetons d’authentification.
2. Si vous n’avez pas de groupe de ressources, créez-en un. Fournissez votre ID abonnement, le nom du groupe de ressources et l’emplacement dont vous avez besoin pour votre solution. Pour plus d’informations, consultez [Créer un groupe de ressources](https://docs.microsoft.com/rest/api/resources/resourcegroups#ResourceGroups_CreateOrUpdate).
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
3. Validez votre déploiement avant son exécution en exécutant l’opération [Valider un déploiement de modèle](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Validate) . Lorsque vous testez le déploiement, indiquez les paramètres exactement comme vous le feriez lors de l'exécution du déploiement (voir l'étape suivante).
4. Créez un déploiement. Fournissez votre ID abonnement, le nom du groupe de ressources, le nom du déploiement et un lien vers votre modèle. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](#parameter-file). Pour plus d’informations sur l’API REST pour créer un groupe de ressources, consultez [Créer un déploiement de modèle](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Notez que le **Mode** est défini sur **Incremental (Incrémentiel)**. Pour exécuter un déploiement complet, définissez le paramètre **Mode** sur la valeur **Complete (Terminé)**. Soyez prudent lorsque vous utilisez le mode complet, car vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.
   
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
   
      Si vous souhaitez consigner le contenu de la réponse et/ou le contenu de la demande, incluez **debugSetting** dans la demande.
   
        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }
   
      Vous pouvez configurer votre compte de stockage pour qu’il utilise un jeton de signature d’accès partagé (SAP). Pour plus d’informations, consultez [Délégation de l’accès avec une signature d’accès partagé](https://docs.microsoft.com/rest/api/storageservices/fileservices/delegating-access-with-a-shared-access-signature).
5. Obtenez l’état du déploiement du modèle. Pour plus d’informations, consultez [Obtenir des informations sur le déploiement d’un modèle](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get).
   
          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## <a name="parameter-file"></a>Fichier de paramètres

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Étapes suivantes
* Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
* Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](solution-dev-test-environments.md).
* Pour plus d’informations sur l’utilisation d’une référence Key Vault pour transmettre des valeurs sécurisées, consultez [Transmettre des valeurs sécurisées pendant le déploiement](resource-manager-keyvault-parameter.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
* Pour consulter une série en quatre parties sur l’automatisation du déploiement, consultez [Automatisation des déploiements d’applications sur des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cette série couvre l’architecture, l’accès, la sécurité, la disponibilité, la mise à l’échelle et le déploiement des applications.




<!--HONumber=Nov16_HO3-->


