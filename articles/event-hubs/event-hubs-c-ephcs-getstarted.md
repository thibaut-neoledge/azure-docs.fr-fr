---
title: "Prise en main d’Event Hubs en C et C# | Microsoft Docs"
description: "Suivez ce didacticiel pour commencer à utiliser Azure Event Hubs, à envoyer des événements en C et à en recevoir en C# à l’aide d’EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5170c138-39ec-4eea-9925-e6902e5c425a
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 08/16/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 30ef95eebb2d260198d56af1832f897a0e8e8001


---
# <a name="get-started-with-event-hubs"></a>Prise en main des hubs d’événements
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction
Les hubs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des hubs d’événements, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez [Vue d’ensemble d’Event Hubs][Vue d’ensemble d’Event Hubs].

Dans ce didacticiel, vous allez apprendre à ingérer les messages dans un Event Hub à l’aide d’une application console en C, et à les récupérer en parallèle en utilisant la bibliothèque de l’[hôte du processeur d’événements][hôte du processeur d’événements] en C#.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un environnement de développement en C. Pour ce didacticiel, nous partirons du principe que la pile GCC se trouve sur une [machine virtuelle Linux Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dotée du système d’exploitation Ubuntu 14.04. Vous trouverez des instructions pour d’autres environnements en consultant les liens externes.
* Microsoft Visual Studio Express pour Windows
* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. Exécutez le projet **Récepteur** dans Visual Studio, puis attendez qu’il démarre les récepteurs pour toutes les partitions.
   
   ![][21]
2. Exécutez le programme **Expéditeur**. Les événements s’affichent dans la fenêtre de réception.
   
   ![][24]

## <a name="next-steps"></a>Étapes suivantes
Vous avez conçu une application opérationnelle qui crée un hub d’événements et envoie et reçoit des données. Vous pouvez à présent passer aux scénarios suivants :

* Un [exemple d'application complet qui utilise des Event Hubs][exemple d'application complet qui utilise des Event Hubs].
* Exemple de [montée en puissance du traitement des événements avec Event Hubs][montée en puissance du traitement des événements avec Event Hubs].
* [Vue d'ensemble d’Event Hubs][Vue d'ensemble d’Event Hubs]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Portail Azure Classic]: https://manage.windowsazure.com/
[hôte du processeur d’événements]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d'ensemble d’Event Hubs]: event-hubs-overview.md
[exemple d'application complet qui utilise des Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[montée en puissance du traitement des événements avec Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO3-->


