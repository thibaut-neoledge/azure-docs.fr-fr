<properties
	pageTitle="Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure"
	description="Découvrez comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure." 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="sdanie"/>

# Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure

Gestion des API prend en charge le déploiement sur plusieurs régions, ce qui permet aux éditeurs d’API de ne distribuer qu’un seul service Gestion des API sur le nombre de régions Azure. Ceci permet de réduire la latence de la demande telle qu’elle est perçue par les utilisateurs distribués de l’API. La disponibilité du service est également améliorée si une région est mise hors connexion.

Lors de la création initiale du service Gestion des API, il ne contient qu’une seule [unité][] et se trouve dans une seule région Azure, désignée comme région principale. D’autres régions peuvent être facilement ajoutées via le portail Azure Classic. Le serveur de passerelle Gestion des API est déployé dans chaque région et le trafic d’appel est acheminé vers la passerelle la plus proche. Si une région est déconnectée, le trafic est automatiquement redirigé vers la passerelle suivante la plus proche.

> [AZURE.IMPORTANT] Le déploiement multi-régions est disponible uniquement dans le niveau **[Premium][]**.

## <a name="add-region"> </a>Déploiement d’une instance de service Gestion des API sur une nouvelle région

Pour commencer, cliquez sur **Gérer** dans le portail Azure Classic de votre service Gestion des API. Vous accédez au portail des éditeurs Gestion des API.

![Portail des éditeurs][api-management-management-console]

>Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

Accédez à l’onglet **Mettre à l’échelle** du portail Azure Classic pour votre instance de service Gestion des API.

![Onglet Mettre à l’échelle][api-management-scale-service]

Pour effectuer le déploiement dans une nouvelle région, cliquez sur la liste déroulante sous la région principale et choisissez une région dans la liste.

![Ajouter une région][api-management-add-region]

Une fois la région sélectionnée, choisissez le nombre d’unités de cette nouvelle région dans la liste déroulante des unités.

![Spécifier les unités][api-management-select-units]

Une fois les régions et unités configurées, cliquez sur **Enregistrer**.

## <a name="remove-region"> </a>Suppression d’une instance de service Gestion des API dans une région

Pour supprimer une instance de service Gestion des API d’une région, accédez à l’onglet **Mettre à l’échelle** du portail Azure Classic pour votre instance de service Gestion des API.

![Onglet Mettre à l’échelle][api-management-scale-service]

Cliquez sur le signe **X** à droite de la région souhaitée pour supprimer cette dernière.

![Supprimer la région][api-management-remove-region]

Une fois que les régions de votre choix supprimées, cliquez sur **Enregistrer**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Création d'une instance de service Gestion des API]: api-management-get-started.md#create-service-instance
[Prise en main de Gestion des API Azure]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unité]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

<!---HONumber=AcomDC_0810_2016-->