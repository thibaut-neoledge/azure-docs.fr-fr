<properties
	pageTitle="Prise en main d’Event Hubs en C# avec Apache Storm | Microsoft Azure"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Event Hubs, à envoyer des événements en C# et à en recevoir dans un cluster Apache Storm."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="09/06/2016"
	ms.author="sethm"/>

# Prise en main des hubs d’événements

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduction

Les hubs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des hubs d’événements, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d'informations, consultez la page [Vue d'ensemble des hubs d'événements].

Dans ce didacticiel, vous allez apprendre comment procéder à la réception des messages dans un hub d'événements à l'aide d'une application console en C# et à les récupérer en parallèle à l'aide d'Apache Storm.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un environnement de développement Java configuré pour exécuter [Maven](http://maven.apache.org/). Pour ce didacticiel, nous partons du principe que la solution utilisée est [Eclipse](https://www.eclipse.org/).

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d’évaluation gratuite d’Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1.	Exécutez la classe **LogTopology** dans Eclipse, puis attendez qu’elle démarre les récepteurs pour toutes les partitions.

2.	Exécutez le projet **Expéditeur,** appuyez sur **Entrée** dans la fenêtre de console. Les événements s’affichent dans la fenêtre du destinataire.

	![][22]

## Étapes suivantes

Vous avez conçu une application opérationnelle qui crée un hub d’événements et envoie et reçoit des données. Vous pouvez à présent passer aux scénarios suivants :

- Un [exemple d'application complet qui utilise des hubs d’événements][].
- L’exemple de [montée en puissance du traitement des événements avec Event Hubs][].
- Une [solution de messages de file d'attente][] utilisant les files d'attente Service Bus.

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Vue d'ensemble des hubs d'événements]: event-hubs-overview.md
[exemple d'application complet qui utilise des hubs d’événements]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[montée en puissance du traitement des événements avec Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[solution de messages de file d'attente]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0907_2016-->