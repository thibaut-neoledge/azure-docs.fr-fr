---
title: "Créer et publier une application gérée Azure | Microsoft Docs"
description: "Montre comment un éditeur de logiciels indépendant ou un partenaire crée une application gérée Azure"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/08/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 56dd68e328abd6c1dacdf7a8e051ca6b3cd07083
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="create-and-publish-an-azure-managed-application"></a>Créer et publier une application gérée Azure 

Comme décrit dans l’article [Managed Application overview](managed-application-overview.md) (Vue d’ensemble de l’application gérée), il existe deux scénarios dans l’expérience de bout en bout. D’un côté, il y a l’éditeur ou l’éditeur de logiciels indépendant désireux de créer une application gérée destinée à être utilisée par les clients. De l’autre, le client ou l’utilisateur de l’application gérée. Cet article se concentre sur le premier scénario et explique comment un éditeur de logiciels indépendant peut créer et publier une application gérée. 

Pour créer une application gérée, vous devez créer :

* un package qui contient les fichiers modèle ;
* l’utilisateur, le groupe ou l’application ayant accès au groupe de ressources de l’abonnement du client ;
* la définition de l’appliance.

Consultez les [exemples d’application gérée](https://github.com/Azure/azure-managedapp-samples/tree/master/samples) pour voir des exemples de fichiers.

## <a name="create-managed-application-package"></a>Créer un package d’application gérée

La première étape consiste à créer le package d’application gérée qui contient les principaux fichiers de modèle. L’éditeur ou l’ISV crée trois fichiers. 

* Le premier fichier est appelé **applianceMainTemplate.json**. Ce fichier modèle définit les ressources réelles configurées dans le cadre de l’application gérée. Par exemple, pour créer un compte de stockage à l’aide d’une application gérée, le fichier applianceMainTemplate.json contient ce qui suit : 

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          }
      },
      "resources": [{
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('storageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "westus",
          "sku": {
              "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {       
          }
      }],
      "outputs": {      
      }
  }
  ```

* Le second fichier que l’éditeur a besoin de créer est le fichier **mainTemplate.json**. Le fichier modèle contient uniquement la ressource de l’appliance (Microsoft.Solutions/appliances). Il contient également tous les paramètres qui sont nécessaires aux ressources du fichier applianceMainTemplate.json. 

  Il existe deux propriétés importantes nécessaires en tant qu’entrée lors de la création de l’application gérée. La propriété **managedResourceGroupId** est l’ID du groupe de ressources dans lequel les ressources définies dans le fichier applianceMainTemplate.json sont créées. L’ID est au format suivant : `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`

  La propriété **applianceDefinitionId** est l’ID de la ressource de la définition de l’application gérée. L’ID est au format suivant :`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Solutions/applianceDefinitions/{applianceDefinitionName}`

  Fournissez des paramètres pour ces deux valeurs pour que le client puisse les spécifier lors de la création d’une application gérée. Dans l’exemple ci-dessous, les deux paramètres qui correspondent à ces propriétés sont managedByResourceGroup et applianceDefinitonId.

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          },
          "applianceDefinitionId": {
              "type": "String"
          },
          "managedByResourceGroup": {
              "type": "String"
          },
          "applianceName": {
              "type": "String"
          },
      },
      "variables": {            
      },
      "resources": [{
          "type": "Microsoft.Solutions/appliances",
          "name": "[parameters('applianceName')]",
          "apiVersion": "2016-09-01-preview",
          "location": "[resourceGroup().location]",
          "kind": "ServiceCatalog",
          "properties": {
              "ManagedResourceGroupId": "[parameters('managedByResourceGroup')]",
              "applianceDefinitionId": "[parameters('applianceDefinitionId')]",
              "Parameters": {
                  "storageAccountName": {
                      "value": "[parameters('storageAccountName')]"
                  }             
              }
          }
      }]
  }
  ```

* Le troisième fichier requis dans le package est **createUiDefinition.json**. Le portail Azure utilise ce fichier pour générer l’interface utilisateur pour les clients créant l’application gérée. Vous définissez les paramètres de l’application gérée, et comment les clients peuvent obtenir la saisie pour chaque paramètre. Vous pouvez utiliser des options comme un sélecteur de liste déroulante, une zone de texte, une zone de mot de passe et d’autres outils de saisie. Pour en savoir plus sur la création d’un fichier de définition de l’interface utilisateur pour une application gérée, consultez [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).

