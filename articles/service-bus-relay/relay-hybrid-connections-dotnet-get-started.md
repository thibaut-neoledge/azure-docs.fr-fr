---
title: Prise en main des connexions hybrides Relay | Microsoft Docs
description: "Comment écrire une application de console C# pour les connexions hybrides"
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 92d7935596ab1dede6dc1d613cb635c32d52e3ab


---
# <a name="get-started-with-relay-hybrid-connections"></a>Prise en main des connexions hybrides Relay
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Les opérations que nous allons effectuer
Étant donné que les connexions hybrides nécessitent un client et un serveur, nous allons créer deux applications de console dans ce didacticiel. Voici la procédure à suivre :

1. Créer un espace de noms Relay à l’aide du portail Azure.
2. Créer une connexion hybride, à l’aide du portail Azure.
3. Écrire une application de console de serveur pour recevoir des messages.
4. Écrire une application de console de client pour envoyer des messages.

## <a name="prerequisites"></a>Composants requis
1. [Visual Studio 2013 ou Visual Studio 2015](http://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2015.
2. Un abonnement Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Créer un espace de noms à l’aide du Portail Azure
Si vous disposez déjà d’un espace de noms Relay créé précédemment, passez directement à la section [Créer une connexion hybride à l’aide du portail Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Créer une connexion hybride à l’aide du portail Azure
Si vous disposez déjà d’une connexion hybride, passez directement à la section [Créer une application de serveur](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Créer une application de serveur (récepteur)
Pour écouter et recevoir des messages à partir de Relay, nous allons écrire une application de console C# à l’aide de Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Créer une application cliente (expéditeur)
Pour envoyer des messages à Relay, nous allons écrire une application de console C# à l’aide de Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Exécution des applications
1. Exécutez l’application de serveur.
2. Exécutez l’application cliente et entrez du texte.
3. Vérifiez que la console d’application de serveur renvoie le texte entré dans l’application cliente.

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Félicitations, vous avez créé une application de connexions hybrides de bout en bout.

## <a name="next-steps"></a>Étapes suivantes :
* [FAQ Relay](relay-faq.md)
* [Créer un espace de noms](relay-create-namespace-portal.md)
* [Prise en main de Node](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO2-->


