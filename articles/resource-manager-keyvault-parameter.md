<properties
   pageTitle="Clé secrète de coffre de clés avec un modèle Resource Manager | Microsoft Azure"
   description="Montre comment passer une clé secrète à partir d’un coffre de clés en tant que paramètre lors du déploiement."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# Passage de valeurs sécurisées lors du déploiement

Lorsque vous devez passer une valeur sécurisée (par exemple, un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur en tant que clé secrète dans un [coffre de clés Azure](./key-vault/key-vault-whatis.md) et faire référence à la valeur dans d'autres modèles Resource Manager. Vous incluez uniquement une référence à la clé secrète dans votre modèle afin de ne jamais l’exposer, et vous n'avez pas besoin d'entrer manuellement la valeur de la clé secrète chaque fois que vous déployez les ressources. Vous spécifiez quels utilisateurs ou principaux du service peuvent accéder à la clé secrète.

## Déploiement d'un coffre de clés et d’une clé secrète

Pour créer un coffre de clés qui peut être référencé à partir d'autres modèles Resource Manager, vous devez définir la propriété **enabledForTemplateDeployment** sur **true**, et vous devez accorder l'accès à l'utilisateur ou au principal du service qui exécutera le déploiement faisant référence à la clé secrète.

Pour en savoir plus sur le déploiement d'un coffre de clés et la clé secrète, consultez [Schéma d’un coffre de clés](resource-manager-template-keyvault.md) et [Schéma d’une clé secrète de coffre de clés](resource-manager-template-keyvault-secret.md).

## Référencement d’une clé secrète avec un ID statique

Vous référencez la clé secrète à partir d'un fichier de paramètres qui transmet les valeurs à votre modèle. Vous référencez la clé secrète en passant l'identificateur de ressource du coffre de clés et le nom de la clé secrète. Dans cet exemple, la clé secrète du coffre de clés doit déjà exister, et vous utilisez une valeur statique pour l’ID de ressource de ce dernier.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Un fichier de paramètres complet peut ressembler à ceci :

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

Le paramètre qui accepte la clé secrète doit être de type**securestring**. L'exemple suivant montre les sections correspondantes d'un modèle qui déploie un serveur SQL nécessitant un mot de passe administrateur.

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
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## Référencement d’une clé secrète avec un ID dynamique

La section précédente expliquait comment passer un ID de ressource statique pour la clé secrète du coffre de clés. Toutefois, dans certains scénarios, vous devez référencer une clé secrète de coffre de clés qui varie selon le déploiement actuel. Dans ce cas, vous ne pouvez pas coder en dur l’ID de ressource dans le fichier de paramètres. Malheureusement, vous ne pouvez pas générer dynamiquement l’ID de ressource dans le fichier de paramètres, car les expressions de modèle ne sont pas autorisées dans ce dernier.

Pour générer dynamiquement l’ID de ressource pour une clé secrète de coffre de clés, vous devez déplacer la ressource qui a besoin de la clé secrète dans un modèle imbriqué. Dans votre modèle principal, vous ajoutez le modèle imbriqué et passez un paramètre qui contient l’ID de ressource généré dynamiquement.

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
        }
      ],
      "outputs": {}
    }


## Étapes suivantes

- Pour obtenir des informations générales sur les coffres de clés, consultez [Prise en main du coffre de clés Azure](./key-vault/key-vault-get-started.md).
- Pour plus d’informations sur l’utilisation d’un coffre de clés avec une machine virtuelle, consultez [Questions de sécurité relatives à Azure Resource Manager](best-practices-resource-manager-security.md).
- Pour obtenir des exemples complets de référencement de clés secrètes, consultez [Exemples de coffres de clés](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

<!---HONumber=AcomDC_0629_2016-->