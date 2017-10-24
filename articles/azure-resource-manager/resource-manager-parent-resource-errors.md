---
title: Erreurs de ressources parentes Azure | Microsoft Docs
description: "Décrit comment résoudre les erreurs avec une ressource parente."
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
ms.openlocfilehash: e59147c4ac18f730f27b9d4aa9c008f219881065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-parent-resources"></a>Résoudre les erreurs avec des ressources parentes

Cet article décrit les erreurs que vous pouvez rencontrer au cours du déploiement d’une ressource dépendant d’une ressource parente.

## <a name="symptom"></a>Symptôme

Au cours du déploiement d’une ressource qui est l’enfant d’une autre ressource, vous pouvez rencontrer l’erreur suivante :

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Cause :

Lorsqu’une ressource est l’enfant d’une autre ressource, la ressource parente doit exister avant de pouvoir créer la ressource enfant. Le nom de la ressource enfant inclut le nom de la ressource parent. Par exemple, une base de données SQL peut être définie de la manière suivante :

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Toutefois, si vous ne spécifiez pas de dépendance sur le serveur, le déploiement de la base de données peut démarrer avant que le serveur n’ait été déployé.

## <a name="solution"></a>Solution

Pour corriger cette erreur, incluez une dépendance.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Pour plus d’informations, consultez [Définir l’ordre de déploiement des ressources dans les modèles Azure Resource Manager](resource-group-define-dependencies.md).