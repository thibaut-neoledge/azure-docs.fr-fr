<properties 
	pageTitle="Liaison des ressources dans Azure Resource Manager | Microsoft Azure" 
	description="Liez différentes ressources dans différents groupes de ressources dans Azure Resource Manager." 
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
	ms.date="05/16/2016" 
	ms.author="tomfitz"/>

# Liaison des ressources dans Azure Resource Manager

Au cours du déploiement, vous pouvez marquer une ressource comme dépendante d’une autre ressource, mais ce cycle de vie se termine au moment du déploiement. Après le déploiement, il n’existe aucune relation identifiée entre les ressources dépendantes. Resource Manager fournit une fonctionnalité appelée « liaison des ressources », qui permet d’établir des relations persistantes entre les ressources.

Avec les liaisons vers les ressources, vous pouvez renseigner les relations qui s’étendent sur des groupes de ressources. Par exemple, il est courant qu’une base de données avec son propre cycle de vie réside dans un groupe de ressources, tandis qu’une application avec un autre cycle de vie réside dans un autre groupe de ressources. L’application se connecte à la base de données, vous devez donc marquer une liaison entre l’application et la base de données.

Toutes les ressources liées doivent appartenir au même abonnement. Chaque ressource peut être liée à 50 autres ressources. La seule façon d’interroger des ressources externes est de passer par l’API REST. Si l’une des ressources liées est supprimée ou déplacée, le propriétaire de la liaison doit nettoyer la liaison restante. Vous n’êtes **pas** averti lors de la suppression d’une ressource qui est liée à d’autres ressources.

## Modèles de liaisons

Pour définir un lien dans un modèle, vous ajoutez un type de ressource qui combine l’espace de noms du fournisseur de ressources et le type de la ressource source avec **/providers/links**. Le nom doit comprendre le nom de la ressource source. Vous fournissez l’ID de ressource de la ressource cible. L’exemple suivant établit un lien entre un site web et un compte de stockage.

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


Pour obtenir une description complète du format de modèle, consultez [Liens vers les ressources - schéma de modèle](resource-manager-template-links.md).

## Liaison avec une API REST

Pour définir une liaison entre des ressources déployées, exécutez :

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Remplacez {subscription-id} par l’identificateur de votre abonnement. Remplacez {resource-group}, {provider-namespace}, {resource-type} et {resource-name} par les valeurs identifiant la première ressource de la liaison. Remplacez {link-name} par le nom du lien à créer. Utilisez 2015-01-01 pour la version d’API.

Dans la demande, intégrez un objet définissant la deuxième ressource de la liaison :

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

L’élément « properties » contient l’identificateur de la deuxième ressource.

Vous pouvez interroger des liens dans votre abonnement avec :

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Pour consulter d’autres exemples traitant notamment de la récupération d’informations à propos des liaisons, consultez la section [Ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Étapes suivantes

- Vous pouvez également organiser vos ressources en utilisant des balises. Pour en savoir plus sur le balisage de ressources, consultez la page [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).
- Pour consulter une description de la création de modèles et définir les ressources à déployer, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0518_2016-->