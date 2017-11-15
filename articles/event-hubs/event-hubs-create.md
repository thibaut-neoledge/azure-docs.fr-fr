---
title: "Création d’un concentrateur d’événements Azure | Microsoft Docs"
description: "Créer un espace de noms Azure Event Hubs et un concentrateur d’événements avec le portail Azure"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: e444c4505d4744c95e08c4ef0d33566356785c81
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Créer un espace de noms Event Hubs et un concentrateur d’événements avec le portail Azure

## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs
1. Connectez-vous au [portail Azure][Azure portal], puis cliquez sur **Créer une ressource** en haut à gauche de l’écran.
1. Cliquez sur **Internet des Objets**, puis sur **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. Dans **Créer un espace de noms**, entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. Lorsque vous avez vérifié la disponibilité de l’espace de noms, sélectionnez le niveau tarifaire (Basique ou Standard). Choisissez également un abonnement Azure, un groupe de ressources et l’emplacement où créer la ressource. 
1. Cliquez sur **Créer** pour créer l’espace de noms. Vous devrez peut-être attendre quelques minutes pour que le système approvisionne entièrement les ressources.
2. Dans la liste des espaces de noms du portail, cliquez sur l’espace de noms que vous venez de créer.
2. Cliquez sur **Stratégies d’accès partagé**, puis sur **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

3. Cliquez sur le bouton Copier pour copier la chaîne de connexion **RootManageSharedAccessKey** dans le Presse-papiers. Enregistrez cette chaîne de connexion dans un emplacement temporaire, tel que le Bloc-notes, pour une utilisation ultérieure.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Création d'un concentrateur d'événements

1. Dans la liste d’espaces de noms Event Hubs, cliquez sur le nouvel espace de noms.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. Dans le panneau de l’espace de noms, cliquez sur **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

1. Cliquez sur **Ajouter un Event Hub**en haut du panneau.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. Saisissez un nom pour votre concentrateur d’événements, puis cliquez sur **Créer**.
   
    ![](./media/event-hubs-create/create-event-hub5.png)

Votre concentrateur d’événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus les hubs d’événements, consultez ces liens :

* [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
* [Vue d’ensemble de l'API Event Hubs](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/