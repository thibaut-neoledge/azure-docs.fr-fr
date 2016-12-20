---
title: "Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure"
description: "Découvrez comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 47389ad6-f865-4706-833f-846115e22e4d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: d99e2d885d56e3752a3b1caf51e52c801acaab52
ms.openlocfilehash: 98201867fd8b1b5f074aa6135e04b04faf384224


---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure
Gestion des API prend en charge le déploiement sur plusieurs régions, ce qui permet aux éditeurs d’API de ne distribuer qu’un seul service Gestion des API sur le nombre de régions Azure. Ceci permet de réduire la latence de la demande telle qu’elle est perçue par les utilisateurs distribués de l’API. La disponibilité du service est également améliorée si une région est mise hors connexion. 

Lors de la création initiale du service Gestion des API, il ne contient qu’une seule [unitéé][unitéé] et se trouve dans une seule région Azure, désignée comme région primaire. D’autres régions peuvent être facilement ajoutées via le portail Azure. Un serveur de passerelle Gestion des API est déployé dans chaque région et le trafic d’appel est acheminé vers la passerelle la plus proche. Si une région est déconnectée, le trafic est automatiquement redirigé vers la passerelle suivante la plus proche. 

> [!IMPORTANT]
> Le déploiement multirégion est disponible uniquement dans le niveau **[Premium][Premium]**.
> 
> 

## <a name="add-region"> </a>Déploiement d’une instance de service Gestion des API sur une nouvelle région
> [!NOTE]
> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d’une instance du service API Management][Création d’une instance du service API Management] dans le didacticiel [Prise en main de Gestion des API Azure][Prise en main de Gestion des API Azure].
> 
> 

Dans le portail Azure, accédez à la page **Scale and pricing (Échelle et tarification)** de votre instance du service Gestion des API. 

![Onglet Mettre à l’échelle][api-management-scale-service]

Pour procéder au déploiement vers une nouvelle région, cliquez sur **+ Ajouter une région** dans la barre d’outils.

![Ajouter une région][api-management-add-region]

Sélectionnez l’emplacement dans la liste déroulante et définissez le nombre d’unités à l’aide du curseur.

![Spécifier les unités][api-management-select-location-units]

Cliquez sur **Ajouter** pour placer votre sélection dans la table des emplacements. 

Répétez cette procédure jusqu’à ce que vous ayez configuré tous les emplacements, puis cliquez sur **Enregistrer** dans la barre d’outils pour démarrer le déploiement.

## <a name="remove-region"> </a>Suppression d’une instance de service Gestion des API dans un emplacement
Dans le portail Azure, accédez à la page **Scale and pricing (Échelle et tarification)** de votre instance du service Gestion des API. 

![Onglet Mettre à l’échelle][api-management-scale-service]

Pour l’emplacement que vous souhaitez supprimer, ouvrez le menu contextuel à l’aide du bouton **...** situé à l’extrême droite du tableau. Sélectionnez l’option **Supprimer**.

![Supprimer la région][api-management-remove-region]

Confirmez la suppression, puis cliquez sur **Enregistrer** pour appliquer les modifications.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Création d’une instance du service API Management]: api-management-get-started.md#create-service-instance
[Prise en main de Gestion des API Azure]: api-management-get-started.md

[Déploiement d’une instance de service Gestion des API sur une nouvelle région]: #add-region
[Suppression d’une instance de service Gestion des API dans une région]: #remove-region

[unitéé]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/




<!--HONumber=Nov16_HO3-->


