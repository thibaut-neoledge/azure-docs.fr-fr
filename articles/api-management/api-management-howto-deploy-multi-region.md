---
title: "Déployer des services Gestion des API Azure sur plusieurs régions Azure | Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: 1c39fee739c2f5fd4b928e1e76e1ea57f072b5f8

---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure
Gestion des API prend en charge le déploiement sur plusieurs régions, ce qui permet aux éditeurs d’API de ne distribuer qu’un seul service Gestion des API sur le nombre de régions Azure. Ceci permet de réduire la latence de la demande telle qu’elle est perçue par les utilisateurs distribués de l’API. La disponibilité du service est également améliorée si une région est mise hors connexion. 

Lors de la création initiale du service Gestion des API, il ne contient qu’une seule [unité][unit] et se trouve dans une seule région Azure, désignée comme région primaire. D’autres régions peuvent être facilement ajoutées via le portail Azure. Un serveur de passerelle Gestion des API est déployé dans chaque région et le trafic d’appel est acheminé vers la passerelle la plus proche. Si une région est déconnectée, le trafic est automatiquement redirigé vers la passerelle suivante la plus proche. 

> [!IMPORTANT]
> Le déploiement multi-régions est disponible uniquement dans le niveau **[Premium][Premium]**.
> 
> 

## <a name="add-region"> </a>Déploiement d’une instance de service Gestion des API sur une nouvelle région
> [!NOTE]
> Si vous n’avez pas encore créé une instance de service Gestion des API, consultez la page de [création d’une instance de service Gestion des API][Create an API Management service instance] dans le didacticiel de [prise en main de Gestion des API Azure][Get started with Azure API Management].
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

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Get started with Azure API Management]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/




<!--HONumber=Jan17_HO5-->


