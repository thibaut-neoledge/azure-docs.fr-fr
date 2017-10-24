---
title: "Stratégies de ressources Azure | Microsoft Docs"
description: "Explique comment utiliser les stratégies d’Azure Resource Manager afin de garantir la définition de propriétés de ressource cohérentes pendant le déploiement. Les stratégies peuvent être appliquées au niveau de l’abonnement ou des groupes de ressources."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: tomfitz
ms.openlocfilehash: cfdbf35b76b6a7f3cddb2deb35dfc475e0fc600f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="resource-policy-overview"></a>Vue d’ensemble des stratégies de ressources
Les stratégies de ressources permettent d’établir des conventions pour les ressources de votre organisation. En définissant des conventions, vous pouvez contrôler les coûts et gérer plus facilement vos ressources. Par exemple, vous pouvez spécifier que seuls certains types de machines virtuelles sont autorisés. Vous pouvez aussi exiger que toutes les ressources soient marquées. Toutes les ressources enfants héritent des stratégies. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle est applicable à toutes les ressources appartenant à ce groupe de ressources.

Il y a deux concepts importants à comprendre concernant les stratégies :

* Définition de stratégie : vous spécifiez à quel moment la stratégie est appliquée et l’action à exécuter.
* Affectation de stratégie : vous appliquez la définition de stratégie à une étendue (abonnement ou groupe de ressources).

Cette rubrique porte sur la définition de stratégie. Pour plus d’informations sur l’affectation des stratégies, consultez [Utiliser le portail Azure pour attribuer et gérer les stratégies de ressources](resource-manager-policy-portal.md) ou [Attribuer et gérer les stratégies de ressources via un script](resource-manager-policy-create-assign.md).

Les stratégies sont évaluées lors de la création et de la mise à jour des ressources (opérations PUT et PATCH).

## <a name="how-is-it-different-from-rbac"></a>Quelle est la différence avec RBAC ?
Il existe quelques différences importantes entre la stratégie et le contrôle d’accès en fonction du rôle (RBAC). RBAC porte sur les actions des **utilisateurs** dans différentes étendues. Par exemple, le rôle de contributeur vous est attribué pour un groupe de ressources dans l’étendue de votre choix, ce qui vous permet d’apporter des modifications à ce groupe de ressources. La stratégie se focalise sur les propriétés des **ressources** pendant le déploiement. Par exemple, vous pouvez utiliser des stratégies pour contrôler les types de ressources qui peuvent être approvisionnées. Vous pouvez aussi restreindre les emplacements auxquels les ressources peuvent être approvisionnées. Contrairement à RBAC, la stratégie est, par défaut, un système explicite d'autorisation et de refus. 

Pour utiliser des stratégies, vous devez vous authentifier au moyen de RBAC. Plus précisément, votre compte a besoin de :

* l’autorisation `Microsoft.Authorization/policydefinitions/write` pour définir une stratégie ;
* l’autorisation `Microsoft.Authorization/policyassignments/write` pour affecter une stratégie. 

Ces autorisations ne sont pas incluses dans le rôle **Contributeur**.

## <a name="built-in-policies"></a>Stratégies prédéfinies

Azure fournit certaines définitions de stratégie intégrées qui peuvent réduire le nombre de stratégies à définir. Avant de procéder à des définitions de stratégie, vous devez vous demander si une stratégie intégrée fournit déjà la définition dont vous avez besoin. Les définitions de stratégie intégrée sont les suivantes :

* Emplacements autorisés
* Types de ressources autorisés
* Références SKU de compte de stockage autorisées
* Références SKU de machine virtuelle autorisées
* Appliquer la valeur par défaut et la balise
* Appliquer une balise et une valeur
* Types de ressources non autorisés
* Nécessitent SQL Server version 12.0
* Nécessitent le chiffrement du compte de stockage

