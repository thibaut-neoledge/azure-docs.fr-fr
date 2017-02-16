---
title: "Modèle Resource Manager pour les verrous de ressources | Microsoft Docs"
description: "Affiche le schéma Resource Manager pour le déploiement de verrous de ressources par le biais d&quot;un modèle."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: de6c0c57-e33a-4960-98e0-900901592003
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 16f87b8859a5d98c2328388ef12b13ef0e261861


---
# <a name="resource-locks-template-schema"></a>Verrou de ressources, schéma de modèle
Crée un verrou sur une ressource et ses ressources enfants.

## <a name="schema-format"></a>Format de schéma
Pour créer un verrou, ajoutez le schéma suivant à la section des ressources de votre modèle.

    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>Valeurs
Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Requis | Description |
| --- | --- | --- |
| type |Oui |Le type de ressource à créer.<br /><br />Pour les ressources :<br />**{espace de noms}/{type}/providers/locks**<br /><br/>Pour les groupes de ressources :<br />**Microsoft.Authorization/locks** |
| apiVersion |Oui |La version de l’API à utiliser pour la création de la ressource.<br /><br />Utilisez :<br />**2015-01-01**<br /><br /> |
| name |Oui |Une valeur qui spécifie la ressource à verrouiller et un nom pour le verrou. Peut comporter jusqu'à 64 caractères et ne peut pas contenir <, >, %, &, ? ou tout caractère de contrôle.<br /><br />Pour les ressources :<br />**{ressource}/Microsoft.Authorization/{nom de verrou}**<br /><br />Pour les groupes de ressources :<br />**{nom de verrou}** |
| dependsOn |Non |Liste séparée par des virgules de noms de ressources ou d’identificateurs de ressources uniques.<br /><br />La collection de ressources dont dépend ce verrou. Si la ressource que vous verrouillez est déployée dans le même modèle, incluez ce nom de ressource dans cet élément pour garantir que la ressource est tout d'abord déployée. |
| properties |Oui |Objet qui identifie le type de verrou et des remarques sur le verrou.<br /><br />Voir les [objet propriétés](#properties-object). |

### <a name="properties-object"></a>objet propriétés
| Nom | Requis | Description |
| --- | --- | --- |
| level |Oui |Le type de verrou à appliquer à l'étendue.<br /><br />**CannotDelete** : les utilisateurs peuvent modifier la ressource, mais pas la supprimer<br />**ReadOnly** : les utilisateurs peuvent lire une ressource, mais ils ne peuvent ni la supprimer ni y effectuer des actions. |
| HDInsight |Non |Description du verrou. Peut comprendre jusqu’à 512 caractères |

## <a name="how-to-use-the-lock-resource"></a>Utilisation de la ressource de verrouillage
Vous ajoutez cette ressource à votre modèle pour empêcher des actions spécifiées sur une ressource. Le verrou s'applique à tous les utilisateurs et groupes.

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès aux actions **Microsoft.Authorization/*** or **Microsoft.Authorization/locks/***. Parmi les rôles prédéfinis, seuls les rôles **Propriétaire** et **Administrateur de l'accès utilisateur** peuvent effectuer ces actions. Pour en savoir plus sur le contrôle d’accès en fonction du rôle, consultez la rubrique [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

Le verrou est appliqué à la ressource spécifiée et à toutes les ressources enfants.

Vous pouvez supprimer un verrou avec la commande PowerShell **Remove-AzureRmResourceLock** ou avec l' [opération de suppression](https://msdn.microsoft.com/library/azure/mt204562.aspx) de l'API REST.

## <a name="examples"></a>Exemples
L’exemple suivant applique un verrou cannot-delete (suppression impossible) à une application web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                  "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

L’exemple suivant applique un verrou cannot-delete (suppression impossible) au groupe de ressources.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations sur la structure du modèle, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour plus d'informations sur les verrous, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).




<!--HONumber=Nov16_HO3-->


