---
title: "Erreur RequestDisallowedByPolicy avec une stratégie de ressource Azure | Microsoft Docs"
description: "Décrit la cause de l’erreur RequestDisallowedByPolicy."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 182a27e444c2f5db66d518a1a0c608d3e319d553
ms.contentlocale: fr-fr
ms.lasthandoff: 07/13/2017

---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Erreur RequestDisallowedByPolicy avec une stratégie de ressource Azure

Cet article décrit la cause de l’erreur RequestDisallowedByPolicy. Il fournit également des solutions pour la résoudre.

## <a name="symptom"></a>Symptôme

Lorsque vous essayez d’effectuer une action lors du déploiement, vous pouvez recevoir une erreur **RequestDisallowedByPolicy** empêchant l’action de s’effectuer. Voici un exemple de cette erreur :

```
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Résolution des problèmes

Pour extraire des informations sur la stratégie qui a bloqué votre déploiement, utilisez l’une des méthodes suivantes :

### <a name="method-1"></a>Méthode 1

Dans PowerShell, fournissez cet identificateur de stratégie en tant que paramètre **Id** pour extraire des informations sur la stratégie qui a bloqué votre déploiement.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>Méthode 2 

Dans Azure CLI 2.0, indiquez le nom de la définition de stratégie : 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solution

À des fins de sécurité ou de conformité, votre service informatique peut appliquer une stratégie de ressources qui interdit la création d’adresses IP publiques, de groupes de sécurité réseau, d’itinéraires définis par l’utilisateur ou de tables de routage. Dans l’exemple de message d’erreur décrit dans la section « Symptômes », la stratégie est nommée **regionPolicyDefinition**, mais il pourrait s’agir d’une autre stratégie.
Pour résoudre ce problème, contactez votre service informatique pour passer en revue les stratégies de ressources et déterminer comment effectuer l’action demandée conformément à ces stratégies.


Pour plus d’informations, consultez les articles suivants :

- [Vue d’ensemble des stratégies de ressources](resource-manager-policy.md)
- [Erreurs de déploiement courantes-RequestDisallowedByPolicy](resource-manager-common-deployment-errors.md#requestdisallowedbypolicy)

 



