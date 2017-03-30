---
title: "Attribuer et gérer les stratégies de ressources Azure | Microsoft Docs"
description: "Décrit comment appliquer des stratégies de ressources Azure à des abonnements et des groupes de ressources et comment afficher les stratégies de ressources."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 5560b22f3f92a8e0a7cb8b973ef2e4c66bc32c06
ms.lasthandoff: 03/16/2017


---
# <a name="assign-and-manage-resource-policies"></a>Attribuer et gérer les stratégies de ressources

Pour implémenter une stratégie, vous devez suivre trois étapes :

1. Définir la règle de stratégie avec JSON.
2. Créer une définition de stratégie dans votre abonnement à partir du JSON que vous avez créé à l’étape précédente. Cette opération rend la stratégie attribuable mais n’applique pas les règles à votre abonnement.
3. Attribuer la stratégie à une étendue (par exemple, un groupe de ressources ou un abonnement). Les règles de la stratégie sont à présent appliquées.

Azure fournit quelques stratégies prédéfinies qui peuvent réduire le nombre de stratégies à définir. Si une stratégie prédéfinie fonctionne pour votre scénario, ignorez les deux premières étapes et attribuez la stratégie prédéfinie à une étendue.

Cette rubrique se concentre sur les étapes de création d’une définition de stratégie et d’attribution de cette définition à une étendue. Elle n’aborde pas la syntaxe de création de la définition de stratégie. Pour plus d’informations sur la syntaxe des stratégies, consultez la page [Vue d’ensemble des stratégies de ressources](resource-manager-policy.md).

## <a name="rest-api"></a>API REST

### <a name="create-policy-definition"></a>Créer une définition de stratégie

Vous pouvez créer une stratégie avec l’ [API REST pour les définitions de stratégies](/rest/api/resources/policydefinitions). L’API REST vous permet de créer et de supprimer des définitions de stratégies, ainsi que d’obtenir des informations sur les définitions existantes.

Pour créer une définition de stratégie, exécutez la commande suivante :

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Incluez un texte de demande semblable à l’exemple suivant :

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>Attribuer la stratégie

Vous pouvez appliquer la définition de stratégie à l’étendue souhaitée via l’ [API REST pour les affectations de stratégies](/rest/api/resources/policyassignments). L’API REST vous permet de créer et de supprimer des affectations de stratégies, ainsi que d’obtenir des informations sur les affectations existantes.

Pour créer une affectation de stratégie, exécutez :

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

{policyAssignmentName} correspond au nom de l’affectation de stratégie.

Incluez un texte de demande semblable à l’exemple suivant :

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>Afficher la stratégie
Pour obtenir une stratégie, utilisez l’opération [Obtenir la définition de stratégie](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

### <a name="get-aliases"></a>Obtenir les alias
Vous pouvez récupérer les alias avec l’API REST :

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

L'exemple suivant montre une définition d’alias : Comme vous pouvez le voir, un alias définit des chemins dans différentes versions d'API, même en cas de changement de nom de propriété. 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

### <a name="create-policy-definition"></a>Créer une définition de stratégie
Vous pouvez créer une définition de stratégie en utilisant l’applet de commande `New-AzureRmPolicyDefinition`. L’exemple suivant crée une définition de stratégie qui permet d’autoriser uniquement les ressources en Europe du Nord et en Europe de l’Ouest.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{
   "if": {
     "not": {
       "field": "location",
       "in": "[parameters(''allowedLocations'')]"
     }
   },
   "then": {
     "effect": "deny"
   }
 }' -Parameter '{
     "allowedLocations": {
       "type": "array",
       "metadata": {
         "description": "An array of permitted locations for resources.",
         "strongType": "location",
         "displayName": "List of locations"
       }
     }
 }'
```            

Le résultat est stocké dans un objet `$policy` utilisé lors de l’attribution de la stratégie. 

Au lieu de spécifier le JSON comme paramètre, vous pouvez fournir le chemin d’accès à un fichier .json contenant la règle de stratégie.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy "c:\policies\storageskupolicy.json"
```

### <a name="assign-policy"></a>Attribuer la stratégie

Appliquez la stratégie à l’étendue souhaitée à l’aide de l’applet de commande `New-AzureRmPolicyAssignment` :

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$array = @("West US", "West US 2")
$param = @{"allowedLocations"=$array}
New-AzureRMPolicyAssignment -Name regionPolicyAssignment -Scope $rg.ResourceId -PolicyDefinition $policy -PolicyParameterObject $param
```

### <a name="view-policies"></a>Afficher les stratégies

Pour obtenir toutes les attributions de stratégies, utilisez :

```powershell
Get-AzureRmPolicyAssignment
```

Pour obtenir une stratégie spécifique, utilisez :

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope $rg.ResourceId
```

Pour afficher la règle de stratégie pour une définition de stratégie, utilisez :

```powershell
(Get-AzureRmPolicyDefinition -Name regionPolicyDefinition).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Supprimer l’attribution de stratégie 

Pour supprimer une affectation de stratégie, utilisez :

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-20"></a>Azure CLI 2.0

### <a name="create-policy-definition"></a>Créer une définition de stratégie

Vous pouvez créer une définition de stratégie à l’aide d’Azure CLI 2.0 et de la commande de définition de stratégie. L’exemple suivant crée une stratégie permettant d’attribuer des ressources uniquement en Europe du Nord et en Europe de l’Ouest.

```azurecli
az policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --rules '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

### <a name="assign-policy"></a>Attribuer la stratégie

Vous pouvez appliquer la stratégie selon l’étendue de votre choix à l’aide de la commande d’affectation de stratégie :

```azurecli
az policy assignment create --name regionPolicyAssignment --policy regionPolicyDefinition --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-definition"></a>Afficher la définition de stratégie
Pour récupérer une définition de stratégie, utilisez la commande suivante :

```azurecli
az policy definition show --name regionPolicyAssignment
```

### <a name="remove-policy-assignment"></a>Supprimer l’attribution de stratégie 

Pour supprimer une affectation de stratégie, utilisez :

```azurecli
az policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-10"></a>Azure CLI 1.0

### <a name="create-policy-definition"></a>Créer une définition de stratégie

Vous pouvez créer une définition de stratégie à l’aide de l’interface de ligne de commande Azure avec la commande de définition de stratégie. L’exemple suivant crée une stratégie permettant d’attribuer des ressources uniquement en Europe du Nord et en Europe de l’Ouest.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

Il est possible de spécifier le chemin d’accès au fichier .json contenant la stratégie plutôt que la stratégie en ligne.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"
```

### <a name="assign-policy"></a>Attribuer la stratégie

Vous pouvez appliquer la stratégie selon l’étendue de votre choix à l’aide de la commande d’affectation de stratégie :

```azurecli
azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{policy-name} --scope    /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

L'étendue est ici le nom du groupe de ressources que vous spécifiez. Si la valeur du paramètre policy-definition-id est inconnue, il est possible de l’obtenir grâce à l’interface de ligne de commande Azure : 

```azurecli
azure policy definition show {policy-name}
```

### <a name="view-policy"></a>Afficher la stratégie
Pour obtenir une stratégie, utilisez la commande suivante :

```azurecli
azure policy definition show {definition-name} --json
```

### <a name="remove-policy-assignment"></a>Supprimer l’attribution de stratégie 

Pour supprimer une affectation de stratégie, utilisez :

```azurecli
azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).


