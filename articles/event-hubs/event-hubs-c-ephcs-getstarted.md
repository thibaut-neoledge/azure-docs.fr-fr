<properties
	pageTitle="Prise en main d’Event Hubs en C et C# | Microsoft Azure"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Event Hubs, à envoyer des événements en C et à en recevoir en C# à l’aide d’EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="csharp"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="sethm"/>

# Prise en main des concentrateurs d’événements

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduction

Les concentrateurs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des concentrateurs d’événements, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la rubrique [Vue d’ensemble des concentrateurs d’événements][].

Dans ce didacticiel, vous allez apprendre à ingérer les messages dans un concentrateur d’événements à l’aide d’une application console en C et à les récupérer en parallèle en utilisant la bibliothèque de l’[hôte du processeur d’événements][] en C#.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

+ Un environnement de développement en C. Pour ce didacticiel, nous partirons du principe que la pile GCC se trouve sur une [machine virtuelle Linux Azure](../virtual-machines/virtual-machines-linux-tutorial.md) dotée du système d’exploitation Ubuntu 14.04. Vous trouverez des instructions pour d’autres environnements en consultant les liens externes.

+ Microsoft Visual Studio Express 2013 pour Windows ;

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d’évaluation gratuite d’Azure</a>.

## Création d’un concentrateur d’événements

1. Connectez-vous au [portail Azure Classic][] et cliquez sur **NOUVEAU** en bas de l’écran.

2. Cliquez successivement sur **App Services**, **Service Bus**, **Concentrateur d’événements**, puis sur **Création rapide**.

   	![][1]

3. Attribuez un nom à votre concentrateur d’événements, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur d’événements**.

   	![][2]

4. Cliquez sur l’espace de noms que vous venez de créer (en règle générale ***nom du hub d’événements*-ns**).

   	![][3]

5. Cliquez sur l’onglet **Hubs d’événements** en haut de la page, puis cliquez sur le concentrateur d’événements que vous venez de créer.

   	![][4]

6. Cliquez sur l’onglet **Configurer** en haut de la page, ajoutez une règle nommée **SendRule** avec des droits *Envoyer*, ajoutez-en une autre appelée **ReceiveRule** avec des droits *Gérer, Envoyer, Écouter*, puis cliquez sur **Enregistrer**.

   	![][5]

7. Sur la même page, notez les clés générées pour **SendRule**.

   	![][6b]

8. Cliquez sur l’onglet **Tableau de bord** en haut de la page, puis sur **Informations de connexion**. Notez les deux chaînes de connexion.

   	![][6]

Votre concentrateur d’événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1.	Exécutez le projet **Récepteur** dans Visual Studio, puis attendez qu’il démarre les récepteurs pour toutes les partitions.

   	![][21]

2.	Exécutez le programme **Expéditeur**. Les événements s’affichent dans la fenêtre de réception.

   	![][24]

## Étapes suivantes

Vous avez conçu une application opérationnelle qui crée un concentrateur d’événements et envoie et reçoit des données. Vous pouvez à présent passer aux scénarios suivants :

- Un [exemple d'application complet qui utilise des hubs d’événements][].
- L’exemple de [montée en puissance du traitement des événements avec Event Hubs][].
- Une [solution de messages de file d'attente][] utilisant les files d'attente Service Bus.
- [Vue d’ensemble des concentrateurs d’événements][]

<!-- Images. -->
[1]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub6.png
[6b]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub6b.png


[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[portail Azure Classic]: https://manage.windowsazure.com/
[hôte du processeur d’événements]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d’ensemble des concentrateurs d’événements]: event-hubs-overview.md
[exemple d'application complet qui utilise des hubs d’événements]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[montée en puissance du traitement des événements avec Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[solution de messages de file d'attente]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

<!---HONumber=AcomDC_1217_2015-->