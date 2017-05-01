---
title: "Stratégies de ressources Azure pour les balises | Microsoft Docs"
description: "Fournit des exemples de stratégies de ressources pour gérer les balises sur les ressources."
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
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 6e71fd9eda822478fa0555aa44908a4094fe8de2
ms.lasthandoff: 03/31/2017


---
# <a name="apply-resource-policies-for-tags"></a>Appliquer des stratégies de ressources pour les balises

Cette rubrique fournit des règles de stratégie courantes que vous pouvez appliquer pour garantir une utilisation cohérente des balises sur les ressources.

Le fait d’appliquer une stratégie de balise à un groupe de ressources ou à un abonnement contenant des ressources n’applique pas rétroactivement la stratégie à ces ressources. Pour appliquer les stratégies à ces ressources, déclenchez une mise à jour des ressources existantes, comme l’indique la page [Déclencher des mises à jour sur des ressources existantes](#trigger-updates-to-existing-resources).

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Vérifier que toutes les ressources d’un groupe de ressources ont une balise / valeur

Il est souvent exigé que toutes les ressources d’un groupe de ressources aient une valeur et une balise particulières. Cette exigence est souvent nécessaire pour effectuer le suivi des coûts par département. Les conditions suivantes doivent être remplies :

* La balise et la valeur requises sont ajoutées à des ressources nouvelles et mises à jour qui ne possèdent pas encore de balises.
* La balise et la valeur requises sont ajoutées à des ressources nouvelles et mises à jour qui possèdent des balises autres que la balise et la valeur requises.
* Il n’est pas possible de supprimer la balise et la valeur requises des ressources existantes.

Pour respecter ces exigences, appliquez à un groupe de ressources les trois stratégies suivantes :

* [Ajouter une balise](#append-tag) 
* [Ajouter une balise avec d’autres balises](#append-tag-with-other-tags)
* [Exiger une balise et une valeur](#require-tag-and-value)

### <a name="append-tag"></a>Ajouter une balise

La règle de stratégie suivante ajoute la balise costCenter avec une valeur prédéfinie si aucune balise n’est présente :

```json
{
  "if": {
    "field": "tags",
    "exists": "false"
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags",
        "value": {"costCenter":"myDepartment" }
      }
    ]
  }
}
```

### <a name="append-tag-with-other-tags"></a>Ajouter une balise avec d’autres balises

La règle de stratégie suivante ajoute la balise costCenter avec une valeur prédéfinie si certaines balises sont présentes mais que la balise costCenter n’est pas définie :

```json
{
  "if": {
    "allOf": [
      {
        "field": "tags",
        "exists": "true"
      },
      {
        "field": "tags.costCenter",
        "exists": "false"
      }
    ]
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags.costCenter",
        "value": "myDepartment"
      }
    ]
  }
}
```

### <a name="require-tag-and-value"></a>Exiger une balise et une valeur

La règle de stratégie suivante refuse la mise à jour ou la création de ressources qui ne possèdent pas la balise costCenter à la valeur prédéfinie.

```json
{
  "if": {
    "not": {
      "field": "tags.costCenter",
      "equals": "myDepartment"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="require-tags-for-a-resource-type"></a>Exiger des balises pour un type de ressource
L’exemple suivant montre comment imbriquer des opérateurs logiques afin d’exiger une balise d’application pour un certain type de ressources seulement (dans ce cas, les comptes de stockage).

```json
{
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
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>Exiger une balise
La stratégie suivante refuse les demandes dépourvues de balise contenant la clé « costCenter » (toutes les valeurs peuvent être appliquées) :

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="trigger-updates-to-existing-resources"></a>Déclencher des mises à jour sur des ressources existantes

Le script PowerShell suivant déclenche une mise à jour sur des ressources existantes pour appliquer les stratégies de balise que vous avez ajoutées.

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($_.Tags -eq $NULL) { @{}} else {$_.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Après avoir défini une règle de stratégie (comme le montrent les exemples précédents), vous devez créer la définition de stratégie et l’attribuer à une étendue. L’étendue peut être un abonnement, un groupe de ressources ou une ressource. Pour affecter des stratégies via le portail, consultez [Utiliser le portail Azure pour affecter et gérer les stratégies de ressources](resource-manager-policy-portal.md). Pour affecter des stratégies via l’API REST, PowerShell ou Azure CLI, consultez [Affecter et gérer des stratégies via un script](resource-manager-policy-create-assign.md).
* Pour une introduction aux stratégies de ressources, consultez la page [Vue d’ensemble des stratégies de ressources](resource-manager-policy.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).


