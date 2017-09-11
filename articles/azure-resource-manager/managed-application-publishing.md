---
title: "Créer et publier une application managée du catalogue de services Azure | Microsoft Docs"
description: "Montre comment créer une application managée Azure destinée aux membres de votre organisation."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 39b74984ec2f89ed39753963de7fe3ff79577c9e
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publier une application managée pour une utilisation interne

Vous pouvez créer et publier des[applications managées](managed-application-overview.md) Azure pour les besoins des membres de votre organisation. Par exemple, un service informatique peut publier des applications managées destinées à contrôler la conformité par rapport aux normes de l’organisation. Ces applications managées sont disponibles via le catalogue de services, et non la Place de marché Azure.

Pour publier une application managée pour le catalogue de services, vous devez :

* créer un package .zip qui contient les trois fichiers modèles nécessaires ;
* désigner l’utilisateur, le groupe ou l’application qui doit avoir accès au groupe de ressources dans l’abonnement de l’utilisateur ;
* créer la définition de l’application managée qui pointe vers le package .zip et qui demande l’accès pour l’identité.

## <a name="create-a-managed-application-package"></a>Créer un package d’application gérée

La première étape consiste à créer les trois fichiers modèles nécessaires. Compressez les trois fichiers dans un fichier .zip, puis chargez ce package à un emplacement accessible, par exemple un compte de stockage. Vous transmettez un lien à ce fichier .zip au moment de créer la définition de l’application managée.

* **applianceMainTemplate.json** : ce fichier définit les ressources Azure approvisionnées pour l’application managée. Le modèle n’est en rien différent d’un modèle Resource Manager normal. Par exemple, pour créer un compte de stockage via une application managée, le fichier applianceMainTemplate.json contient :

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]",
            "apiVersion": "2016-01-01",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {}
  }
  ```

* **mainTemplate.json** : les utilisateurs déploient ce modèle au moment de créer l’application managée. Il définit la ressource d’application managée, qui est un type de ressource Microsoft.Solutions/appliances. Ce fichier contient tous les paramètres dont vous avez besoin pour les ressources contenues dans applianceMainTemplate.json.

  Vous définissez deux propriétés importantes dans ce modèle. La première est la propriété **applianceDefinitionId**, qui correspond à l’ID de la définition de l’application managée. Vous créerez cette définition plus loin dans cette rubrique. Au moment de définir cette valeur, vous devez indiquer quel abonnement et quels groupes de ressources utiliser pour stocker les définitions de l’application managée. Par ailleurs, vous devez décider du nom que vous souhaitez attribuer à la définition. L’ID est au format suivant :

  `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Solutions/applianceDefinitions/<definition-name>`

  La deuxième propriété, **managedResourceGroupId**, correspond à l’ID du groupe de ressources dans lequel les ressources Azure sont créées. Vous pouvez affecter une valeur à ce nom de groupe de ressources ou laisser le soin à l’utilisateur d’indiquer un nom. L’ID est au format suivant :

  `/subscriptions/<subscription-id>/resourceGroups/<resoure-group-name>`.

  L’exemple suivant présente un fichier mainTemplate.json. Il spécifie un groupe de ressources pour les ressources déployées. L’ID de définition utilise ici une définition nommée **storageApp** dans un groupe de ressources nommé **managedApplicationGroup**. Vous pouvez modifier ces valeurs de façon à utiliser d’autres noms. Indiquez votre propre ID d’abonnement dans l’ID de définition.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "variables": {
        "managedRGId": "[concat(resourceGroup().id,'-application-resources')]",
        "managedAppName": "[concat('managedStorage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/appliances",
            "name": "[variables('managedAppName')]",
            "apiVersion": "2016-09-01-preview",
            "location": "[resourceGroup().location]",
            "kind": "ServiceCatalog",
            "properties": {
                "managedResourceGroupId": "[variables('managedRGId')]",
                "applianceDefinitionId": "/subscriptions/<subscription-id>/resourceGroups/managedApplicationGroup/providers/Microsoft.Solutions/applianceDefinitions/storageApp",
                "parameters": {
                    "storageAccountNamePrefix": {
                        "value": "[parameters('storageAccountNamePrefix')]"
                    }
                }
            }
        }
    ]
  }
  ```

