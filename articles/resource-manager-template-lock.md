<properties
   pageTitle="Modèle Resource Manager pour les verrous de ressources | Microsoft Azure"
   description="Affiche le schéma Resource Manager pour le déploiement de verrous de ressources par le biais d'un modèle."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Verrou de ressources, schéma de modèle

Crée un nouveau verrou sur une ressource et ses ressources enfants.

## Format de schéma

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



## Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- | 
| type | Enum<br />Requis<br />**{namespace}/{type}/providers/locks** - pour des ressources ou <br />**Microsoft.Authorization/locks** - pour des groupes de ressources<br /><br />Type de ressource à créer. |
| apiVersion | Enum<br />Requis<br />**2015-01-01**<br /><br />Version de l’API à utiliser pour créer la ressource. |  
| name | String<br />Requis<br />**{resource}/Microsoft.Authorization/{lockname}** - pour des ressources ou<br />**{lockname}** - pour des groupes de ressources<br />jusqu’à 64 caractères et ne peut pas contenir <, > %, &, ? ou les caractères de contrôle.<br /><br />Une valeur qui spécifie à la fois la ressource à verrouiller et le nom du verrou. |
| dependsOn | Array<br />Facultatif<br />Une liste séparée par des virgules de noms de ressource ou d'identificateurs de ressource uniques.<br /><br />La collection de ressources dont dépend ce verrou. Si la ressource que vous verrouillez est déployée dans le même modèle, incluez ce nom de ressource dans cet élément pour garantir que la ressource est tout d'abord déployée. | 
| properties | Object<br />Requis<br />[properties object](#properties)<br /><br />Objet qui identifie le type de verrou et des remarques sur le verrou. |  

<a id="properties" />
### objet propriétés

| Nom | Valeur |
| ------- | ---- |
| level | Enum<br />Requis<br />**CannotDelete**<br /><br />Le type de verrou à appliquer à l'étendue. CanNotDelete permet la modification, mais empêche toute suppression. |
| HDInsight | String<br />Facultatif<br />jusqu’à 512 caractères<br /><br />Description du verrou. |


## Utilisation de la ressource de verrouillage

Vous ajoutez cette ressource à votre modèle pour empêcher des actions spécifiées sur une ressource. Le verrou s'applique à tous les utilisateurs et groupes. En règle générale, vous appliquez un verrou pour une durée limitée, par exemple lorsqu'un processus est en cours d'exécution et que vous souhaitez vous assurer que personne dans votre organisation ne modifie ou ne supprime par mégarde une ressource.

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès aux actions **Microsoft.Authorization/*** ou **Microsoft.Authorization/locks/***. Parmi les rôles prédéfinis, seuls les rôles **Propriétaire** et **Administrateur de l'accès utilisateur** peuvent effectuer ces actions. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

Le verrou est appliqué à la ressource spécifiée et à toutes les ressources enfants.

Vous pouvez supprimer un verrou avec la commande PowerShell **Remove-AzureRmResourceLock** ou avec l'[opération de suppression](https://msdn.microsoft.com/library/azure/mt204562.aspx) de l'API REST.

## Exemples

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

## Étapes suivantes

- Pour plus d'informations sur la structure du modèle, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour plus d'informations sur les verrous, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0406_2016-->