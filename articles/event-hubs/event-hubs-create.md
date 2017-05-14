---
title: "Création d’un concentrateur d’événements Azure | Microsoft Docs"
description: "Créer un espace de noms Azure Event Hubs et un concentrateur d’événements avec le portail Azure"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: jotaub
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 2ef14fccda5c38ebdbf9d64b4766b45503755af0
ms.contentlocale: fr-fr
ms.lasthandoff: 05/04/2017

---

# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Créer un espace de noms Event Hubs et un concentrateur d’événements avec le portail Azure

## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs
1. Connectez-vous au [portail Azure][Azure portal], puis cliquez sur **Nouveau** en haut à gauche de l’écran.
1. Cliquez sur **Internet des Objets**, puis sur **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. Dans le panneau **Créer un espace de noms** , entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. Lorsque vous avez vérifié la disponibilité de l’espace de noms, sélectionnez le niveau tarifaire (Basique ou Standard). Choisissez également un abonnement Azure, un groupe de ressources et l’emplacement où créer la ressource. 
1. Cliquez sur **Créer** pour créer l’espace de noms.
1. Dans la liste d’espaces de noms Event Hubs, cliquez sur l’espace de noms créé.      
   
    ![](./media/event-hubs-create/create-event-hub2.png)
1. Dans le panneau de l’espace de noms, cliquez sur **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

## <a name="create-an-event-hub"></a>Création d'un concentrateur d'événements

1. Cliquez sur **Ajouter un Event Hub**en haut du panneau.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. Saisissez un nom pour votre concentrateur d’événements, puis cliquez sur **Créer**.
   
    ![](./media/event-hubs-create/create-event-hub5.png)
1. Dans la liste de concentrateurs d’événements, cliquez sur le nom du concentrateur d’événements nouvellement créé. 
    
    ![](./media/event-hubs-create/create-event-hub6.png)
1. Revenez au panneau de l’espace de noms (pas le panneau du concentrateur d’événements spécifique), cliquez sur **Stratégies d’accès partagé**, puis sur **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)
1. Cliquez sur le bouton Copier pour copier la chaîne de connexion **RootManageSharedAccessKey** dans le Presse-papiers. Enregistrez cette chaîne de connexion pour l’utiliser plus tard dans ce didacticiel.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

Votre concentrateur d’événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus les hubs d’événements, consultez ces liens :

* [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
* [Vue d’ensemble de l'API Event Hubs](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/
