<properties
   pageTitle="Affichage des opérations de déploiement avec l’API REST | Microsoft Azure"
   description="Explique comment utiliser l’API REST Azure Resource Manager pour détecter les problèmes de déploiement de Resource Manager."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# Afficher les opérations de déploiement avec l’API REST Azure Resource Manager

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Interface de ligne de commande Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Si vous avez obtenu une erreur lors du déploiement des ressources sur Azure, vous pouvez afficher plus de détails sur les opérations de déploiement qui ont été exécutées. L’API REST fournit les opérations qui vous permettent de rechercher les erreurs et de déterminer les corrections éventuelles.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Vous pouvez éviter certaines erreurs en validant votre modèle et votre infrastructure avant le déploiement. Vous pouvez également enregistrer des informations supplémentaires de requête et de réponse pendant le déploiement, qui pourront se révéler utiles plus tard pour la résolution des problèmes. Pour en savoir plus sur la validation et l’enregistrement d’informations de requête et de réponse, consultez [Déployer un groupe de ressources avec le modèle Azure Resource Manager](resource-group-template-deploy-rest.md).

## Résolution des problèmes avec API REST

1. Déployez vos ressources avec l’opération [Créer un modèle de déploiement](https://msdn.microsoft.com/library/azure/dn790564.aspx). Pour conserver les informations qui peuvent être utiles pour le débogage, définissez la propriété **debugSetting** dans la requête JSON comme **requestContent** et/ou **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
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
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    Par défaut, la valeur de **debugSetting** est **none**. Lorsque vous spécifiez la valeur **debugSetting**, prêtez attention au type d’informations que vous transmettez pendant le déploiement. En enregistrant des informations sur la requête ou la réponse, vous risquez d’exposer des données sensibles récupérées au cours des opérations de déploiement.

2. Récupérez des informations sur un déploiement avec l’opération [Obtenir des informations sur le déploiement d’un modèle](https://msdn.microsoft.com/library/azure/dn790565.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    Dans la réponse, repérez en particulier les éléments **provisioningState**, **correlationId** et **error**. L’élément **correlationId** est utilisé pour effectuer le suivi des événements connexes et peut être utile lorsque vous travaillez avec le support technique pour résoudre un problème.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  "error": {\r\n    "message": "Conflict",\r\n    "code": "Conflict"\r\n  }\r\n}"}]
            }  
          }
        }

3. Récupérez des informations sur les opérations de déploiement avec l’opération [Répertorier toutes les opérations de déploiement de modèle](https://msdn.microsoft.com/library/azure/dn790518.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    La réponse inclut des informations sur la requête et/ou la réponse selon ce que vous avez spécifié dans la propriété **debugSetting** pendant le déploiement.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Récupérez des événements des journaux d’audit pour le déploiement avec l’opération [Répertorier les événements de gestion dans un abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## Étapes suivantes

- Pour obtenir de l’aide afin de résoudre des erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, consultez [Auditer les opérations avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant son exécution, consultez [Déployer un groupe de ressources avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0615_2016-->