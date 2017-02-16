---
title: "Modèle Resource Manager pour les attributions de rôles | Microsoft Docs"
description: "Affiche le schéma Resource Manager pour le déploiement d&quot;une attribution de rôle par le biais d&quot;un modèle."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: d9541169-00d1-4497-b57f-68896d51849b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 69767db0399d02b82495da475cb8c5bf25710c87


---
# <a name="role-assignments-template-schema"></a>Attributions de rôles, schéma de modèle
Attribue un utilisateur, un groupe ou un principal du service à un rôle avec une portée spécifique.

## <a name="resource-format"></a>Format des ressources
Pour créer une attribution de rôle, ajoutez le schéma suivant à la section des ressources de votre modèle.

    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Valeurs
Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Requis | Description |
| --- | --- | --- |
| type |Oui |Le type de ressource à créer.<br /><br /> Pour un groupe de ressources :<br />**Microsoft.Authorization/roleAssignments**<br /><br />Pour une ressource :<br />**{espace_noms-fournisseur}/{type_ressource}/providers/roleAssignments** |
| apiVersion |Oui |La version de l’API à utiliser pour la création de la ressource.<br /><br /> Utilisez **2015-07-01**. |
| name |Oui |Un identificateur global unique pour la nouvelle affectation de rôle. |
| dependsOn |Non |Tableau de noms de ressources ou d’identificateurs de ressources uniques, avec une virgule comme séparateur.<br /><br />La collection de ressources dont cette attribution de rôle dépend. Si vous affectez un rôle qui s'étend sur une ressource et que la ressource est déployée dans le même modèle, incluez ce nom de ressource dans cet élément pour garantir que la ressource est tout d'abord déployée. |
| properties |Oui |Objet qui identifie la définition, le principal et l’étendue du rôle. |

### <a name="properties-object"></a>objet propriétés
| Nom | Requis | Description |
| --- | --- | --- |
| roleDefinitionId |Oui |L'identificateur d'une définition de rôle existante à utiliser dans l'attribution de rôle.<br /><br /> Utilisez le format suivant :<br /> **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId |Oui |Identificateur global unique pour un principal existant. Cette valeur est mappée à l’ID au sein de l’annuaire et peut pointer vers un utilisateur, un principal du service ou un groupe de sécurité. |
| scope |Non |L'étendue avec laquelle l'attribution de rôle est appliquée.<br /><br />Pour les groupes de ressources, utilisez :<br />**/subscriptions/{ID_abonnement}/resourceGroups/{nom_groupe_ressources}**  <br /><br />Pour les ressources, utilisez :<br />**/subscriptions/{ID_abonnement}/resourceGroups/{nom_groupe_ressources}/providers/{espace_noms-fournisseur}/{type_ressource}/{nom_ressource}** |

## <a name="how-to-use-the-role-assignment-resource"></a>Utilisation de la ressource d'attribution de rôle
Vous ajoutez une attribution de rôle à votre modèle lorsque vous devez ajouter un utilisateur, un groupe ou un principal du service à un rôle au cours du déploiement. Les affectations de rôles sont héritées des niveaux plus élevés de l’étendue. Ainsi, si vous avez déjà ajouté un principal à un rôle au niveau de l’abonnement, vous n’avez pas besoin de le réaffecter au groupe de ressources ou à la ressource.

Vous devez fournir un grand nombre de valeurs d’identificateur lors de l’utilisation des affectations de rôle. Vous pouvez récupérer les valeurs via PowerShell ou Azure CLI.

### <a name="powershell"></a>PowerShell
Le nom de l’affectation de rôle nécessite un identificateur global unique. Vous pouvez générer un nouvel identificateur pour **name** avec :

    $name = [System.Guid]::NewGuid().toString()

Vous pouvez récupérer l’identificateur pour la définition de rôle avec :

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Vous pouvez récupérer l'identificateur du principal avec l'une des commandes suivantes.

Pour un groupe nommé **Auditors**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Pour un utilisateur nommé **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Pour un principal du service nommé **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Interface de ligne de commande Azure
Vous pouvez récupérer l’identificateur pour la définition de rôle avec :

    azure role show Reader --json | jq .[].Id -r

Vous pouvez récupérer l'identificateur du principal avec l'une des commandes suivantes.

Pour un groupe nommé **Auditors**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Pour un utilisateur nommé **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Pour un principal du service nommé **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Exemples
Le modèle suivant reçoit un identificateur pour un rôle et un identificateur pour un utilisateur, un groupe ou un principal du service. Il affecte le rôle au niveau du groupe de ressources.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



Le modèle suivant crée un compte de stockage et affecte le rôle de lecteur au compte de stockage. Les identificateurs de deux groupes et le rôle de lecteur ont été inclus dans le modèle pour simplifier le déploiement. Ces valeurs peuvent être récupérées au moment du déploiement par le biais d’un script et passés comme paramètres en entrée.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Modèles de démarrage rapide
Les modèles suivants montrent comment utiliser la ressource d'attribution de rôle :

* [Attribuer un rôle intégré au groupe de ressources](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
* [Attribuer un rôle intégré à une machine virtuelle existante](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
* [Attribuer un rôle intégré à plusieurs machines virtuelles existantes](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations sur la structure du modèle, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md).




<!--HONumber=Nov16_HO3-->


