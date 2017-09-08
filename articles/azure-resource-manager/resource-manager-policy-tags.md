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
ms.date: 08/24/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 469bd8d637337e5900ea84c6bfaf88064695fb7e
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---
# <a name="apply-resource-policies-for-tags"></a>Appliquer des stratégies de ressources pour les balises

Cette rubrique fournit des règles de stratégie courantes que vous pouvez appliquer pour garantir une utilisation cohérente des balises sur les ressources.

Le fait d’appliquer une stratégie de balise à un groupe de ressources ou à un abonnement contenant des ressources n’applique pas rétroactivement la stratégie à ces ressources. Pour appliquer les stratégies à ces ressources, déclenchez une mise à jour des ressources existantes. Cet article comprend un exemple PowerShell de déclenchement d’une mise à jour.

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Vérifier que toutes les ressources d’un groupe de ressources ont une balise / valeur

Il est souvent exigé que toutes les ressources d’un groupe de ressources aient une valeur et une balise particulières. Cette exigence est souvent nécessaire pour effectuer le suivi des coûts par département. Les conditions suivantes doivent être remplies :

* La balise et la valeur requises sont ajoutées à des ressources nouvelles et mises à jour qui ne possèdent pas de balises.
* Il n’est pas possible de supprimer la balise et la valeur requises des ressources existantes.

Pour respecter ces exigences, vous devez appliquer à un groupe de ressources deux stratégies intégrées.

| ID | Description |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | Applique une balise requise et sa valeur par défaut lorsqu’elle n’est pas spécifiée par l’utilisateur. |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | Applique une balise requise et sa valeur. |

### <a name="powershell"></a>PowerShell

Le script PowerShell suivant affecte les deux définitions de stratégies intégrées à un groupe de ressources. Avant d’exécuter le script, affectez toutes les balises requises au groupe de ressources. Chaque balise du groupe de ressources est requise pour les ressources contenues dans le groupe. Pour les affecter à tous les groupes de ressources de votre abonnement, n’indiquez pas le paramètre `-Name` lorsque vous récupérez les groupes de ressources.

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

Après avoir attribué les stratégies, vous pouvez déclencher une mise à jour sur toutes les ressources existantes pour appliquer les stratégies de balise que vous avez ajoutées. Le script suivant conserve toutes les autres balises existant sur les ressources :

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
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

## <a name="next-steps"></a>Étapes suivantes
* Après avoir défini une règle de stratégie (comme le montrent les exemples précédents), vous devez créer la définition de stratégie et l’attribuer à une étendue. L’étendue peut être un abonnement, un groupe de ressources ou une ressource. Pour affecter des stratégies via le portail, consultez [Utiliser le portail Azure pour affecter et gérer les stratégies de ressources](resource-manager-policy-portal.md). Pour affecter des stratégies via l’API REST, PowerShell ou Azure CLI, consultez [Affecter et gérer des stratégies via un script](resource-manager-policy-create-assign.md).
* Pour une introduction aux stratégies de ressources, consultez la page [Vue d’ensemble des stratégies de ressources](resource-manager-policy.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).


