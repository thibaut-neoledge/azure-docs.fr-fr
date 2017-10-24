---
title: Erreurs de ressources Azure introuvables | Microsoft Docs
description: "Décrit comment résoudre les erreurs lorsqu’une ressource est introuvable."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Résoudre les erreurs de ressources Azure introuvables

Cet article décrit les erreurs que vous pouvez rencontrer lorsqu’une ressource est introuvable pendant le déploiement. 

## <a name="symptom"></a>Symptôme

Lorsque votre modèle inclut le nom d’une ressource qui ne peut pas être résolue, vous recevez une erreur similaire à la suivante :

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Si vous essayez d’utiliser les fonctions [reference](resource-group-template-functions-resource.md#reference) ou [listKeys](resource-group-template-functions-resource.md#listkeys) avec une ressource qui ne peut pas être résolue, vous recevez l’erreur suivante :

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Cause :

Resource Manager a besoin de récupérer les propriétés d’une ressource, mais ne peut pas identifier la ressource dans votre abonnement.

## <a name="solution"></a>Solution

### <a name="solution-1"></a>Solution 1

Si vous tentez de déployer la ressource manquante dans le modèle, vérifiez si vous devez ajouter une dépendance. Resource Manager optimise le déploiement en créant des ressources en parallèle, quand cela est possible. Si une ressource doit être déployée après une autre ressource, vous devez utiliser l’élément **dependsOn** de votre modèle pour créer une dépendance avec l’autre ressource. Par exemple, quand vous déployez une application web, le plan App Service doit être présent. Si vous n’avez pas spécifié que l’application web est dépendante du plan App Service, Resource Manager crée les deux ressources en même temps. Vous recevez une erreur indiquant que la ressource du plan App Service est introuvable, car elle n’existe pas encore quand vous tentez de définir une propriété sur l’application web. Vous pouvez éviter cette erreur en définissant la dépendance dans l’application web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Pour obtenir des conseils sur la résolution des erreurs de dépendance, consultez [Vérifier la séquence de déploiement](resource-manager-troubleshoot-tips.md#check-deployment-sequence).

### <a name="solution-2"></a>Solution 2

Lorsque la ressource existe dans un groupe de ressources différent de celui dans lequel a lieu le déploiement, utilisez la [fonction resourceId](resource-group-template-functions-resource.md#resourceid) pour obtenir le nom complet de la ressource.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>Solution 3

Recherchez une expression qui inclut la fonction [reference](resource-group-template-functions-resource.md#reference). Les valeurs que vous fournissez varient selon si la ressource fait partie des mêmes modèle, groupe de ressources et abonnement. Vérifiez que vous fournissez les valeurs de paramètres requises pour votre scénario. Si la ressource se trouve dans un autre groupe de ressources, fournissez l’ID complet de la ressource. Par exemple, pour faire référence à un compte de stockage dans un autre groupe de ressources, utilisez :

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```