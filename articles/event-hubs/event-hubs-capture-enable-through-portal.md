---
title: "Activer Azure Event Hubs Capture à l’aide du portail | Microsoft Docs"
description: "Activez la fonctionnalité Event Hubs Capture à l’aide du portail Azure."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017


---

<a id="enable-event-hubs-capture-using-the-azure-portal" class="xliff"></a>

# Activer Event Hubs Capture à l’aide du portail Azure

Vous pouvez configurer la fonctionnalité Capture lors de la création du concentrateur d’événements, à l’aide du [portail Azure](https://portal.azure.com). Pour activer cette fonctionnalité, cliquez sur le bouton **Activé** du panneau **Créer un Event Hub** du portail. Ensuite, configurez un compte de stockage et un conteneur en cliquant sur la section **Conteneur** du panneau. Comme Event Hubs Capture utilise l’authentification de service à service avec le stockage, il est inutile de spécifier une chaîne de connexion de stockage. Le sélecteur de ressources sélectionne automatiquement l’URI de ressource pour votre compte de stockage. Si vous utilisez Azure Resource Manager, vous devez fournir explicitement cet URI en tant que chaîne.

La fenêtre temporelle par défaut est de cinq minutes. La valeur minimum est 1, la valeur maximum 15. La fenêtre **Taille** présente une plage de 10 à 500 Mo.

![][1]

<a id="adding-capture-to-an-existing-event-hub" class="xliff"></a>

## Ajout de Capture à un concentrateur d’événements existant

La fonctionnalité Capture peut être configurée sur des concentrateurs d’événements se trouvant dans des espaces de noms Event Hubs. Cette fonctionnalité n’est pas disponible sur les espaces de noms plus anciens, de type **Messagerie** ou **Mixte**. Pour activer Capture sur un concentrateur d’événements existant, ou pour modifier les paramètres de cette fonctionnalité, cliquez sur l’espace de noms afin de charger le panneau **Essentials**, puis cliquez sur le concentrateur d’événements pour lequel vous souhaitez activer ou modifier la configuration de Capture. Pour finir, cliquez sur la section **Propriétés** du panneau ouvert, comme indiqué dans la figure suivante :

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

Vous pouvez également configurer Event Hubs Capture via des modèles Azure Resource Manager. Pour en savoir plus, consultez la section relative à [l’activation de Capture à l’aide d’un modèle Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).

