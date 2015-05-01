<properties
	pageTitle="Prise en main des concentrateurs d'événements"
	description="Suivez ce didacticiel pour commencer à utiliser les concentrateurs d'événements Azure avec C# à l'aide de EventProcessorHost"
	services="service-bus"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="core"
	ms.tgt_pltfrm="csharp"
	ms.devlang="csharp"
	ms.topic="hero-article"
	ms.date="04/13/2015"
	ms.author="sethm"/>

# Prise en main des concentrateurs d'événements

[AZURE.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

## Introduction

Les concentrateurs d'événements permettent de traiter de grandes quantités de données d'événement à partir de périphériques et d'applications connectés. Après avoir collecté des données dans les concentrateurs d'événements, vous pouvez les stocker à l'aide d'un cluster de stockage ou les transformer à l'aide d'un fournisseur d'analyses en temps réel. Cette fonctionnalité de collecte et de traitement d'événements à grande échelle représente un élément clé des architectures d'applications modernes, notamment l'Internet des objets.

Ce didacticiel montre comment utiliser le portail de gestion Azure pour créer un concentrateur d'événements. Il montre également comment collecter les messages dans un concentrateur d'événements à l'aide d'une application console en C# et les récupérer en parallèle en utilisant la bibliothèque [EventProcessorHost] en C#.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

+ Microsoft Visual Studio 2013 ou Microsoft Visual Studio Express 2013 pour Windows.

+ un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

## Création d'un concentrateur d'événements

1. Connectez-vous au [portail de gestion Azure] et cliquez sur **NOUVEAU** en bas de l'écran.

2. Cliquez successivement sur **Services d'application**, **Service Bus**, **Concentrateur d'événements**, puis sur **Création rapide**.

   	![][1]

3. Attribuez un nom à votre concentrateur d'événements, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur d'événements**.

   	![][2]

4. Cliquez sur l'espace de noms que vous venez de créer (en règle générale, ***event hub name*-ns**).

   	![][3]

5. Cliquez sur l'onglet **Concentrateurs d'événements** en haut de la page, puis cliquez sur le concentrateur d'événements que vous venez de créer.

   	![][4]

6. Cliquez sur l'onglet **Configurer** en haut de la page, ajoutez une règle nommée **SendRule** avec des droits *Envoyer*, ajoutez-en une autre appelée **ReceiveRule** avec des droits *Gérer, Envoyer, Écouter*, puis cliquez sur **Enregistrer**.

   	![][5]

7. Cliquez sur l'onglet **Tableau de bord** en haut de la page, puis sur **Informations de connexion**. Prenez note des deux chaînes de connexion, ou copiez-les en vue d'une utilisation ultérieure pour ce didacticiel.

   	![][6]

Votre concentrateur d'événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1.	Dans Visual Studio, exécutez le projet **Receiver**, puis attendez qu'il démarre les récepteurs pour toutes les partitions.

   	![][21]

2.	Exécutez le projet **Expéditeur**, appuyez sur **Entrée** dans la fenêtre de console. Les événements s'affichent dans la fenêtre de réception.

   	![][22]

## Étapes suivantes

Vous avez conçu une application opérationnelle qui crée un concentrateur d'événements et envoie et reçoit des données. Vous pouvez à présent passer aux scénarios suivants :

- Un [exemple d'application complet qui utilise les concentrateurs d'événements].
- L'exemple de [montée en puissance du traitement des événements avec les concentrateurs d'événements].
- Une [solution de messages de file d'attente] utilisant les files d'attente Service Bus.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png

[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Hôte du processeur d'événements]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d'ensemble des concentrateurs d'événements]: http://msdn.microsoft.com/library/azure/dn836025.aspx
[exemple d'application complet qui utilise les concentrateurs d'événements]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[montée en puissance du traitement des événements avec les concentrateurs d'événements]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Solution de messages de file d'attente]: cloud-services-dotnet-multi-tier-app-using-service-bus-queues.md


<!--HONumber=52-->