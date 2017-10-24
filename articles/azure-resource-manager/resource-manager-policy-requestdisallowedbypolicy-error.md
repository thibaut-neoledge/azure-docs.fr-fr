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
ms.topic: support-article
ms.date: 07/12/2017
ms.author: genli
ms.openlocfilehash: 3b11dc6afac716ef391976093839547e8fd37a91
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Erreur RequestDisallowedByPolicy avec une stratégie de ressource Azure

Cet article décrit la cause de l’erreur RequestDisallowedByPolicy. Il fournit également des solutions pour la résoudre.

## <a name="symptom"></a>Symptôme

Quand vous essayez d’effectuer une action au cours du déploiement, vous pouvez recevoir une erreur **RequestDisallowedByPolicy** empêchant l’action de s’effectuer. L’exemple ci-après illustre l’erreur :

```json
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

Dans PowerShell, fournissez cet identificateur de stratégie en tant que paramètre `Id` pour extraire des informations sur la stratégie qui a bloqué votre déploiement.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>Méthode 2 

Dans Azure CLI 2.0, indiquez le nom de la définition de stratégie : 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solution

À des fins de sécurité ou de conformité, votre service informatique peut appliquer une stratégie de ressources qui interdit la création d’adresses IP publiques, de groupes de sécurité réseau, d’itinéraires définis par l’utilisateur ou de tables de routage. Le message d’erreur présenté dans la section **Symptôme** illustre une stratégie nommée **regionPolicyDefinition**. Votre stratégie peut avoir un nom différent.
Pour résoudre ce problème, contactez votre service informatique pour passer en revue les stratégies de ressources et déterminer comment effectuer l’action demandée conformément à ces stratégies.

Pour plus d’informations, consultez les articles suivants :

- [Vue d’ensemble des stratégies de ressources](resource-manager-policy.md)
- [Afficher les affectations de stratégies via le portail](resource-manager-policy-portal.md#view-policy-assignments)