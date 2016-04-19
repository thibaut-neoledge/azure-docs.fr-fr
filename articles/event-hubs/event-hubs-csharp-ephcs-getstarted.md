<properties
	pageTitle="Prise en main d’Event Hubs en C# | Microsoft Azure"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Event Hubs en C# et à l’aide d’EventProcessorHost."
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
	ms.topic="hero-article"
	ms.date="04/12/2016"
	ms.author="sethm"/>

# Prise en main des concentrateurs d’événements

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduction

Event Hubs constitue un service qui traite de grandes quantités de données d'événement (télémétrie) à partir de périphériques et d'applications connectés. Après avoir collecté des données dans les concentrateurs d’événements, vous pouvez les stocker à l’aide d’un cluster de stockage ou les transformer à l’aide d’un fournisseur d’analyses en temps réel. Cette fonctionnalité de collecte et de traitement d’événements à grande échelle représente un élément clé des architectures d’applications modernes, notamment l’Internet des objets (IoT).

Ce didacticiel montre comment utiliser le portail Azure Classic pour créer un concentrateur d’événements. Il montre également comment collecter les messages dans un concentrateur d’événements à l’aide d’une application console en C# et les récupérer en parallèle en utilisant la bibliothèque de l’[hôte du processeur d’événements][] en C#.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

+ Microsoft Visual Studio 2013 ou version ultérieure, ou Microsoft Visual Studio Express pour Windows. Les exemples de cet article utilisent Visual Studio 2015.

+ Un compte Azure actif. <br/>Si vous n’en possédez pas, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d'informations, consultez la page d'[essai gratuit d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="\_blank").

## Création d’un concentrateur d’événements

1. Connectez-vous au [portail Azure Classic][] et cliquez sur **NOUVEAU** en bas de l’écran.

2. Cliquez successivement sur **App Services**, **Service Bus**, **Concentrateur d’événements**, puis sur **Création rapide**.

	![][1]

3. Attribuez un nom à votre concentrateur d’événements, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur d’événements**.

	![][2]

4. Si vous n'avez pas explicitement sélectionné un espace de noms existant dans une région donnée, le portail crée un espace de noms pour vous (généralement ***nom du concentrateur d'événements*-ns**). Cliquez sur cet espace de noms (dans cet exemple, **eventhub-ns**).

	![][3]

5. Cliquez sur l’onglet **Hubs d’événements** en haut de la page, puis cliquez sur le concentrateur d’événements que vous venez de créer.

	![][4]

6. Cliquez sur l'onglet **Configurer** en haut de la page, ajoutez une règle nommée **SendRule** avec des droits Envoyer, ajoutez-en une autre appelée **ReceiveRule** avec des droits *Gérer*, *Envoyer*, *Écouter*, puis cliquez sur **Enregistrer**.

	![][5]

7. Cliquez sur l’onglet **Tableau de bord** en haut de la page, puis sur **Informations de connexion**. Copiez les deux chaînes de connexion dans un emplacement temporaire car vous les utiliserez ultérieurement dans ce didacticiel.

	![][6]

Votre concentrateur d’événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. Dans Visual Studio, ouvrez le projet **Récepteur** que vous avez créé précédemment.

2. Cliquez avec le bouton droit sur la solution **Récepteur**, cliquez sur **Ajouter**, puis sur **Projet existant**.
 
3. Recherchez le fichier Sender.csproj existant, puis double-cliquez dessus pour l'ajouter à la solution.
 
4. Cliquez de nouveau avec le bouton droit sur la solution **Récepteur**, puis cliquez sur **Propriétés**. La page de propriétés **Récepteur** s'affiche.

5. Cliquez sur **Projet de démarrage**, puis sur le bouton **Plusieurs projets de démarrage**. Définissez le champ **Action** des projets **Récepteur** et **Expéditeur** sur **Démarrer**.

	![][19]

6. Cliquez sur **Dépendances du projet**. Dans le champ **Projets** cliquez sur **Expéditeur**. Dans le champ **Dépend de**, assurez-vous que l'option **Récepteur** est activée.

	![][20]

7. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés**.

1.	Appuyez sur F5 pour exécuter le projet **Récepteur** dans Visual Studio, puis attendez qu’il démarre les récepteurs pour toutes les partitions.

	![][21]

2.	Le projet **Expéditeur** s'exécutera automatiquement. Appuyez sur **Entrée** dans la fenêtre de console. Les événements s’affichent dans la fenêtre du destinataire.

	![][22]

Appuyez sur **Ctrl+C** dans la fenêtre **Expéditeur** pour quitter l'application Expéditeur, puis appuyez sur **Entrée** dans la fenêtre Récepteur pour fermer cette application.

## Étapes suivantes

Vous avez conçu une application opérationnelle qui crée un concentrateur d’événements et envoie et reçoit des données. Vous pouvez à présent passer aux scénarios suivants :

- Un [exemple d'application complet qui utilise des hubs d’événements][].
- L’exemple de [montée en puissance du traitement des événements avec Event Hubs][].
- Une [solution de messages de file d'attente][] utilisant les files d'attente Service Bus.
- [Vue d’ensemble des concentrateurs d’événements][]

<!-- Images. -->
[1]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub6.png

[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[portail Azure Classic]: https://manage.windowsazure.com/
[hôte du processeur d’événements]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d’ensemble des concentrateurs d’événements]: event-hubs-overview.md
[exemple d'application complet qui utilise des hubs d’événements]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[montée en puissance du traitement des événements avec Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[solution de messages de file d'attente]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0413_2016-->