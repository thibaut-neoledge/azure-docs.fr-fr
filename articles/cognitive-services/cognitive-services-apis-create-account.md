---
title: "Créer un compte d’API Cognitive Services sur le Portail Azure | Microsoft Docs"
description: "Guide pratique de création d’un compte d’API Microsoft Cognitive Services sur le Portail Azure."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: garye;gibattag
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: a19f56f0b87d992e445034989bd35a827a72f367
ms.lasthandoff: 03/03/2017


---

# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Créer un compte d’API Cognitive Services sur le Portail Azure

Pour utiliser les API Microsoft Cognitive Services, vous devez d’abord créer un compte sur le Portail Azure.

1. Connectez-vous au [portail Azure](http://portal.azure.com).

2. Cliquez sur **+ NOUVEAU**.

3. Sélectionnez **Intelligence et analyse**, puis **API Cognitive Services (préversion)**.

    ![Sélectionner API Cognitive Services](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Sur la page **Créer**, renseignez les informations suivantes :

    -   **Nom du compte :** nom du compte. Nous recommandons d’utiliser un nom explicite, par exemple *AFaceAPIAccount*.

    -   **Abonnement :** sélectionnez l’un des abonnements Azure disponibles pour lequel vous disposez au moins des autorisations Collaborateur.

    -   **Type d’API :** choisissez l’API Cognitive Services que vous souhaitez utiliser. Pour plus d’informations sur les différentes API Cognitive Services disponibles, consultez le site [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

    ![Sélectionner le type d’API](media/cognitive-services-apis-create-account/list-of-apis.png)

    -   **Niveau tarifaire :** le coût de votre compte Cognitive Services dépend de l’utilisation réelle et des options que vous choisissez ci-dessous. Pour plus d’informations sur la tarification de chaque API, consultez les [pages Tarification](https://azure.microsoft.com/pricing/details/cognitive-services/).

    -   **Groupe de ressources :** un groupe de ressources désigne une collection de ressources qui partagent un cycle de vie, des autorisations et des stratégies identiques. Pour plus d’informations sur les groupes de ressources, consultez la page [Gérer les ressources Azure sur le portail](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    -   **Emplacement du groupe de ressources :** requis uniquement si l’API sélectionnée est globale (non liée à un emplacement). Toutefois, si l’API est globale et non liée à un emplacement, vous devez spécifier un emplacement pour le groupe de ressources, où résideront les métadonnées associées au compte d’API Cognitive Services. Cet emplacement n’aura aucune incidence sur la disponibilité de votre compte au moment de l’exécution. Pour plus d’informations sur les groupes de ressources, consultez la page [Gérer les ressources Azure sur le portail](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    -   **Paramètre de l’API :** par défaut, la création du compte est désactivée jusqu’à ce que votre [Administrateur de compte Azure](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles) l’active explicitement.

        La modification de ce paramètre s’applique uniquement au type et à l’emplacement d’API actuellement sélectionnés ou à l’emplacement du groupe de ressources sur le panneau de gauche.

        ![Créer un compte API Cognitive Services](media/cognitive-services-apis-create-account/create-account.png)

        > [!NOTE]
        > Si vous recevez une notification d’échec du paramètre de mise à jour, c’est parce que vous n’êtes pas connecté en tant [qu’Administrateur de compte](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator#types-of-azure-admin-accounts). L’Administrateur de compte doit suivre les étapes précédentes pour permettre la création.
        >
        > ![Message d’échec du paramètre de mise à jour](./media/cognitive-services-apis-create-account/updatefailed.png)
        
        Dans certains cas, l’Administrateur de compte n’a peut-être pas accès à l’abonnement. Dans ce cas, demandez à l’Administrateur de services fédérés de suivre les étapes du document [Ajouter ou modifier les rôles d’administrateur Azure qui gèrent l’abonnement ou le service](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).
        
        Pour connaître l’Administrateur de compte ou de service de votre abonnement, sélectionnez votre abonnement sur le [Portail Azure](https://portal.azure.com), puis sélectionnez __Propriétés__. Les informations __Administrateur de compte__ et __Administrateur de service__ s’affichent au bas du panneau Propriétés.
        
        ![Propriétés de l’abonnement](./media/cognitive-services-apis-create-account/subscription-properties.png)
    
    Microsoft peut utiliser les données que vous envoyez à Cognitive Services pour améliorer les produits et services Microsoft. Pour plus d’informations, consultez la [section Microsoft Cognitive Services](http://www.microsoft.com/Licensing/product-licensing/products.aspx) 
    dans les conditions des services en ligne.

5. Pour épingler le compte au tableau de bord du Portail Azure, cliquez sur **Épingler au tableau de bord**.

6. Cliquez sur **Créer** pour créer le compte.

Une fois le compte Cognitive Services déployé avec succès, cliquez sur la vignette dans le tableau de bord pour afficher les informations du compte.

Vous pouvez utiliser **l’URL du point de terminaison** dans la section **Vue d’ensemble** et les clés dans la section **Clés** pour effectuer des appels d’API dans vos applications.

![Afficher les informations sur le compte](media/cognitive-services-apis-create-account/display-account.png)

![Afficher les clés du compte](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur tous services Microsoft Cognitive Services disponibles, consultez la page [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

- Pour utiliser quelques exemples d’API Cognitive Services, consultez les guides de démarrage rapide suivants :
    - [Prise en main des API Text Analytics pour détecter le sentiment, les expressions clés et la langue](cognitive-services-text-analytics-quick-start.md)
    - [Guide de démarrage rapide pour l’API de recommandations Cognitive Services](cognitive-services-recommendations-quick-start.md)
