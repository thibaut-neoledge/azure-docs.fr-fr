---
title: "Ressources - fonctions de modèle Azure Resource Manager | Microsoft Docs"
description: "Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour récupérer des valeurs sur les ressources."
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
ms.date: 08/09/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 494ade55f21c19d9c68d5cc52756528401d9bb77
ms.contentlocale: fr-fr
ms.lasthandoff: 08/10/2017

---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Fonctions de ressources pour les modèles Azure Resource Manager

Resource Manager offre les fonctions ci-après pour obtenir des valeurs de ressource :

* [listKeys and list{Value}](#listkeys)
* [fournisseurs](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [abonnement](#subscription)

Pour obtenir des valeurs de paramètres, de variables ou du déploiement actuel, consultez [Fonctions de valeur de déploiement](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="listkeys-and-listvalue"></a>listKeys and list{Value}
`listKeys(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

Renvoie les valeurs pour n’importe quel type de ressource qui prend en charge l’opération list. L’utilisation la plus courante est `listKeys`. 

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| nom_ressource ou identificateur_ressource |Oui |string |Identificateur unique pour la ressource. |
| apiVersion |Oui |string |Version d'API de l'état d'exécution des ressources. En règle générale, au format, **aaaa-mm-jj**. |

### <a name="return-value"></a>Valeur de retour

L’objet renvoyé par listKeys a le format suivant :

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

D’autres fonctions de liste ont différents formats de retour. Pour afficher le format d’une fonction, incluez-le dans la section des sorties comme indiqué dans l’exemple de modèle. 

### <a name="remarks"></a>Remarques

Toute opération qui commence par **list** peut être utilisée en tant que fonction dans votre modèle. Les opérations disponibles incluent non seulement listKeys, mais également des opérations telles que `list`, `listAdminKeys` et `listStatus`. Toutefois, vous ne pouvez pas utiliser les opérations de **liste** qui requièrent des valeurs dans le corps de la demande. Par exemple, l’opération de [signature d’accès partagé de compte de liste](/rest/api/storagerp/storageaccounts#StorageAccounts_ListAccountSAS) nécessite des paramètres de corps de la demande, tels que *signedExpiry* ; par conséquent, vous ne pouvez pas l’utiliser dans un modèle.

Pour déterminer les types de ressources qui ont une opération de liste, utilisez les options suivantes :

* Affichez les [opérations d’API REST](/rest/api/) pour un fournisseur de ressources et recherchez les opérations de liste. Par exemple, les comptes de stockage présentent l’[opération listKeys](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Utilisez l’applet de commande PowerShell [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation). L’exemple ci-dessous obtient toutes les opérations de liste pour les comptes de stockage :

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Utilisez la commande Azure CLI suivante pour filtrer uniquement les opérations de liste :

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

Spécifiez la ressource en utilisant la [fonction resourceId](#resourceid) ou le format `{providerNamespace}/{resourceType}/{resourceName}`.


### <a name="example"></a>Exemple

L’exemple suivant montre comment renvoyer les clés primaires et secondaires à partir d’un compte de stockage dans la section outputs.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountId": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "storageKeysOutput": {
            "value": "[listKeys(parameters('storageAccountId'), '2016-01-01')]",
            "type" : "object"
        }
    }
}
``` 

<a id="providers" />

## <a name="providers"></a>fournisseurs
`providers(providerNamespace, [resourceType])`

Renvoie des informations sur un fournisseur de ressources et les types de ressources qu’il prend en charge. Si vous ne fournissez pas un type de ressource, la fonction renvoie tous les types pris en charge pour le fournisseur de ressources.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| espacedenoms_fournisseur |Oui |string |Espace de noms du fournisseur. |
| resourceType |Non |string |Type de ressource dans l'espace de noms spécifié. |

### <a name="return-value"></a>Valeur de retour

Chaque type pris en charge est renvoyé au format suivant : 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Le classement du tableau des valeurs renvoyées n’est pas garanti.

### <a name="example"></a>Exemple

L'exemple suivant montre comment utiliser la fonction provider :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers('Microsoft.Web', 'sites')]",
            "type" : "object"
        }
    }
}
```

L’exemple précédent renvoie un objet dans le format suivant :

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

<a id="reference" />

## <a name="reference"></a>reference
`reference(resourceName or resourceIdentifier, [apiVersion])`

Renvoie un objet représentant l’état d’exécution d’une ressource.

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| nom_ressource ou identificateur_ressource |Oui |string |Nom ou identificateur unique d’une ressource. |
| apiVersion |Non |string |Version d’API de la ressource spécifiée. Incluez ce paramètre lorsque la ressource n’est pas approvisionnée dans le même modèle. En règle générale, au format, **aaaa-mm-jj**. |

### <a name="return-value"></a>Valeur de retour

Chaque type de ressource retourne des propriétés différentes pour la fonction de référence. La fonction ne retourne pas un format prédéfini unique. Pour afficher les propriétés d’un type de ressource, renvoyez l’objet dans la section des sorties comme indiqué dans l’exemple.

### <a name="remarks"></a>Remarques

La fonction reference dérive sa valeur d'un état d'exécution, et ne peut donc pas être utilisée dans la section variables. Elle peut être utilisée dans la section outputs d'un modèle. 

En utilisant la fonction « reference », vous déclarez de manière implicite qu’une ressource dépend d’une autre ressource si la ressource référencée est configurée dans le même modèle. Vous n’avez pas besoin d’utiliser également la propriété dependsOn. La fonction n’est pas évaluée tant que le déploiement de la ressource référencée n’est pas terminé.

Pour afficher les noms et les valeurs des propriétés pour un type de ressource donné, créez un modèle qui retourne l’objet dans la section outputs. Si vous disposez déjà d’une ressource de ce type, votre modèle retourne l’objet sans déployer de nouvelles ressources. 

En règle générale, vous utilisez la fonction de **référence** pour renvoyer une valeur particulière d’un objet, telle que l’URI du point de terminaison d’objet blob ou le nom de domaine complet.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

### <a name="example"></a>Exemple

Pour déployer et faire référence à la ressource dans le même modèle, utilisez :

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      }
    }
}
``` 

L’exemple précédent renvoie un objet dans le format suivant :

```json
{
   "creationTime": "2017-06-13T21:24:46.618364Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

L’exemple ci-après référence un compte de stockage déployé dans un modèle différent. Le compte de stockage existe déjà dans le même groupe de ressources.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }
}
```

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Renvoie un objet qui représente le groupe de ressources actuel. 

### <a name="return-value"></a>Valeur de retour

L’objet renvoyé présente le format suivant :

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

### <a name="remarks"></a>Remarques

Une utilisation courante de la fonction resourceGroup consiste à créer des ressources dans le même emplacement que le groupe de ressources. L'exemple suivant utilise l'emplacement du groupe de ressources pour affecter l'emplacement d'un site web.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="example"></a>Exemple

Le modèle suivant retourne les propriétés du groupe de ressources.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

L’exemple précédent renvoie un objet dans le format suivant :

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Retourne l'identificateur unique d'une ressource. Vous utilisez cette fonction lorsque le nom de la ressource est ambigu ou non configuré dans le même modèle. 

### <a name="parameters"></a>Paramètres

| Paramètre | Requis | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |Non |string (au format GUID) |La valeur par défaut est l’abonnement actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre abonnement. |
| resourceGroupName |Non |string |La valeur par défaut est le groupe de ressources actuel. Spécifiez cette valeur lorsque vous devez récupérer une ressource se trouvant dans un autre groupe de ressources. |
| resourceType |Oui |string |Type de ressource, y compris l'espace de noms du fournisseur de ressources. |
| nom_ressource1 |Oui |string |Nom de la ressource. |
| nom_ressource2 |Non |string |Segment de nom de ressource suivant si la ressource est imbriquée. |

### <a name="return-value"></a>Valeur de retour

L'identificateur est retourné au format suivant :

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Remarques

Les valeurs de paramètre spécifiées varient selon que la ressource se trouve ou non dans le même abonnement et le même groupe de ressources que le déploiement actuel.

Pour obtenir l’ID de ressource d’un compte de stockage se trouvant dans le même abonnement et le même groupe de ressources, utilisez :

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

Pour obtenir l’ID de ressource d’un compte de stockage se trouvant dans le même abonnement mais dans un groupe de ressources différent, utilisez :

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Pour obtenir l’ID de ressource d’un compte de stockage se trouvant dans un abonnement et un groupe de ressources différents, utilisez :

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Pour obtenir l’ID de ressource d’une base de données se trouvant dans un groupe de ressources différent, utilisez :

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

Souvent, vous devez utiliser cette fonction lorsque vous utilisez un compte de stockage ou un réseau virtuel se trouvant dans un autre groupe de ressources. L'exemple suivant montre comment une ressource d'un groupe de ressources externe peut être facilement utilisée :

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="example"></a>Exemple

L’exemple suivant retourne l’ID de ressource pour un compte de stockage dans le groupe de ressources :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/{different-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

<a id="subscription" />

## <a name="subscription"></a>abonnement
`subscription()`

Retourne des détails concernant l’abonnement pour le déploiement actuel. 

### <a name="return-value"></a>Valeur de retour

La fonction retourne les informations au format suivant :

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Exemple

L’exemple suivant montre la fonction subscription appelée dans la section outputs. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).


