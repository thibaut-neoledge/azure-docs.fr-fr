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
ms.date: 02/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 6d459e37b8b39f5d76c4ec86ebb7351c783b81fb
ms.openlocfilehash: 64cb4be184e02519a6c496f8639035201ebb60f8


---
# <a name="resource-policy-overview"></a>Vue d’ensemble des stratégies de ressources
Les stratégies de ressources permettent d’établir des conventions pour les ressources de votre organisation. En définissant des conventions, vous pouvez contrôler les coûts et gérer plus facilement vos ressources. Par exemple, vous pouvez spécifier que seuls certains types de machines virtuelles sont autorisés, ou encore exiger que toutes les ressources présentent une balise spécifique. Toutes les ressources enfants héritent des stratégies. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle est applicable à toutes les ressources appartenant à ce groupe de ressources.

Il y a deux concepts importants à comprendre concernant les stratégies :

* Définition de stratégie : vous spécifiez à quel moment la stratégie est appliquée et l’action à exécuter.
* Affectation de stratégie : vous appliquez la définition de stratégie à une étendue (abonnement ou groupe de ressources).

Cette rubrique porte sur la définition de stratégie. Pour plus d’informations sur l’affectation de stratégie, consultez [Affecter et gérer les stratégies](resource-manager-policy-create-assign.md).

Azure fournit certaines définitions de stratégie intégrées qui peuvent réduire le nombre de stratégies à définir. Si une définition de stratégie intégrée fonctionne pour votre scénario, utilisez cette définition lors de l’affectation à une étendue.

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

Vous pouvez imbriquer des opérateurs logiques. L’exemple suivant illustre une opération **not** imbriquée dans une opération **And**. 

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
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Lorsque vous utilisez la condition **like**, vous pouvez utiliser un caractère générique (*) dans la valeur.

### <a name="fields"></a>Champs
Les conditions sont formées à partir de champs. Un champ représente des propriétés dans la charge utile de la requête de ressource qui est utilisée pour décrire l'état de la ressource.  

Les champs suivants sont pris en charge :

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* Alias de propriété

Les alias de propriété permettent d’accéder aux propriétés spécifiques d’un type de ressource. Les alias pris en charge sont les suivants :

* Microsoft.CDN/profiles/sku.name
* Microsoft.Compute/virtualMachines/imageOffer
* Microsoft.Compute/virtualMachines/imagePublisher
* Microsoft.Compute/virtualMachines/sku.name
* Microsoft.Compute/virtualMachines/imageSku 
* Microsoft.Compute/virtualMachines/imageVersion
* Microsoft.SQL/servers/databases/edition
* Microsoft.SQL/servers/databases/elasticPoolName
* Microsoft.SQL/servers/databases/requestedServiceObjectiveId
* Microsoft.SQL/servers/databases/requestedServiceObjectiveName
* Microsoft.SQL/servers/elasticPools/dtu
* Microsoft.SQL/servers/elasticPools/edition
* Microsoft.SQL/servers/version
* Microsoft.Storage/storageAccounts/accessTier
* Microsoft.Storage/storageAccounts/enableBlobEncryption
* Microsoft.Storage/storageAccounts/sku.name
* Microsoft.Web/serverFarms/sku.name

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

## <a name="policy-examples"></a>Exemples de stratégies

Les rubriques suivantes contiennent des exemples de stratégies :

* Pour obtenir des exemples de stratégies de balises, consultez [Apply resource policies for tags](resource-manager-policy-tags.md) (Appliquer des stratégies de ressources pour les balises).
* Pour obtenir des exemples de stratégies de balises, consultez [Apply resource policies to storage accounts](resource-manager-policy-storage.md) (Appliquer des stratégies de ressources aux comptes de stockage).
* Pour obtenir des exemples de stratégies de machine virtuelle, consultez [Apply resource policies to Linux VMs](../virtual-machines/virtual-machines-linux-policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) (Appliquer des stratégies de ressources aux machines virtuelles Linux) et [Apply resource policies to Windows VMs](../virtual-machines/virtual-machines-windows-policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) (Appliquer des stratégies de ressources aux machines virtuelles Windows).

### <a name="allowed-resource-locations"></a>Emplacements de ressources autorisés
Pour spécifier les emplacements autorisés, consultez l’exemple présenté dans la section [Structure de la définition de stratégie](#policy-definition-structure). Pour affecter cette définition de stratégie, utilisez la stratégie intégrée avec l’ID de ressource `/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c`.

### <a name="not-allowed-resource-locations"></a>Emplacements de ressources non autorisés
Pour spécifier les emplacements non autorisés, utilisez la définition de stratégie suivante :

```json
{
  "properties": {
    "parameters": {
      "notAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that are not allowed when deploying resources",
          "strongType": "location",
          "displayName": "Not allowed locations"
        }
      }
    },
    "displayName": "Not allowed locations",
    "description": "This policy enables you to block locations that your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "field": "location",
        "in": "[parameters('notAllowedLocations')]"
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="allowed-resource-types"></a>Types de ressources autorisés
L’exemple suivant illustre une stratégie qui autorise uniquement les déploiements pour les types de ressources Microsoft.Resources, Microsoft.Compute, Microsoft.Storage et Microsoft.Network. Tous les autres types de ressources sont refusés :

```json
{
  "if": {
    "not": {
      "anyOf": [
        {
          "field": "type",
          "like": "Microsoft.Resources/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Compute/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Storage/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Network/*"
        }
      ]
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

### <a name="set-naming-convention"></a>Définir une convention d’affectation de noms
L’exemple suivant illustre l’utilisation de caractères génériques, grâce à la condition **like**. La condition stipule que la demande est refusée si le nom ne correspond pas au modèle indiqué (namePrefix\*nameSuffix) :

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Après avoir défini une règle de stratégie, affectez-la à une étendue. Pour plus d’informations sur l’affectation de stratégie, consultez [Affecter et gérer les stratégies](resource-manager-policy-create-assign.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
* Le schéma de stratégie est publié à l’adresse [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 




<!--HONumber=Feb17_HO3-->


