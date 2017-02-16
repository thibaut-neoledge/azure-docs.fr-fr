---
title: "Prise en main d’Event Hubs en C | Microsoft Docs"
description: "Suivez ce didacticiel pour commencer à utiliser Azure Event Hubs, à envoyer des événements en C et à en recevoir en Java à l’aide d’EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: aa6553f9-e12e-4568-9bf3-667f1c47a6cf
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 01/04/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 964adaa1b4d1247cf26bd1b8a345d785a114e3ab
ms.openlocfilehash: ec1ed9ccf9510587a76a4860f910ac2772b35f9a


---
# <a name="get-started-with-event-hubs"></a>Prise en main des hubs d’événements
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction
Les hubs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des hubs d’événements, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la page [Vue d’ensemble d’Event Hubs][Event Hubs overview].

Dans ce didacticiel, vous allez apprendre à ingérer les messages dans un hub d’événements à l’aide d’une application console en C et à les récupérer en parallèle en utilisant la bibliothèque de l’[hôte du processeur d’événements][Event Processor Host] en C#.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un environnement de développement en C. Pour ce didacticiel, nous partirons du principe que la pile GCC se trouve sur une [machine virtuelle Linux Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dotée du système d’exploitation Ubuntu 14.04. Vous trouverez des instructions pour d’autres environnements en consultant les liens externes.
* Microsoft Visual Studio Express pour Windows
* Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. Exécutez le projet **Récepteur** .
   
   ![][21]
2. Exécutez le programme **Expéditeur**. Les événements s’affichent dans la fenêtre de réception.
   
   ![][24]

## <a name="next-steps"></a>Étapes suivantes
Vous avez conçu une application opérationnelle qui crée un hub d’événements et envoie et reçoit des données. Vous pouvez à présent passer aux scénarios suivants :

* Un [exemple d'application complet qui utilise des hubs d’événements][sample application that uses Event Hubs].
* L’exemple de [montée en puissance du traitement des événements avec Event Hubs][Scale out Event Processing with Event Hubs].
* [Vue d’ensemble des hubs d’événements][Event Hubs overview]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Jan17_HO1-->


