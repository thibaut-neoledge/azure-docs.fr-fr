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
ms.date: 12/09/2016
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 0bf872a44b8ed7cae53d2659aa7be878902130e9
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Utiliser Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement

Lorsque vous avez besoin de passer une valeur sécurisée (par exemple, un mot de passe) comme paramètre au cours du déploiement, vous pouvez récupérer la valeur à partir d’un coffre [Azure Key Vault](../key-vault/key-vault-whatis.md). Vous récupérez la valeur en référençant le coffre de clés et la clé secrète dans votre fichier de paramètres. La valeur n’est jamais exposée, car vous référencez uniquement son ID de coffre de clés. Il est inutile d’entrer manuellement la valeur de la clé secrète chaque fois que vous déployez les ressources. Le coffre de clés peut exister dans un autre abonnement que le groupe de ressources sur lequel vous effectuez le déploiement. Lorsque vous référencez le coffre de clés, incluez l’ID d’abonnement.

Cette rubrique vous montre comment créer un coffre de clés et une clé secrète, configurer l’accès à la clé secrète pour un modèle Resource Manager et la passer en paramètre. Si vous disposez déjà d’un coffre de clés et d’une clé secrète, mais que vous devez vérifier le modèle et l’accès utilisateur, allez à la section [Activer l’accès à la clé secrète](#enable-access-to-the-secret). Si vous avez déjà le coffre de clés et la clé secrète et que vous êtes sûr qu’ils sont configurés pour l’accès utilisateur et le modèle, accédez à la section [Référencer une clé secrète avec un ID statique](#reference-a-secret-with-static-id). 

## <a name="deploy-a-key-vault-and-secret"></a>Déploiement d'un coffre de clés et d’une clé secrète

Vous pouvez déployer un coffre de clés et une clé secrète par le biais d’un modèle Resource Manager. Pour voir un exemple, consultez les pages [Modèle de coffre de clé](resource-manager-template-keyvault.md) et [Modèle de clé secrète de coffre de clé](resource-manager-template-keyvault-secret.md). Lorsque vous créez le coffre de clés, définissez la propriété **enabledForTemplateDeployment** sur **true** afin qu’elle puisse être référencée à partir d’autres modèles Resource Manager. 

Vous pouvez également créer le coffre de clés et la clé secrète par le biais du Portail Azure. 

1. Sélectionnez **Nouveau** -> **Sécurité + identité** -> **Key Vault**.

   ![créer un coffre de clés](./media/resource-manager-keyvault-parameter/new-key-vault.png)

2. Donnez des valeurs au coffre de clés. Vous pouvez ignorer les paramètres **Stratégies d’accès** et **Stratégie d’accès avancé** pour l’instant. Ces paramètres sont détaillés dans la section. Sélectionnez **Créer**.

   ![définir un coffre de clés](./media/resource-manager-keyvault-parameter/create-key-vault.png)

3. Vous disposez maintenant d’un coffre de clés. Sélectionnez-le.

4. Dans le panneau du coffre de clés, sélectionnez **Clés secrètes**.

   ![sélectionner Clés secrètes](./media/resource-manager-keyvault-parameter/select-secret.png)

5. Sélectionnez **Ajouter**.

   ![sélectionner ajouter](./media/resource-manager-keyvault-parameter/add-secret.png)

6. Sélectionnez **Manuel** pour voir les options de chargement. Donnez un nom et une valeur à la clé secrète. Sélectionnez **Créer**.

   ![fournir la clé secrète](./media/resource-manager-keyvault-parameter/provide-secret.png)

Vous avez créé votre coffre de clés et votre clé secrète.

## <a name="enable-access-to-the-secret"></a>Autoriser l’accès à la clé secrète

Que vous utilisiez un coffre de clés nouveau ou existant, vérifiez que l’utilisateur qui déploie le modèle peut accéder à la clé secrète. L’utilisateur déployant un modèle qui fait référence à une clé secrète doit avoir l’autorisation `Microsoft.KeyVault/vaults/deploy/action` pour le coffre de clés. Les rôles [propriétaire](../active-directory/role-based-access-built-in-roles.md#owner) et [contributeur](../active-directory/role-based-access-built-in-roles.md#contributor) accordent cet accès. Vous pouvez également créer un [rôle personnalisé](../active-directory/role-based-access-control-custom-roles.md) qui accorde cette autorisation et ajouter l’utilisateur à ce rôle. En outre, vous devez accorder à Resource Manager la possibilité d’accéder au coffre de clés lors du déploiement.

Vous pouvez vérifier ou effectuer ces opérations par le biais du portail.

1. Sélectionnez **Contrôle d’accès (IAM)**.

   ![sélectionner Contrôle d’accès](./media/resource-manager-keyvault-parameter/select-access-control.png)

2. Si le compte que vous souhaitez utiliser pour déployer des modèles n’est pas déjà Propriétaire ou Collaborateur (ou ajouté à un rôle personnalisé qui accorde l’autorisation `Microsoft.KeyVault/vaults/deploy/action`), sélectionnez **Ajouter**.

   ![ajouter un utilisateur](./media/resource-manager-keyvault-parameter/add-user.png)

3. Sélectionnez le rôle Collaborateur ou Propriétaire, et recherchez l’identité à affecter à ce rôle. Sélectionnez **OK** pour terminer l’ajout de l’identité au rôle.

   ![ajouter un utilisateur](./media/resource-manager-keyvault-parameter/search-user.png)

4. Pour activer l’accès à partir d’un modèle pendant le déploiement, sélectionnez **Contrôle d’accès avancé**. Sélectionnez l’option **Autoriser l’accès à Azure Resource Manager pour le déploiement de modèles**.

   ![autoriser l’accès aux modèles](./media/resource-manager-keyvault-parameter/select-template-access.png)

## <a name="reference-a-secret-with-static-id"></a>Référencement d’une clé secrète avec un ID statique

Vous référencez la clé secrète au sein d’un **fichier de paramètres (et non du modèle)** qui transmet les valeurs à votre modèle. Vous référencez la clé secrète en passant l'identificateur de ressource du coffre de clés et le nom de la clé secrète. Dans l’exemple suivant, la clé secrète du coffre de clés doit déjà exister, et vous définissez une valeur statique pour son ID de ressource.

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

Dans le modèle, le paramètre qui accepte la clé secrète doit être de type **securestring**. L'exemple suivant montre les sections correspondantes d'un modèle qui déploie un serveur SQL nécessitant un mot de passe administrateur.

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
* Pour obtenir des exemples complets de référencement de clés secrètes, consultez [Exemples de coffres de clés](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).


