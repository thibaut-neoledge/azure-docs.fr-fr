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
ms.date: 09/19/2017
ms.author: tomfitz
ms.openlocfilehash: 64bdd6ed41e98079c8d4112e895aaeddcd629282
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="assign-and-manage-resource-policies"></a>Attribuer et gérer les stratégies de ressources

Pour implémenter une stratégie, vous devez suivre ces étapes :

1. Vérifiez les définitions de stratégie (y compris les stratégies intégrées fournies par Azure) pour voir s’il en existe déjà une dans votre abonnement qui répond à vos besoins.
2. S’il en existe une, obtenez son nom.
3. S’il n’en existe pas, définissez la règle de stratégie avec JSON et ajoutez-la en tant que définition de stratégie dans votre abonnement. Cette opération rend la stratégie attribuable mais n’applique pas les règles à votre abonnement.
4. Dans les deux cas, attribuez la stratégie à une étendue (par exemple, un groupe de ressources ou un abonnement). Les règles de la stratégie sont à présent appliquées.

Cet article est centré sur les étapes de création d’une définition de stratégie et d’attribution de cette définition à une étendue via l’API REST, PowerShell ou Azure CLI. Si vous préférez utiliser le portail pour affecter des stratégies, consultez [Utiliser le portail Azure pour affecter et gérer les stratégies de ressources](resource-manager-policy-portal.md). Il n’aborde pas la syntaxe de création de la définition de stratégie. Pour plus d’informations sur la syntaxe des stratégies, consultez la page [Vue d’ensemble des stratégies de ressources](resource-manager-policy.md).

## <a name="exclusion-scopes"></a>Étendues d’exclusion

Vous pouvez exclure une étendue lors de l’attribution d’une stratégie. Cette possibilité simplifie les attributions de stratégies car vous pouvez attribuer une stratégie au niveau de l’abonnement tout en spécifiant les endroits où la stratégie n’est pas appliquée. Par exemple, dans votre abonnement, vous avez un groupe de ressources destiné à l’infrastructure réseau. Les équipes d’application déploient leurs ressources dans d’autres groupes de ressources. Vous voulez éviter que ces équipes créent des ressources réseau pouvant entraîner des problèmes de sécurité. Mais dans le groupe de ressources réseau, vous souhaitez autoriser des ressources réseau. Vous pouvez attribuer la stratégie au niveau de l’abonnement, mais exclure le groupe de ressources réseau. Vous pouvez spécifier plusieurs étendues secondaires.

```json
{
    "properties":{
        "policyDefinitionId":"<ID for policy definition>",
        "notScopes":[
            "/subscriptions/<subid>/resourceGroups/networkresourceGroup1"
        ]
    }
}
```

Si vous spécifiez une étendue d’exclusion dans votre attribution, utilisez la version d’API **2017-06-01-preview**.

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

Avant de passer aux exemples PowerShell, assurez-vous que vous avez [installé la dernière version](/powershell/azure/install-azurerm-ps) d’Azure PowerShell. Les paramètres de stratégie ont été ajoutés dans la version 3.6.0. Si vous utilisez une version antérieure, les exemples renvoient une erreur indiquant que le paramètre est introuvable.

### <a name="view-policy-definitions"></a>Afficher les définitions de stratégie
Pour afficher toutes les définitions de stratégie dans votre abonnement, utilisez la commande suivante :

```powershell
Get-AzureRmPolicyDefinition
```

Elle renvoie toutes les définitions de stratégie disponibles, y compris les stratégies intégrées. Chaque stratégie est renvoyée au format suivant :

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

Avant de passer à la création d’une définition de stratégie, examinez les stratégies intégrées. Si vous trouvez une stratégie intégrée qui applique les limites dont vous avez besoin, vous pouvez ignorer la création d’une définition de stratégie. À la place, assignez la stratégie intégrée à l’étendue souhaitée.

### <a name="create-policy-definition"></a>Créer une définition de stratégie
Vous pouvez créer une définition de stratégie en utilisant l’applet de commande `New-AzureRmPolicyDefinition`.

Pour créer une définition de stratégie à partir d’un fichier, transmettez le chemin d’accès au fichier. Pour un fichier externe, utilisez :

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Pour un fichier local, utilisez :

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Pour créer une définition de stratégie avec une règle en ligne, utilisez :

```powershell
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

Le résultat est stocké dans un objet `$definition` utilisé lors de l’attribution de la stratégie. 

L’exemple suivant crée une définition de stratégie qui inclut des paramètres :

```powershell
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}' 

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters 
```

### <a name="assign-policy"></a>Attribuer la stratégie

Appliquez la stratégie à l’étendue souhaitée à l’aide de la cmdlet `New-AzureRmPolicyAssignment`. L’exemple suivant assigne la stratégie à un groupe de ressources.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Pour assigner une stratégie qui requiert des paramètres, créez un objet avec ces valeurs. L’exemple suivant récupère une stratégie intégrée et transmet les valeurs de paramètres :

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $definition -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>Afficher l’attribution de stratégie

Pour obtenir une affectation de stratégie spécifique, utilisez :

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

Pour afficher la règle de stratégie pour une définition de stratégie, utilisez :

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Supprimer l’attribution de stratégie 

Pour supprimer une affectation de stratégie, utilisez :

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>Interface de ligne de commande Azure

### <a name="view-policy-definitions"></a>Afficher les définitions de stratégie
Pour afficher toutes les définitions de stratégie dans votre abonnement, utilisez la commande suivante :

```azurecli
az policy definition list
```

Elle renvoie toutes les définitions de stratégie disponibles, y compris les stratégies intégrées. Chaque stratégie est renvoyée au format suivant :

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

Avant de passer à la création d’une définition de stratégie, examinez les stratégies intégrées. Si vous trouvez une stratégie intégrée qui applique les limites dont vous avez besoin, vous pouvez ignorer la création d’une définition de stratégie. À la place, assignez la stratégie intégrée à l’étendue souhaitée.

### <a name="create-policy-definition"></a>Créer une définition de stratégie

Vous pouvez créer une définition de stratégie à l’aide d’Azure CLI avec la commande de définition de stratégie.

Pour créer une définition de stratégie avec une règle en ligne, utilisez :

```azurecli
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>Attribuer la stratégie

Vous pouvez appliquer la stratégie selon l’étendue de votre choix à l’aide de la commande d’affectation de stratégie. L’exemple suivant assigne une stratégie à un groupe de ressources.

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>Afficher l’attribution de stratégie

Pour afficher une attribution de stratégie, fournissez le nom de l’attribution de stratégie et l’étendue :

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>Supprimer l’attribution de stratégie 

Pour supprimer une affectation de stratégie, utilisez :

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).