Vous pouvez affecter l’une de ces stratégies par le biais du [portail](resource-manager-policy-portal.md), de [PowerShell](resource-manager-policy-create-assign.md#powershell) ou d’[Azure CLI](resource-manager-policy-create-assign.md#azure-cli).

## <a name="policy-definition-structure"></a>Structure de la définition de stratégie
Vous devez utiliser JSON pour créer une définition de stratégie. La définition de stratégie contient des éléments pour :

* le mode
* parameters
* le nom d’affichage
* description
* la règle de stratégie
  * évaluation logique
  * effet

L’exemple suivant illustre une stratégie qui limite les emplacements où les ressources sont déployées :

```json
{
  "properties": {
    "mode": "all",
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

## <a name="mode"></a>Mode

Nous vous recommandons de définir `mode` sur `all`. Quand vous le définissez sur **all**, les groupes de ressources et tous les types de ressource sont évalués pour la stratégie. Le portail utilise **all** pour toutes les stratégies. Si vous utilisez PowerShell ou Azure CLI, vous devez spécifier le paramètre `mode` et le définir sur **all**.
 
Auparavant, la stratégie était uniquement évaluée sur les types de ressource qui prenaient en charge les balises et l’emplacement. Le mode `indexed` fonctionne toujours de la sorte. Si vous utilisez le mode **all**, les stratégies sont également évaluées sur les types de ressource qui ne prennent pas en charge les balises et l’emplacement. Le [sous-réseau de réseau virtuel](https://github.com/Azure/azure-policy-samples/tree/master/samples/Network/enforce-nsg-on-subnet) est un exemple de type récemment ajouté. Les groupes de ressources sont en outre évalués quand mode est défini sur **all**. Par exemple, vous pouvez [appliquer des balises sur un groupe de ressources](https://github.com/Azure/azure-policy-samples/tree/master/samples/ResourceGroup/enforce-resourceGroup-tags). 

## <a name="parameters"></a>Paramètres
Les paramètres permettent de simplifier la gestion des stratégies en réduisant le nombre de définitions de stratégies. Vous définissez une stratégie pour une propriété de ressource (vous limitez par exemple les emplacements où les ressources peuvent être déployées) et incluez des paramètres dans la définition. Ensuite, vous réutilisez cette définition de stratégie pour différents scénarios en transmettant différentes valeurs (vous spécifiez par exemple un ensemble d’emplacements pour un abonnement) au moment de l’affectation de la stratégie.

Vous déclarez des paramètres lorsque vous créez des définitions de stratégies.

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

Le type d’un paramètre peut être une chaîne ou un tableau. La propriété de métadonnées est utilisée pour des outils comme le Portail Azure pour afficher des informations conviviales. 

Dans la règle de stratégie, vous référencez des paramètres avec la syntaxe suivante : 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Nom d’affichage et description

Vous utilisez **displayName** et **description** pour distinguer la définition de stratégie et préciser le contexte d’utilisation.

## <a name="policy-rule"></a>Règle de stratégie

La règle de stratégie se compose de blocs **if** et **then**. Dans le bloc **if**, vous définissez une ou plusieurs conditions qui spécifient à quel moment la stratégie est mise en œuvre. Vous pouvez appliquer des opérateurs logiques à ces conditions pour définir avec précision le scénario d’une stratégie. Dans le bloc **then**, vous définissez l’effet qui se produit lorsque les conditions de **si** sont remplies.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>Opérateurs logiques
Les opérateurs logiques pris en charge sont les suivants :

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

La syntaxe **not** inverse le résultat de la condition. La syntaxe **allOf** (semblable à l’opération logique **And**) nécessite que toutes les conditions soient remplies. La syntaxe **anyOf** (semblable à l’opération logique **Of**) nécessite qu’au moins une des conditions soit remplie.

Vous pouvez imbriquer des opérateurs logiques. L’exemple suivant illustre une opération **not** imbriquée dans une opération **allOf**. 

```json
"if": {
  "allOf": [
    {
      "not": {
        "field": "tags",
        "containsKey": "application"
      }
    },
    {
      "field": "type",
      "equals": "Microsoft.Storage/storageAccounts"
    }
  ]
},
```

### <a name="conditions"></a>Conditions
La condition évalue si un **champ** répond à certains critères. Les conditions prises en charge sont les suivantes :

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Lorsque vous utilisez la condition **like**, vous pouvez utiliser un caractère générique (*) dans la valeur.

Lorsque vous utilisez la condition de **correspondance**, indiquez `#` pour représenter un chiffre, `?` pour une lettre et tout autre caractère pour représenter ce caractère réel. Pour obtenir des exemples, consultez [Appliquer des stratégies de ressources pour les noms et le texte](resource-manager-policy-naming-convention.md).

### <a name="fields"></a>Champs
Les conditions sont formées à partir de champs. Un champ représente des propriétés dans la charge utile de la requête de ressource qui est utilisée pour décrire l'état de la ressource.  

Les champs suivants sont pris en charge :

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* alias de propriété : pour en obtenir la liste, consultez [Alias](#aliases).

### <a name="effect"></a>Résultat
La stratégie prend en charge cinq types d’effet : `deny`, `audit`, `append`, `AuditIfNotExists` et `DeployIfNotExists`. 

* **deny** génère un événement dans le journal d’audit et fait échouer la requête.
* **audit** génère un événement d’avertissement dans le journal d’audit, mais ne fait pas échouer la requête.
* **append** ajoute l’ensemble de champs défini à la requête. 
* **AuditIfNotExists** active l’audit si une ressource n’existe pas.
* **DeployIfNotExists** déploie une ressource si elle n’existe pas déjà. Actuellement, cet effet est uniquement pris en charge par le biais de stratégies intégrées.

Pour **append**, vous devez fournir les détails suivants :

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

La valeur peut être une chaîne ou un objet au format JSON. 

Avec **AuditIfNotExists** et **DeployIfNotExists**, vous pouvez évaluer l’existence d’une ressource enfant et appliquer une règle quand cette ressource n’existe pas. Par exemple, vous pouvez exiger qu’un observateur réseau soit déployé pour tous les réseaux virtuels.

Pour obtenir un exemple d’audit quand une extension de machine virtuelle n’est pas déployée, consultez [Auditer des extensions de machine virtuelle](https://github.com/Azure/azure-policy-samples/blob/master/samples/Compute/audit-vm-extension/azurepolicy.json).

## <a name="aliases"></a>Alias

Les alias de propriété permettent d’accéder aux propriétés spécifiques d’un type de ressource. Les alias permettent de restreindre les valeurs ou les conditions autorisées pour la propriété d’une ressource. Chaque alias correspond aux chemins des différentes versions d’API d’un type de ressource donné. Lors de l’évaluation de la stratégie, le moteur de stratégie obtient le chemin de la propriété de cette version de l’API.

**Microsoft.Cache/Redis**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Définissez si le port du serveur Redis non-ssl (6379) est activé. |
| Microsoft.Cache/Redis/shardCount | Définissez le nombre de partitions à créer sur un cache de cluster Premium.  |
| Microsoft.Cache/Redis/sku.capacity | Définissez la taille du cache Redis à déployer.  |
| Microsoft.Cache/Redis/sku.family | Définissez la famille de références (SKU) à utiliser. |
| Microsoft.Cache/Redis/sku.name | Définissez le type de cache Redis à déployer. |

**Microsoft.Cdn/profiles**

| Alias | Description |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Définissez le nom du niveau tarifaire. |

**Microsoft.Compute/disks**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Définissez l’offre de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imagePublisher | Définissez le serveur de publication de l’image de plateforme ou de l’image de Place de marché utilisé pour créer la machine virtuelle. |
| Microsoft.Compute/imageSku | Définissez la référence SKU de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imageVersion | Définissez la version de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |


**Microsoft.Compute/virtualMachines**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Compute/imageId | Définissez l’identificateur de l’image utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imageOffer | Définissez l’offre de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imagePublisher | Définissez le serveur de publication de l’image de plateforme ou de l’image de Place de marché utilisé pour créer la machine virtuelle. |
| Microsoft.Compute/imageSku | Définissez la référence SKU de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imageVersion | Définissez la version de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/licenseType | Définissez si l’image ou le disque est sous licence en local. Cette valeur est utilisée uniquement pour les images qui contiennent le système d’exploitation Windows Server.  |
| Microsoft.Compute/virtualMachines/imageOffer | Définissez l’offre de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/virtualMachines/imagePublisher | Définissez le serveur de publication de l’image de plateforme ou de l’image de Place de marché utilisé pour créer la machine virtuelle. |
| Microsoft.Compute/virtualMachines/imageSku | Définissez la référence SKU de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/virtualMachines/imageVersion | Définissez la version de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Définissez l’URI du disque dur virtuel. |
| Microsoft.Compute/virtualMachines/sku.name | Définissez la taille de la machine virtuelle. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Définissez le nom du serveur de publication de l’extension. |
| Microsoft.Compute/virtualMachines/extensions/type | Définissez le type d’extension. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Définissez la version de l’extension. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Compute/imageId | Définissez l’identificateur de l’image utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imageOffer | Définissez l’offre de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imagePublisher | Définissez le serveur de publication de l’image de plateforme ou de l’image de Place de marché utilisé pour créer la machine virtuelle. |
| Microsoft.Compute/imageSku | Définissez la référence SKU de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imageVersion | Définissez la version de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/licenseType | Définissez si l’image ou le disque est sous licence en local. Cette valeur est utilisée uniquement pour les images qui contiennent le système d’exploitation Windows Server. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | Définissez le préfixe du nom de l’ordinateur pour toutes les machines virtuelles dans le groupe identique. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | Définisez l’URI de blob pour l’image utilisateur. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | Définissez les URL de conteneur utilisées afin de stocker les disques du système d’exploitation pour le groupe identique. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | Définissez la taille des machines virtuelles dans un groupe identique. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | Définissez le niveau des machines virtuelles dans un groupe identique. |
  
**Microsoft.Network/applicationGateways**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Définissez la taille de la passerelle. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Définissez le type de cette passerelle de réseau virtuel. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Définissez la référence SKU de la passerelle. |

**Microsoft.Sql/servers**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Définissez la version du serveur. |

**Microsoft.Sql/databases**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Définissez la modification de la base de données. |
| Microsoft.Sql/servers/databases/elasticPoolName | Définissez le nom du pool élastique dans lequel se trouve la base de données. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Définissez l’ID d’objectif de niveau de service configuré de la base de données. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Définissez le nom de l’objectif de niveau de service configuré de la base de données.  |

**Microsoft.Sql/elasticpools**

| Alias | Description |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Définissez la valeur DTU partagée totale pour le pool élastique de la base de données. |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Définissez la modification du pool élastique. |

**Microsoft.Storage/storageAccounts**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Définissez le niveau d’accès utilisé pour la facturation. |
| Microsoft.Storage/storageAccounts/accountType | Définissez le nom de la référence SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Définissez si le service chiffre les données lorsqu’elles sont stockées dans le service de stockage d’objets blob. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Définissez si le service chiffre les données lorsqu’elles sont stockées dans le service de stockage de fichiers. |
| Microsoft.Storage/storageAccounts/sku.name | Définissez le nom de la référence SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Indiquez que seul le trafic https est autorisé vers le service de stockage. |

## <a name="policy-sets"></a>Jeux de stratégies

Les jeux de stratégies permettent de regrouper des définitions de stratégies associées. Le jeu de stratégies simplifie l’attribution et la gestion, car vous travaillez avec un groupe sous forme d’élément unique. Par exemple, vous pouvez regrouper toutes les stratégies de marquage associées dans un même jeu de stratégies. Au lieu d’attribuer chaque stratégie individuellement, vous appliquez le jeu de stratégies.
 
L’exemple suivant montre comment créer un jeu de stratégies pour gérer deux balises (costCenter et productName). Il utilise deux stratégies intégrées pour appliquer la valeur de balise par défaut et imposer la valeur de balise. Le jeu de stratégies déclare deux paramètres (costCenterValue et productNameValue) pour la réutilisabilité. Il fait référence aux deux définitions de stratégies intégrées plusieurs fois avec des paramètres différents. Pour chaque paramètre, vous pouvez définir soit une valeur fixe, comme indiqué pour tagName, soit un paramètre du jeu de stratégies, comme indiqué pour tagValue.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

Ajoutez un jeu de stratégies avec la commande PowerShell **New-AzureRMPolicySetDefinition**.

Pour les opérations REST, utilisez la version d’API **2017-06-01-preview**, comme indiqué dans l’exemple suivant :

```
PUT /subscriptions/<subId>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicySet?api-version=2017-06-01-preview
```

## <a name="next-steps"></a>Étapes suivantes
* Après avoir défini une règle de stratégie, affectez-la à une étendue. Pour affecter des stratégies via le portail, consultez [Utiliser le portail Azure pour affecter et gérer les stratégies de ressources](resource-manager-policy-portal.md). Pour affecter des stratégies via l’API REST, PowerShell ou Azure CLI, consultez [Affecter et gérer des stratégies via un script](resource-manager-policy-create-assign.md).
* Pour obtenir des exemples de stratégies, consultez [Dépôt GitHub de stratégies de ressources Azure](https://github.com/Azure/azure-policy-samples).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
* Le schéma de stratégie est publié à l’adresse [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