Une fois que tous les fichiers nécessaires sont prêts, vous chargez le package vers un emplacement accessible à partir duquel il peut être utilisé.


## <a name="create-azure-ad-user-group-or-application"></a>Créer un groupe d’utilisateurs ou une application Azure AD
Créez ensuite un groupe d’utilisateurs ou une application que vous souhaitez utiliser pour gérer les ressources pour le compte du client. Ce groupe d’utilisateurs ou l’application dispose d’autorisations sur le groupe de ressources géré comme décrit par le rôle. Le rôle peut être n’importe quel rôle RBAC intégré, tel que **Propriétaire** ou **Collaborateur**. Un utilisateur individuel peut également se voir attribuer des autorisations pour gérer les ressources, mais en général, cette autorisation est attribuée pour utiliser un groupe d’utilisateurs. Pour créer un nouveau groupe d’utilisateurs Active Directory, utilisez :

```azurecli
az ad group create --display-name "name" --mail-nickname "nickname"
```

Vous pouvez également utiliser un groupe existant. Vous avez besoin de l’ID d’objet du groupe d’utilisateurs que vous venez de créer d’un groupe d’utilisateurs existant. L’exemple suivant montre comment obtenir l’ID d’objet à partir du nom d’affichage utilisé pour créer le groupe.

```azurecli
az ad group show --group "groupName"
```

Exemple :

```azurecli
az ad group show --group ravAppliancetestADgroup
```

Qui renvoie le résultat suivant :

```json 
{
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```
    
Vous avez besoin de la valeur objectId ci-dessus. 

## <a name="get-the-role-definition-id"></a>Obtenir l’ID de définition de rôle

Ensuite, vous avez besoin de l’ID de définition de rôle du rôle RBAC intégré auquel vous souhaitez accorder l’accès à l’utilisateur, au groupe d’utilisateurs ou à l’application. En règle générale, vous souhaitez utiliser le rôle « Propriétaire » ou « Collaborateur » ou « Lecteur ». La commande suivante montre comment obtenir l’ID de définition de rôle pour le rôle « Propriétaire » :

```azurecli
az role definition list --name owner
```

Qui renvoie le résultat suivant :

```json
{
    "id": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
    "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
    {
      "actions": [
    "*"
      ],
      "notActions": []
    }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

Vous avez besoin de la valeur de la propriété « name » issue de l’exemple précédent.


## <a name="create-the-managed-application-definition"></a>Créer la définition d’application gérée

La dernière étape consiste à créer la ressource de définition de l’application gérée. Une fois le package d’appliance et les autorisations créées, créez la définition de l’appliance à l’aide de la commande suivante : 

```azurecli
az managedapp definition create -n ravtestAppDef4 -l "westcentralus" 
    --resource-group ravApplianceDefRG3 --lock-level ReadOnly 
    --display-name ravtestappdef --description ravtestdescription  
    --authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
    --package-file-uri "{path to package}" --debug 
```

Les paramètres utilisés dans cet exemple sont les suivants :

- resource-group : nom du groupe de ressources où la définition de l’appliance est créée.
- lock-level : type de verrou placé sur le groupe de ressources gérées. Il empêche le client d’effectuer des opérations indésirables sur ce groupe de ressources. Actuellement, **ReadOnly** est le seul niveau de verrou pris en charge. Lorsque ReadOnly est spécifié, le client peut lire uniquement les ressources présentes dans le groupe de ressources gérées.
- authorizations : décrit l’ID principal et l’ID de définition de rôle utilisés pour accorder des autorisations au groupe de ressources gérées. Il est spécifié sous la forme `<principalId>:<roleDefinitionId>`. Plusieurs valeurs peuvent également être spécifiées pour cette propriété. Si plusieurs valeurs sont nécessaires, il doit être spécifié sous la forme `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Les valeurs sont séparées par un espace.
- package-file-uri : emplacement du package d’appliance solution qui contient les fichiers modèle, qui peuvent être un objet blob Azure Storage. 


## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](managed-application-overview.md).
* Consultez les [exemples d’application gérée](https://github.com/Azure/azure-managedapp-samples/tree/master/samples) pour voir des exemples de fichiers.
* Pour comprendre l’expérience client, consultez [Utiliser une application gérée Azure](managed-application-consumption.md).
* Pour en savoir plus sur la création d’un fichier de définition de l’interface utilisateur pour une application gérée, consultez [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).

