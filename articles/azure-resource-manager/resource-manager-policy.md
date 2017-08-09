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
ms.date: 07/25/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ce19c24905c9ea93a7a35134fe6035b788d8af22
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---
# <a name="resource-policy-overview"></a>Vue d’ensemble des stratégies de ressources
Les stratégies de ressources permettent d’établir des conventions pour les ressources de votre organisation. En définissant des conventions, vous pouvez contrôler les coûts et gérer plus facilement vos ressources. Par exemple, vous pouvez spécifier que seuls certains types de machines virtuelles sont autorisés, ou encore exiger que toutes les ressources présentent une balise spécifique. Toutes les ressources enfants héritent des stratégies. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle est applicable à toutes les ressources appartenant à ce groupe de ressources.

Il y a deux concepts importants à comprendre concernant les stratégies :

* Définition de stratégie : vous spécifiez à quel moment la stratégie est appliquée et l’action à exécuter.
* Affectation de stratégie : vous appliquez la définition de stratégie à une étendue (abonnement ou groupe de ressources).

Cette rubrique porte sur la définition de stratégie. Pour plus d’informations sur l’affectation des stratégies, consultez [Utiliser le portail Azure pour attribuer et gérer les stratégies de ressources](resource-manager-policy-portal.md) ou [Attribuer et gérer les stratégies de ressources via un script](resource-manager-policy-create-assign.md).

Les stratégies sont évaluées lors de la création et de la mise à jour des ressources (opérations PUT et PATCH).

> [!NOTE]
> Actuellement, la stratégie n'évalue pas les types de ressources qui ne prennent pas en charge les balises, le type et l'emplacement, par exemple, le type de ressource Microsoft.Resources/deployments. Cette prise en charge sera ajoutée prochainement. Pour éviter des problèmes de compatibilité descendante, vous devriez spécifier explicitement le type lors de la création de stratégies. Par exemple, une stratégie de balises sans spécification des types est appliquée à tous les types. Dans ce cas, le déploiement d’un modèle risque d’échouer s’il existe une ressource imbriquée ne prenant pas en charge les balises, et le type de ressource du déploiement sera ajouté à l’évaluation de la stratégie. 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>Quelle est la différence avec RBAC ?
Il existe quelques différences importantes entre la stratégie et le contrôle d’accès en fonction du rôle (RBAC). RBAC porte sur les actions des **utilisateurs** dans différentes étendues. Par exemple, le rôle de contributeur vous est attribué pour un groupe de ressources dans l’étendue de votre choix, ce qui vous permet d’apporter des modifications à ce groupe de ressources. La stratégie se focalise sur les propriétés des **ressources** pendant le déploiement. Par exemple, avec les stratégies, vous pouvez contrôler les types de ressources qui peuvent être mises en service ou restreindre les emplacements dans lesquels les ressources peuvent être mises en service. Contrairement à RBAC, la stratégie est, par défaut, un système explicite d'autorisation et de refus. 

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
La stratégie prend en charge trois types d’effet : `deny`, `audit` et `append`. 

* **deny** génère un événement dans le journal d’audit et fait échouer la requête.
* **audit** génère un événement d’avertissement dans le journal d’audit, mais ne fait pas échouer la requête.
* **append** ajoute l’ensemble de champs défini à la requête. 

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

## <a name="aliases"></a>Alias

Les alias de propriété permettent d’accéder aux propriétés spécifiques d’un type de ressource. 

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


## <a name="policy-examples"></a>Exemples de stratégies

Les rubriques suivantes contiennent des exemples de stratégies :

* Pour obtenir des exemples de stratégies de balises, consultez [Apply resource policies for tags](resource-manager-policy-tags.md) (Appliquer des stratégies de ressources pour les balises).
* Pour obtenir des exemples de modèles d’affectation de noms et de texte, consultez [Appliquer des stratégies de ressources pour les noms et le texte](resource-manager-policy-naming-convention.md).
* Pour obtenir des exemples de stratégies de balises, consultez [Apply resource policies to storage accounts](resource-manager-policy-storage.md) (Appliquer des stratégies de ressources aux comptes de stockage).
* Pour obtenir des exemples de stratégies de machine virtuelle, consultez [Apply resource policies to Linux VMs](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) (Appliquer des stratégies de ressources aux machines virtuelles Linux) et [Apply resource policies to Windows VMs](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) (Appliquer des stratégies de ressources aux machines virtuelles Windows).


## <a name="next-steps"></a>Étapes suivantes
* Après avoir défini une règle de stratégie, affectez-la à une étendue. Pour affecter des stratégies via le portail, consultez [Utiliser le portail Azure pour affecter et gérer les stratégies de ressources](resource-manager-policy-portal.md). Pour affecter des stratégies via l’API REST, PowerShell ou Azure CLI, consultez [Affecter et gérer des stratégies via un script](resource-manager-policy-create-assign.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
* Le schéma de stratégie est publié à l’adresse [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 


