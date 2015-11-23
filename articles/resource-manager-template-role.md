<properties
   pageTitle="Modèle Resource Manager pour les attributions de rôles | Microsoft Azure"
   description="Affiche le schéma Ressource Manager pour la création d'une attribution de rôle au cours du déploiement."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="tomfitz"/>

# Attributions de rôles - schéma de modèle

Attribue un utilisateur, un groupe ou un principal du service à un rôle avec une portée spécifique.

## Format de schéma

Pour créer une attribution de rôle, ajoutez le schéma suivant à la section des ressources de votre modèle.
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
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

## Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Type | Requis | Valeurs autorisées | Description |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Oui | **Microsoft.Authorization/roleAssignments** | Le type de ressource à créer. |
| apiVersion | enum | Oui | **2015-07-01** | La version de l'API à utiliser pour la création de la ressource. |  
| name | string | Oui | Identificateur global unique | Un identificateur pour la nouvelle attribution de rôle. |
| dependsOn | array | Non | Une liste séparée par des virgules de noms de ressource ou d'identificateurs de ressource uniques. | La collection de ressources dont cette attribution de rôle dépend. Si vous affectez un rôle qui s'étend sur une ressource et que la ressource est déployée dans le même modèle, incluez ce nom de ressource dans cet élément pour garantir que la ressource est tout d'abord déployée. | 
| properties | objet | Oui | (voir ci-dessous) | Un objet qui identifie la définition de rôle, le principal et l'étendue. |  

### objet propriétés

| Nom | Type | Requis | Valeurs autorisées | Description |
| ------- | ---- | ---------------- | -------- | ----------- |
| roleDefinitionId | string | Oui | **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** | L'identificateur d'une définition de rôle existante à utiliser dans l'attribution de rôle. |
| principalId | string | Oui | Identificateur global unique | L'identificateur d'un principal existant. Cette opération mappe l'ID au sein du répertoire et peut pointer vers un utilisateur, un principal du service ou un groupe de sécurité. |
| scope | string | Oui | Pour un groupe de ressources :<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**<br /><br />Puor une ressource :<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** | L'étendue avec laquelle l'attribution de rôle est appliquée. |


## Utilisation de la ressource de verrouillage

Vous ajoutez une attribution de rôle à votre modèle lorsque vous devez ajouter un utilisateur, un groupe ou un principal du service à un rôle au cours du déploiement. Les attributions de rôles sont héritées des niveaux supérieurs de la portée. Si vous avez déjà ajouté un principal à un rôle au niveau d'abonnement, vous n'avez donc pas besoin de le réaffecter au groupe de ressources ou à la ressource.

Vous pouvez générer un nouvel identificateur pour **name** avec :

    PS C:\> $name = [System.Guid]::NewGuid().toString()

Vous pouvez récupérer l'identificateur global unique pour la définition de rôle avec :

    PS C:\> $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Vous pouvez récupérer l'identificateur du principal avec l'une des commandes suivantes.

Pour un groupe nommé **Auditors** :

    PS C:\> $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Pour un utilisateur nommé **exampleperson** :

    PS C:\> $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Pour un principal du service nommé **exampleapp** :

    PS C:\> $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 
 

## Exemples

L'exemple suivant affecte un groupe à un rôle pour le groupe de ressources.

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


## Étapes suivantes

- Pour plus d'informations sur la structure du modèle, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
- Pour plus d'informations sur le contrôle d'accès en fonction du rôle, voir [Contrôle d'accès en fonction du rôle Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!---HONumber=Nov15_HO3-->