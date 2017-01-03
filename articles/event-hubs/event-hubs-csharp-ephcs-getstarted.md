---
title: "Prise en main d’Event Hubs en C# | Microsoft Docs"
description: "Suivez ce didacticiel pour commencer à utiliser Azure Event Hubs en C# et à l’aide d’EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 2ec2378a-34f7-44c3-b976-cc444c98c338
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/07/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 015dbab81f807cd05d1fedb97f5fc62a012f2084
ms.openlocfilehash: 54aaf68864fc83501b860ecc3daef1c0216c0140


---
# <a name="get-started-with-event-hubs"></a>Prise en main des hubs d’événements
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction
Event Hubs constitue un service qui traite de grandes quantités de données d'événement (télémétrie) à partir de périphériques et d'applications connectés. Après avoir collecté des données dans les concentrateurs d’événements, vous pouvez les stocker à l’aide d’un cluster de stockage ou les transformer à l’aide d’un fournisseur d’analyses en temps réel. Cette fonctionnalité de collecte et de traitement d’événements à grande échelle représente un élément clé des architectures d’applications modernes, notamment l’Internet des objets (IoT).

Ce didacticiel montre comment utiliser le portail Azure pour créer un concentrateur d'événements. Il montre également comment collecter les messages dans un hub d’événements à l’aide d’une application console en C# et les récupérer en parallèle en utilisant la bibliothèque de [l’hôte du processeur d’événements][Event Processor Host] en C#.

Pour réaliser ce didacticiel, vous aurez besoin des éléments suivants :

* [Microsoft Visual Studio](http://visualstudio.com)
* Un compte Azure actif. Si vous n’en avez pas, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/free/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. Dans Visual Studio, ouvrez le projet **Récepteur** que vous avez créé précédemment.
2. Cliquez avec le bouton droit sur la solution **Récepteur** et cliquez sur **Ajouter**, puis sur **Projet existant**.
3. Recherchez le fichier Sender.csproj existant, puis double-cliquez dessus pour l'ajouter à la solution.
4. Cliquez de nouveau avec le bouton droit sur la solution **Récepteur**, puis cliquez sur **Propriétés**. La page de propriétés **Récepteur** s’affiche.
5. Cliquez sur **Projet de démarrage**, puis sur le bouton **Plusieurs projets de démarrage**. Définissez le champ **Action** des projets **Récepteur** et **Expéditeur** sur **Démarrer**.
   
    ![][19]
6. Cliquez sur **Dépendances du projet**. Dans la zone **Projets**, cliquez sur **Expéditeur**. Dans le champ **Dépend de**, assurez-vous que l’option **Récepteur** est activée.
   
    ![][20]
7. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés**.
8. Appuyez sur F5 pour exécuter le projet **Récepteur** dans Visual Studio, puis attendez qu’il démarre les récepteurs pour toutes les partitions.
   
   ![][21]
9. Le projet **Expéditeur** s’exécute automatiquement. Appuyez sur **Entrée** dans la fenêtre de console. Les événements s’affichent dans la fenêtre du récepteur.
   
   ![][22]

Appuyez sur **Ctrl+C** dans la fenêtre **Expéditeur** pour quitter l’application Expéditeur, puis appuyez sur **Entrée** dans la fenêtre Récepteur pour fermer cette application.

## <a name="next-steps"></a>Étapes suivantes
Vous avez conçu une application opérationnelle qui crée un hub d’événements et envoie et reçoit des données. Vous pouvez à présent passer aux scénarios suivants :

* Un [exemple d’application complet qui utilise des hubs d’événements][sample application that uses Event Hubs].
* [hôte du processeur d’événements](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* L’exemple de [montée en puissance du traitement des événements avec Event Hubs][Scale out Event Processing with Event Hubs].
* [Vue d'ensemble d’Event Hubs][Event Hubs overview]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Jan17_HO1-->


