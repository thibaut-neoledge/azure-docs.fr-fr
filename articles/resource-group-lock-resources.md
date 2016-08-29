<properties 
	pageTitle="Verrouillage de ressources avec Resource Manager | azure.microsoft.com/ Azure" 
	description="Empêchez les utilisateurs de mettre à jour ou de supprimer certaines ressources en appliquant une restriction à tous les utilisateurs et rôles." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="tomfitz"/>

# Verrouiller des ressources avec Azure Resource Manager

En tant qu’administrateur, vous pouvez avoir besoin de verrouiller un abonnement, une ressource ou un groupe de ressources afin d’empêcher d’autres utilisateurs de votre organisation de supprimer ou modifier de manière accidentelle des ressources critiques. Vous pouvez définir le niveau de verrouillage sur **CanNotDelete** ou **ReadOnly**.

- **CanNotDelete** signifie que les utilisateurs autorisés peuvent toujours lire et modifier une ressource, mais qu’ils ne peuvent pas la supprimer.
- **ReadOnly** signifie que les utilisateurs autorisés peuvent lire une ressource, mais qu’ils ne peuvent ni la supprimer ni y effectuer des actions. L’autorisation sur la ressource est limitée au rôle **Lecteur**.

L’application de **ReadOnly** peut produire des résultats inattendus, car certaines opérations qui ressemblent à des opérations de lecture nécessitent en fait des actions supplémentaires. Par exemple, le placement d’un verrou **ReadOnly** sur un compte de stockage empêche tous les utilisateurs de répertorier les clés. L’opération de listage de clés est gérée via une demande POST, car les clés retournées sont disponibles pour les opérations d’écriture. Autre exemple : le placement d’un verrou **ReadOnly** sur une ressource App Service empêche l’Explorateur de serveurs Visual Studio d’afficher les fichiers de la ressource, car cette interaction requiert un accès en écriture.

Contrairement au contrôle d'accès basé sur les rôles, vous utilisez des verrous de gestion pour appliquer une restriction à tous les utilisateurs et rôles. Pour en savoir plus sur la définition des autorisations pour les utilisateurs et les rôles, consultez [Contrôle d’accès basé sur un rôle Azure](./active-directory/role-based-access-control-configure.md).

Lorsque vous appliquez un verrou à une étendue parente, toutes les ressources enfants héritent du même verrou. Même les ressources que vous ajoutez par la suite héritent du verrou du parent. Le verrou le plus restrictif de l’héritage est prioritaire.

## Personnes autorisées à créer ou supprimer des verrous dans votre organisation

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès aux actions **azure.microsoft.com/.Authorization/*** ou **azure.microsoft.com/.Authorization/locks/***. Parmi les rôles prédéfinis, seuls les rôles **Propriétaire** et **Administrateur de l'accès utilisateur** peuvent effectuer ces actions.

## Création d’un verrou via le portail

[AZURE.INCLUDE [resource-manager-lock-resources](../includes/resource-manager-lock-resources.md)]

## Création d’un verrou dans un modèle

L’exemple suivant représente un modèle créant un verrou sur un compte de stockage. Le compte de stockage auquel est appliqué le verrou est fourni en tant que paramètre. Le nom du verrou résulte de la concaténation du nom de la ressource, de **/azure.microsoft.com/.Authorization/** et du nom du verrou, en l’occurrence **myLock**.

Le type fourni est spécifique au type de ressource. Pour le stockage, définissez le type suivant : « azure.microsoft.com/.Storage/storageaccounts/providers/locks ».

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
          "name": "[concat(parameters('lockedResource'), '/azure.microsoft.com/.Authorization/myLock')]",
          "type": "azure.microsoft.com/.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## Création d’un verrou avec l’API REST

Vous pouvez verrouiller des ressources déployées à l’aide de l’[API REST pour les verrous de gestion](https://msdn.azure.microsoft.com/.com/library/azure/mt204563.aspx). L’API REST vous permet de créer et de supprimer des verrous, et de récupérer des informations relatives aux verrous existants.

Pour créer un verrou, exécutez :

    PUT https://management.azure.com/{scope}/providers/azure.microsoft.com/.Authorization/locks/{lock-name}?api-version={api-version}

Le verrou peut être appliqué à un abonnement, à un groupe de ressources ou à une ressource. Le nom du verrou est personnalisable. Pour la version de l’API, utilisez **2015-01-01**.

Dans la demande, incluez un objet JSON spécifiant les propriétés du verrou.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

Pour obtenir des exemples, consultez [API REST pour les verrous de gestion](https://msdn.azure.microsoft.com/.com/library/azure/mt204563.aspx).

## Création d’un verrou à l’aide d’Azure PowerShell

Vous pouvez verrouiller des ressources déployées avec Azure PowerShell en utilisant **New-AzureRmResourceLock**, comme indiqué ci-dessous.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType azure.microsoft.com/.Web/sites -ResourceGroupName exampleresourcegroup

Azure PowerShell fournit d'autres commandes d'utilisation des verrous, comme **Set-AzureRmResourceLock** pour mettre à jour un verrou et **Remove-AzureRmResourceLock** pour supprimer un verrou.

## Étapes suivantes

- Pour plus d’informations sur l’utilisation de verrous sur des ressources, consultez [Lock Down Your Azure Resources](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Pour en savoir plus sur l’organisation logique de vos ressources, consultez [Organisation des ressources à l’aide de balises](resource-group-using-tags.md)
- Pour changer le groupe de ressources où se trouve une ressource, consultez [Déplacer des ressources vers un nouveau groupe de ressources](resource-group-move-resources.md)
- Vous pouvez appliquer des restrictions et des conventions sur votre abonnement avec des stratégies personnalisées. Pour plus d'informations, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l'accès](resource-manager-policy.md).

<!---HONumber=AcomDC_0817_2016-->