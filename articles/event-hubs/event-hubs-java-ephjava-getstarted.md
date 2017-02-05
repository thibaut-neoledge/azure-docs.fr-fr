---
title: "Prise en main d’Event Hubs dans Java | Microsoft Docs"
description: "Suivez ce didacticiel pour commencer à utiliser Azure Event Hubs, à envoyer des événements en Java et à en recevoir à l’aide d’EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 38e3be53-251c-488f-a856-9a500f41b6ca
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f71c6f337efaab7ae497eb6c3334e33d9531ec57


---
# <a name="get-started-with-event-hubs"></a>Prise en main des hubs d’événements
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction
Les hubs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des hubs d’événements, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la [Vue d'ensemble d’Event Hubs][Vue d'ensemble d’Event Hubs].

Ce didacticiel explique comment ingérer les messages dans un hub d’événements à l’aide d’une application console en Java et les récupérer en parallèle à l’aide de la bibliothèque de l’hôte du processeur d’événements en Java.

Pour effectuer ce didacticiel, vous devrez disposer des éléments suivants :

* Un environnement de développement Java. Pour ce didacticiel, nous partons du principe que la solution utilisée est [Eclipse](https://www.eclipse.org/).
* Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. Exécutez le projet **Récepteur** .
   
   ![][21]
2. Exécutez le projet **Expéditeur** .
   
   ![][22]

## <a name="next-steps"></a>Étapes suivantes
Vous avez conçu une application opérationnelle qui crée un hub d’événements et envoie et reçoit des données. Vous pouvez à présent passer aux scénarios suivants :

* Un [exemple d'application complet qui utilise des Event Hubs][exemple d'application complet qui utilise des Event Hubs].
* Exemple de [montée en puissance du traitement des événements avec Event Hubs][montée en puissance du traitement des événements avec Event Hubs].

Pour plus d’informations, consultez le [Centre pour développeurs Java](/develop/java/).

<!-- Images. -->
[21]: ./media/event-hubs-java-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-java-ephjava-getstarted/java-send.png

<!-- Links -->
[Portail Azure Classic]: https://manage.windowsazure.com/
[Vue d'ensemble d’Event Hubs]: event-hubs-overview.md
[exemple d'application complet qui utilise des Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[montée en puissance du traitement des événements avec Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO3-->


