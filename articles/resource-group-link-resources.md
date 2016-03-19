<properties 
	pageTitle="Liaison des ressources dans Azure Resource Manager" 
	description="Liez différentes ressources dans différents groupes de ressources dans Azure Resource Manager." 
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
	ms.date="01/26/2016" 
	ms.author="tomfitz"/>

# Liaison des ressources dans Azure Resource Manager

Après le déploiement, vous pouvez avoir besoin d’interroger les relations ou les liens entre les ressources. Les dépendances informent le processus de déploiement, mais ce cycle se termine lors du déploiement. Une fois le déploiement terminé, il n’existe aucune relation identifiée entre les ressources dépendantes.

Au lieu de cela, Azure Resource Manager fournit une nouvelle fonctionnalité appelée « liaison des ressources », qui permet d’établir des relations entre les ressources et d’interroger les relations existantes. Vous pouvez déterminer quelles ressources sont liées à une ressource ou quelles ressources sont liées à partir d’une ressource.

L’étendue d’une liaison de ressource peut correspondre à un abonnement, un groupe de ressources ou une ressource spécifique. Cela signifie que les liaisons vers les ressources peuvent renseigner les relations qui s’étendent sur des groupes de ressources. Lorsque vous commencez à décomposer votre solution en plusieurs groupes de ressources et plusieurs modèles, le fait d’avoir un mécanisme pour identifier ces liaisons de ressources s’avère utile. Par exemple, il est courant qu’une base de données avec son propre cycle de vie réside dans un groupe de ressources, tandis qu’une application avec un autre cycle de vie réside dans un autre groupe de ressources. L’application se connecte à la base de données, il y a donc une liaison entre les ressources dans différents groupes de ressources.

Toutes les ressources liées doivent appartenir au même abonnement. Chaque ressource peut être liée à 50 autres ressources. Si l’une des ressources liées est supprimée ou déplacée, le propriétaire de la liaison doit nettoyer la liaison restante.

## Modèles de liaisons

Pour définir une liaison entre les ressources d’un modèle, consultez [Liaisons de ressources - schéma de modèle](resource-manager-template-links.md).

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

Pour consulter d’autres exemples traitant notamment de la récupération d’informations à propos des liaisons, consultez la section [Ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Étapes suivantes

- Vous pouvez également organiser vos ressources en utilisant des balises. Pour en savoir plus sur le balisage de ressources, consultez la page [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).
- Pour consulter une description de la création de modèles et définir les ressources à déployer, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0128_2016-->