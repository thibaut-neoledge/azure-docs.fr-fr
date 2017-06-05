---
title: Prise en main des connexions hybrides Azure Relay dans Node | Microsoft Docs
description: "Comment écrire une application de console de nœud pour les connexions hybrides"
services: service-bus-relay
documentationcenter: node
author: jtaubensee
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 05/22/2017
ms.author: jotaub;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: a97082b38d146964d77cd9029ce74baa781c6c27
ms.contentlocale: fr-fr
ms.lasthandoff: 05/23/2017


---
# <a name="get-started-with-relay-hybrid-connections"></a>Prise en main des connexions hybrides Relay

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Les opérations que nous allons effectuer

Étant donné que les connexions hybrides nécessitent un client et un serveur, nous allons créer deux applications de console dans ce didacticiel. Voici la procédure à suivre :

1. Créer un espace de noms Relay à l’aide du portail Azure.
2. Créer une connexion hybride, à l’aide du portail Azure.
3. Écrire une application de console de serveur pour recevoir des messages.
4. Écrire une application de console de client pour envoyer des messages.

## <a name="prerequisites"></a>Conditions préalables

1. [Node.js](https://nodejs.org/en/) (Cet exemple utilise Node 7.0).
2. Un abonnement Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Créer un espace de noms à l’aide du Portail Azure

Si vous disposez déjà d’un espace de noms Relay créé précédemment, passez directement à la section [Créer une connexion hybride à l’aide du portail Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Créer une connexion hybride à l’aide du portail Azure

Si vous disposez déjà d’une connexion hybride, passez directement à la section [Créer une application de serveur](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Créer une application de serveur (récepteur)

Pour écouter et recevoir des messages à partir de Relay, nous allons écrire une application de console Node.js.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Créer une application cliente (expéditeur)

Pour envoyer des messages à Relay, nous allons écrire une application de console Node.js.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Exécution des applications

1. Exécutez l’application de serveur.
2. Exécutez l’application cliente et entrez du texte.
3. Vérifiez que la console d’application de serveur renvoie le texte entré dans l’application cliente.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Vous avez créé une application de connexions hybrides complète : félicitations !

## <a name="next-steps"></a>Étapes suivantes :

* [FAQ Relay](relay-faq.md)
* [Créer un espace de noms](relay-create-namespace-portal.md)
* [Prise en main de .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Prise en main de Node](relay-hybrid-connections-node-get-started.md)