* **applianceCreateUiDefinition.json** : le portail Azure utilise ce fichier pour générer l’interface utilisateur pour les clients qui créent l’application managée. Vous déterminez la façon dont les utilisateurs fournissent une entrée pour chaque paramètre. Vous pouvez utiliser des options comme un sélecteur de liste déroulante, une zone de texte, une zone de mot de passe et d’autres outils de saisie. Pour en savoir plus sur la création d’un fichier de définition de l’interface utilisateur pour une application gérée, consultez [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).

  L’exemple suivant présente un fichier applianceCreateUiDefinition.json qui permet aux utilisateurs de spécifier le préfixe du nom du compte de stockage dans une zone de texte.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {
                "name": "storageAccounts",
                "type": "Microsoft.Common.TextBox",
                "label": "Storage account name prefix",
                "defaultValue": "storage",
                "toolTip": "Provide a value that is used for the prefix of your storage account. Limit to 11 characters.",
                "constraints": {
                    "required": true,
                    "regex": "^[a-z0-9A-Z]{1,11}$",
                    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-11 characters long."
                },
                "visible": true
            }
        ],
        "steps": [],
        "outputs": {
            "storageAccountNamePrefix": "[basics('storageAccounts')]"
        }
    }
  }
  ```

Une fois que tous les fichiers nécessaires sont prêts, empaquetez-les sous forme de fichier .zip. Les trois fichiers doivent se trouver au niveau racine du fichier .zip. Si vous les placez dans un dossier, vous obtenez une erreur au moment de créer la définition de l’application managée qui indique que les fichiers nécessaires ne sont pas présents. Chargez le package à un emplacement accessible à partir de là où il peut être utilisé. La suite de cet article considère que le fichier .zip existe dans un conteneur d’objets blob de stockage accessible publiquement.

## <a name="create-an-azure-active-directory-user-group-or-application"></a>Créer un groupe d’utilisateurs ou une application Azure Active Directory

La deuxième étape consiste à sélectionner un groupe d’utilisateurs ou une application pour gérer les ressources pour le compte du client. Ce groupe d’utilisateurs ou l’application dispose d’autorisations sur le groupe de ressources managé en fonction du rôle attribué. Le rôle peut être n’importe quel rôle Contrôle d’accès en fonction du rôle (RBAC) intégré, par exemple Propriétaire ou Contributeur. Vous pouvez également autoriser un utilisateur individuel à pour gérer les ressources, mais en général, cette autorisation est attribuée à un groupe d’utilisateurs. Pour créer un groupe d’utilisateurs Active Directory, consultez [Créer un groupe et ajouter des membres dans Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Vous avez besoin de l’ID d’objet du groupe d’utilisateurs à utiliser pour gérer les ressources. L’exemple suivant montre comment obtenir l’ID d’objet à partir du nom d’affichage du groupe :

```azurecli-interactive
az ad group show --group exampleGroupName
```

L’exemple de commande renvoie le résultat suivant :

```azurecli
{
    "displayName": "exampleGroupName",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```

Pour récupérer uniquement l’ID d’objet, utilisez :

```azurecli-interactive
groupid=$(az ad group show --group exampleGroupName --query objectId --output tsv)
```

## <a name="get-the-role-definition-id"></a>Obtenir l’ID de définition de rôle

Ensuite, vous avez besoin de l’ID de définition de rôle du rôle RBAC intégré auquel vous souhaitez accorder l’accès à l’utilisateur, au groupe d’utilisateurs ou à l’application. En règle générale, vous utilisez le rôle Propriétaire, Collaborateur ou Lecteur. La commande suivante montre comment obtenir l’ID de définition de rôle pour le rôle Propriétaire :


```azurecli-interactive
az role definition list --name owner
```

Cette commande renvoie le résultat suivant :

```azurecli
{
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
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

Vous avez besoin de la valeur de la propriété « name » issue de l’exemple précédent. Vous pouvez récupérer uniquement cette propriété avec :

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

## <a name="create-the-managed-application-definition"></a>Créer la définition d’application gérée

Si vous ne disposez pas déjà d’un groupe de ressources pour stocker la définition de votre application managée, créez-en un maintenant :

```azurecli-interactive
az group create --name managedApplicationGroup --location westcentralus
```

À présent, créez la ressource de définition de l’application managée.

```azurecli-interactive
az managedapp definition create \
  --name storageApp \
  --location "westcentralus" \
  --resource-group managedApplicationGroup \
  --lock-level ReadOnly \
  --display-name myteststorageapp \
  --description storageapp \
  --authorizations "$groupid:$roleid" \
  --package-file-uri <uri-path-to-zip-file>
```

Les paramètres utilisés dans cet exemple sont les suivants :

* **resource-group** : nom du groupe de ressources dans lequel la définition de l’application managée est créée.
* **lock-level**: type de verrou placé sur le groupe de ressources gérées. Il empêche le client d’effectuer des opérations indésirables sur ce groupe de ressources. Actuellement, ReadOnly est le seul niveau de verrou pris en charge. Lorsque ReadOnly est spécifié, le client peut lire uniquement les ressources présentes dans le groupe de ressources gérées.
* **authorizations** : décrit l’ID principal et l’ID de définition de rôle utilisés pour accorder des autorisations au groupe de ressources gérées. Il est spécifié sous la forme `<principalId>:<roleDefinitionId>`. Plusieurs valeurs peuvent également être spécifiées pour cette propriété. Si plusieurs valeurs sont nécessaires, elles doivent être spécifiées sous la forme `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Les valeurs sont séparées par un espace.
* **package-file-uri** : emplacement du package de l’application managée qui contient les fichiers modèles, qui peut être un objet blob de stockage Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées](managed-application-overview.md).
* Consultez les [exemples d’application gérée](https://github.com/Azure/azure-managedapp-samples/tree/master/samples) pour voir des exemples de fichiers.
* Pour plus d’informations sur l’utilisation d’une application gérée de catalogue de services, consultez l’article [Utiliser une application gérée du catalogue de services](managed-application-consumption.md).
* Pour plus d’informations sur la publication d’applications gérées sur la Place de marché, consultez l’article [Applications gérées sur la Place de marché](managed-application-author-marketplace.md).
* Pour plus d’informations sur l’utilisation d’applications gérées de la Place de marché, consultez l’article [Utilisation des applications gérées de la Place de marché Azure](managed-application-consume-marketplace.md).
* Pour en savoir plus sur la création d’un fichier de définition de l’interface utilisateur pour une application gérée, consultez [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).

