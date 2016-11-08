---
title: Modèle Resource Manager pour la liaison de ressources | Microsoft Docs
description: Affiche le schéma Resource Manager pour le déploiement de liens entre des ressources associées par le biais d'un modèle.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz

---
# Liens vers les ressources, schéma de modèle
Crée un lien entre deux ressources. Le lien est appliqué à une ressource connue en tant que ressource source. La deuxième ressource dans le lien est connue en tant que ressource cible.

## Format de schéma
Pour créer un lien, ajoutez le schéma suivant à la section des ressources de votre modèle.

    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## Valeurs
Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| --- | --- |
| type |Enum<br />Requis<br />**{namespace}/{type}/providers/links**<br /><br />Type de ressource à créer. Les valeurs {namespace} et {type} font référence au type de ressource et à l'espace de nom du fournisseur de la ressource source. |
| apiVersion |Enum<br />Requis<br />**2015-01-01**<br /><br />Version de l’API à utiliser pour créer la ressource. |
| name |String<br />Requis<br />**{resouce}/Microsoft.Resources/{linkname}****<br /> jusqu’à 64 caractères et ne peut pas contenir <, > %, &, ? ou les caractères de contrôle.<br /><br />Une valeur qui spécifie à la fois la source à verrouiller et le nom du verrou. |
| dependsOn |Array<br />Facultatif<br />Liste séparée par des virgules de noms de ressources ou d’identificateurs de ressources uniques.<br /><br />La collection de ressources dont dépend ce verrou. Si la ressource que vous liez est déployée dans le même modèle, incluez ces noms de ressources dans cet élément pour garantir que les ressources sont déployées en premier. |
| properties |Object<br />Requis<br />[properties object](#properties)<br /><br />Objet qui identifie la ressource à lier et des remarques sur le lien. |

<a id="properties" />

### objet propriétés
| Nom | Valeur |
| --- | --- |
| targetId |String<br />Requis<br />**{resource id}****<br /><br />Identificateur de la ressource cible à lier.. |

## Utilisation de la ressource de lien
Vous appliquez un lien entre deux ressources lorsque les ressources ont une dépendance qui se poursuit après le déploiement. Par exemple, une application peut se connecter à une base de données dans un groupe de ressources différent. Vous pouvez définir cette dépendance en créant un lien à partir de l'application vers la base de données. Les liens vous permettent de documenter la relation entre deux ressources. Ultérieurement, vous ou une autre personnes dans votre organisation peut interroger une ressource concernant les liens pour découvrir comment la ressource fonctionne avec d'autres ressources.

Toutes les ressources liées doivent appartenir au même abonnement. Chaque ressource peut être liée à 50 autres ressources. Si l’une des ressources liées est supprimée ou déplacée, le propriétaire de la liaison doit nettoyer la liaison restante.

Pour utiliser des liens par le biais de REST, consultez [Ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Utilisez la commande Azure PowerShell suivante pour voir tous les liens dans votre abonnement. Vous pouvez fournir d'autres paramètres pour limiter les résultats.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## Exemples
L'exemple suivant applique un verrou ReadOnly à une application web.

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
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## Modèles de démarrage rapide
Les modèles suivants de démarrage rapide déploient des ressources avec un lien.

* [Alerte de file d'attente avec application logique](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
* [Alerte Slack avec application logique](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
* [Provisionner une application API avec une passerelle existante](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
* [Provisionner une application API avec une nouvelle passerelle](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
* [Créer une application logique et une application API à l'aide d'un modèle](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
* [Application logique qui envoie un message texte en cas de déclenchement d'une alerte](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)

## Étapes suivantes
* Pour plus d'informations sur la structure du modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0406_2016-->