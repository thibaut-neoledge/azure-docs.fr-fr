<properties 
	pageTitle="Verrouillez des ressources avec Azure Resource Manager" 
	description="Verrouillez des ressources afin d’empêcher les utilisateurs de mettre à jour ou de supprimer certaines ressources." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Verrouiller des ressources avec Azure Resource Manager

En tant qu’administrateur, vous pouvez envisager de verrouiller une ressource ou un groupe de ressources afin d’empêcher d’autres utilisateurs de votre organisation de procéder à des actions d’écriture ou de supprimer de manière accidentelle une ressource critique.

Azure Resource Manager prend en charge la restriction des opérations sur les ressources, par le biais de verrous de gestion des ressources. Les verrous de ressource sont des stratégies qui appliquent un niveau de verrou sur une étendue particulière. Le niveau de verrouillage identifie le type d’application de la stratégie, qui comporte actuellement deux valeurs : **CanNotDelete** et **ReadOnly**. L’étendue, exprimée sous la forme d’un URI, peut être une ressource ou un groupe de ressources.

Les verrous sont différents de l’attribution des autorisations aux utilisateurs d’effectuer certaines actions. Pour en savoir plus sur la définition des autorisations pour les utilisateurs et les rôles, consultez les pages [Contrôle d’accès basé sur les rôles dans le portail Azure de Microsoft](role-based-access-control-configure.md) et [Gestion et audit d’accès aux ressources](resource-group-rbac.md).

## Scénarios courants

Il n’est pas rare de disposer d’un groupe de ressources avec certaines ressources utilisées selon un modèle alterné d’activation/de désactivation. Les ressources de machines virtuelles sont régulièrement activées pour traiter les données pendant un intervalle considéré, puis désactivées une fois cet intervalle terminé. Dans ce scénario, vous pouvez envisager d’activer l’arrêt des machines virtuelles, mais le compte de stockage ne doit aucunement être supprimé. Dans ce scénario, vous utiliserez un verrou de ressource avec le niveau de verrouillage **CanNotDelete** sur le compte de stockage.

Dans un autre scénario, il est possible que votre entreprise ne juge pas nécessaire de procéder à des mises à jour durant des intervalles considérés. Le niveau de verrouillage **ReadOnly** interrompt la création des mises à jour. Si, par exemple, vous exercez dans le domaine de la vente au détail, vous avez intérêt à interdire les mises à jour durant les périodes de fêtes. Si vous êtes une société de services financiers, vous pouvez rencontrer des contraintes de déploiement durant certaines heures d’ouverture du marché. Un verrou de ressource peut fournir une stratégie permettant de verrouiller de manière appropriée les ressources. Le verrou peut être appliqué à des ressources spécifiques, ou à l’intégralité du groupe de ressources.

## Création d’un verrou dans un modèle

L’exemple ci-dessous représente un modèle créant un verrou sur un compte de stockage. Le compte de stockage sur lequel appliquer le verrou est fourni en tant que paramètre, utilisé conjointement avec la fonction concat(). Il en résulte le nom de la ressource complété par Microsoft.Authorization, puis le nom du verrou, en l’occurrence **myLock**.

Le type fourni est spécifique au type de ressource. Pour le stockage, ce type est « Microsoft.Storage/storageaccounts/providers/locks ».

Le niveau d’étendue est défini à l’aide de la propriété **level** de la ressource. Ici, comme l’exemple s’intéresse au blocage des suppressions accidentelles, le niveau est défini sur **CannotDelete**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "lockedResource": {
                "type": "string"
            }
        },
        "resources": [
            {
                "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
                "type": "Microsoft.Storage/storageAccounts/providers/locks",
                "apiVersion": "2015-01-01",
                "properties": {
	                "level": "CannotDelete"
                }
            }
        ]
    }

## Création d’un verrou avec l’API REST

Vous pouvez verrouiller des ressources déployées à l’aide de l’[API REST pour les verrous de gestion](https://msdn.microsoft.com/library/azure/mt204563.aspx). L’API REST vous permet de créer et de supprimer des verrous, et de récupérer des informations relatives aux verrous existants.

Pour créer un verrou, exécutez :

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Le verrou peut être appliqué à un abonnement, un groupe de ressources ou à une ressource. Le nom du verrou est personnalisable. Pour la version de l’API, utilisez **2015-01-01**.

Dans la demande, incluez un objet JSON spécifiant les propriétés du verrou.

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

Pour le niveau de verrouillage, spécifiez **CanNotDelete** ou **ReadOnly**.

Pour consulter des exemples, accédez à la section [API REST pour les verrous de gestion](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Création d’un verrou à l’aide d’Azure PowerShell

Pour verrouiller avec Azure PowerShell des ressources déployées, utilisez l’élément **New-AzureResourceLock**, comme indiqué ci-dessous.

    PS C:\> New-AzureResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName ExampleGroup

PowerShell fournit d’autres commandes d’utilisation des verrous, comme **Set-AzureResourceLock** pour mettre à jour un verrou et **Remove-AzureResourceLock** pour supprimer un verrou.

## Étapes suivantes

- [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md)
- [Déplacez des ressources vers un nouveau groupe de ressources](resource-group-move-resources.md)

<!---HONumber=July15_HO4-->