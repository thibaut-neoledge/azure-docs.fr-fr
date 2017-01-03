---
title: "Clé secrète de coffre de clés avec un modèle Resource Manager | Microsoft Docs"
description: "Montre comment passer une clé secrète à partir d’un coffre de clés en tant que paramètre lors du déploiement."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: fa688748be96aa614b46a218e19a0e771b908baf


---
# <a name="pass-secure-values-during-deployment"></a>Passage de valeurs sécurisées lors du déploiement

Lorsque vous devez transmettre une valeur sécurisée (par exemple, un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur en tant que clé secrète dans un [coffre de clés Azure](../key-vault/key-vault-whatis.md) et faire référence au secret dans votre fichier de paramètre. La valeur n’est jamais exposée, car vous référencez uniquement son ID de coffre de clés. Il est inutile d’entrer manuellement la valeur de la clé secrète chaque fois que vous déployez les ressources.

## <a name="deploy-a-key-vault-and-secret"></a>Déploiement d'un coffre de clés et d’une clé secrète

Pour en savoir plus sur le déploiement d'un coffre de clés et sur la clé secrète, consultez les rubriques concernant le [schéma d’un coffre de clés](resource-manager-template-keyvault.md) et le [schéma d’une clé secrète de coffre de clés](resource-manager-template-keyvault-secret.md). Lorsque vous créez le coffre de clés, définissez la propriété **enabledForTemplateDeployment** sur **true** afin qu’elle puisse être référencée à partir d’autres modèles Resource Manager. 

## <a name="reference-a-secret-with-static-id"></a>Référencement d’une clé secrète avec un ID statique

Vous référencez la clé secrète à partir d'un fichier de paramètres qui transmet les valeurs à votre modèle. Vous référencez la clé secrète en passant l'identificateur de ressource du coffre de clés et le nom de la clé secrète. Dans l’exemple suivant, la clé secrète du coffre de clés doit déjà exister, et vous définissez une valeur statique pour son ID de ressource.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "sqlsvrAdminLoginPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          },
          "secretName": "adminPassword"
        }
      },
      "sqlsvrAdminLogin": {
        "value": "exampleadmin"
      }
    }
}
```

Le paramètre qui accepte la clé secrète doit être de type **securestring**. L'exemple suivant montre les sections correspondantes d'un modèle qui déploie un serveur SQL nécessitant un mot de passe administrateur.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlsvrAdminLogin": {
            "type": "string",
            "minLength": 4
        },
        "sqlsvrAdminLoginPassword": {
            "type": "securestring"
        },
        ...
    },
    "resources": [
        {
          "name": "[variables('sqlsvrName')]",
          "type": "Microsoft.Sql/servers",
          "location": "[resourceGroup().location]",
          "apiVersion": "2014-04-01-preview",
          "properties": {
              "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
              "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
          },
          ...
        }
    ],
    "variables": {
        "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
    },
    "outputs": { }
}
```

L’utilisateur déployant un modèle qui fait référence à une clé secrète doit avoir l’autorisation **Microsoft.KeyVault/vaults/deploy/action** pour le coffre de clés. Les rôles [propriétaire](../active-directory/role-based-access-built-in-roles.md#owner) et [contributeur](../active-directory/role-based-access-built-in-roles.md#contributor) accordent cet accès. Vous pouvez également créer un [rôle personnalisé](../active-directory/role-based-access-control-custom-roles.md) qui accorde cette autorisation et ajouter l’utilisateur à ce rôle.

## <a name="reference-a-secret-with-dynamic-id"></a>Référencement d’une clé secrète avec un ID dynamique

La section précédente expliquait comment transmettre un ID de ressource statique pour la clé secrète du coffre de clés. Toutefois, dans certains scénarios, vous devez référencer une clé secrète de coffre de clés qui varie selon le déploiement actuel. Dans ce cas, vous ne pouvez pas coder en dur l’ID de ressource dans le fichier de paramètres. Malheureusement, vous ne pouvez pas générer dynamiquement l’ID de ressource dans le fichier de paramètres, car les expressions de modèle ne sont pas autorisées dans ce dernier.

Pour générer dynamiquement l’ID de ressource pour une clé secrète de coffre de clés, vous devez déplacer la ressource qui a besoin de la clé secrète dans un modèle imbriqué. Dans votre modèle principal, vous ajoutez le modèle imbriqué et passez un paramètre qui contient l’ID de ressource généré dynamiquement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des informations générales sur les coffres de clés, consultez [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md).
* Pour plus d’informations sur l’utilisation d’un coffre de clés avec une machine virtuelle, consultez [Questions de sécurité relatives à Azure Resource Manager](best-practices-resource-manager-security.md).
* Pour obtenir des exemples complets de référencement de clés secrètes, consultez [Exemples de coffres de clés](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).




<!--HONumber=Nov16_HO3-->


