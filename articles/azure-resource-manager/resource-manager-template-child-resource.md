---
title: "Définir des ressources enfant dans un modèle Azure | Microsoft Docs"
description: "Explique comment définir le type et le nom d’une ressource enfant dans un modèle Azure Resource Manager"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: tomfitz
ms.openlocfilehash: 5b6ce5526f354008eb4a697deec737876f22391f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>Définir le nom et le type d’une ressource enfant dans un modèle Resource Manager
Quand vous créez un modèle, vous devez souvent inclure une ressource enfant liée à une ressource parent. Par exemple, votre modèle peut inclure un serveur SQL et une base de données. Le serveur SQL est la ressource parent, tandis que la base de données est la ressource enfant. 

Le format du type de la ressource enfant est : `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Le format du nom de la ressource enfant est : `{parent-resource-name}/{child-resource-name}`

Toutefois, vous spécifiez le type et le nom d’un modèle différemment selon qu’il est imbriqué au sein de la ressource parent, ou indépendant au niveau supérieur. Cette rubrique explique comment gérer les deux approches.

Lors de la création d’une référence complète à une ressource, l’ordre utilisé pour combiner les segments de type et de nom n’est pas une simple concaténation des deux.  Au lieu de cela, utilisez après l’espace de noms une séquence de paires *type/nom* du moins spécifique au plus spécifique :

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Par exemple :

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` est correct `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` n’est pas correct

## <a name="nested-child-resource"></a>Ressource enfant imbriquée
Pour définir une ressource enfant, la méthode la plus simple consiste à l’imbriquer dans la ressource parent. L’exemple suivant montre une base de données SQL imbriquée dans un serveur SQL.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Pour la ressource enfant, le type est défini sur `databases`, mais son type de ressource complet est `Microsoft.Sql/servers/databases`. Vous ne fournissez pas `Microsoft.Sql/servers/`, car il est déduit à partir du type de ressource parent. Le nom de la ressource enfant est défini sur `exampledatabase`, mais le nom complet inclut le nom parent. Vous ne fournissez pas `exampleserver`, car il est déduit à partir de la ressource parent.

## <a name="top-level-child-resource"></a>Ressource enfant de niveau supérieur
Vous pouvez définir la ressource enfant au niveau supérieur. Vous pouvez utiliser cette approche si la ressource parent n’est pas déployée dans le même modèle, ou si voulez utiliser `copy` pour créer plusieurs ressources enfant. Dans le cadre de cette approche, vous devez fournir le type de ressource complet et inclure le nom de la ressource parent dans le nom de la ressource enfant.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

La base de données est une ressource enfant pour le serveur, même si elle est définie au même niveau dans le modèle.

## <a name="next-steps"></a>Étapes suivantes
* Pour des recommandations sur la création de modèles, consultez [Bonnes pratiques relatives à la création de modèles Azure Resource Manager](resource-manager-template-best-practices.md).
* Pour obtenir un exemple de création de plusieurs ressources enfant, consultez [Déployer plusieurs instances de ressources dans des modèles Azure Resource Manager](resource-group-create-multiple.md).
