<properties
   pageTitle="Déployer des ressources avec le modèle et l’API REST | Microsoft Azure"
   description="Utilisez Azure Resource Manager et l’API REST Resource Manager pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/08/2016"
   ms.author="tomfitz"/>

# Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Interface de ligne de commande Azure](resource-group-template-deploy-cli.md)
- [Portail](resource-group-template-deploy-portal.md)
- [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [API REST](resource-group-template-deploy-rest.md)

Cet article explique comment utiliser l’API REST Resource Manager avec les modèles Resource Manager pour déployer vos ressources dans Azure.

> [AZURE.TIP] Pour obtenir de l’aide dans le débogage d’une erreur pendant le déploiement, consultez :
>
> - [Afficher les opérations de déploiement avec l’API REST](resource-manager-troubleshoot-deployments-rest.md) pour apprendre à récupérer des informations qui vous aideront à résoudre votre erreur
> - [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md) pour apprendre à résoudre les erreurs de déploiement courantes

[AZURE.INCLUDE [déploiements-resource-manager](../includes/resource-manager-deployments.md)]

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
   
3. Validez votre déploiement avant son exécution en exécutant l’opération [Valider un déploiement de modèle](https://msdn.microsoft.com/library/azure/dn790547.aspx). Lorsque vous testez le déploiement, indiquez les paramètres exactement comme vous le feriez lors de l'exécution du déploiement (voir l'étape suivante).

3. Créez un déploiement. Fournissez votre ID abonnement, le nom du groupe de ressources à déployer, le nom du déploiement et un lien vers votre modèle. Pour plus d’informations sur le fichier de modèle, consultez [Fichier de paramètres](./#parameter-file). Pour plus d’informations sur l’API REST et créer un groupe de ressources, consultez [Créer un déploiement de modèle](https://msdn.microsoft.com/library/azure/dn790564.aspx). Notez que le **mode** est défini sur **Incrémentiel**. Pour exécuter un déploiement complet, définissez **mode** sur **Complet**. Soyez prudent lorsque vous utilisez le mode complet, car vous pouvez supprimer par inadvertance des ressources qui ne sont pas dans votre modèle.
    
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

      Vous pouvez configurer votre compte de stockage pour qu’il utilise un jeton de signature d’accès partagé (SAP). Pour plus d’informations, consultez [Délégation d’accès avec une signature d’accès partagé](https://msdn.microsoft.com/library/ee395415.aspx).

4. Obtenez l’état du déploiement du modèle. Pour plus d’informations, consultez [Obtenir des informations sur le déploiement d’un modèle](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [fichier-de-paramètre-resource-manager](../includes/resource-manager-parameter-file.md)]

## Étapes suivantes
- Pour découvrir un exemple de déploiement de ressources par le biais de la bibliothèque cliente .NET, consultez [Déployer des ressources à l’aide de bibliothèques .NET et d’un modèle](virtual-machines/virtual-machines-windows-csharp-template.md).
- Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
- Pour obtenir des instructions sur le déploiement de votre solution dans différents environnements, consultez [Environnements de développement et de test dans Microsoft Azure](solution-dev-test-environments.md).
- Pour plus d’informations sur l’utilisation d’une référence Key Vault pour transmettre des valeurs sécurisées, consultez [Transmettre des valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0615_2016-->