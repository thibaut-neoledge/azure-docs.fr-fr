<properties 
	pageTitle="Prise en main des concentrateurs d'événements" 
	description="Suivez ce didacticiel pour commencer à utiliser les concentrateurs d'événements Azure envoyant des événements avec C# et les recevant dans un cluster Storm Apache" 
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
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Prise en main des concentrateurs d'événements

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduction

Les concentrateurs d'événements représentent un système d'ingestion à l'extensibilité élevée en mesure d'absorber des millions d'événements par seconde, ce qui permet à une application de traiter et d'analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des concentrateurs d'événements, vous pouvez transformer et stocker des données à l'aide de n'importe quel fournisseur d'analyses en temps réel ou d'un cluster de stockage.

Pour plus d'informations, consultez la page [Vue d'ensemble des concentrateurs d'événements].

Dans ce didacticiel, vous allez apprendre comment procéder à la réception des messages dans un concentrateur d'événements à l'aide d'une application console en C# et à les récupérer en parallèle à l'aide d'Apache Storm.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

+ Microsoft Visual Studio Express 2013 pour Windows ;

+ Un environnement de développement Java configuré pour exécuter [Maven](http://maven.apache.org/). Pour ce didacticiel, nous partirons du principe que la solution utilisée est [Eclipse](https://www.eclipse.org/).

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

6. Cliquez sur l'onglet **Configurer** en haut de la page, ajoutez une règle nommée **SendRule** avec des droits *Envoyer*, ajoutez-en une autre appelée **ReceiveRule** avec des droits *Écoutern*, puis cliquez sur **Enregistrer**.

   	![][5]

7. Sur la même page, notez les clés générées pour **ReceiveRule**.

   	![][6c]

8. Cliquez sur l'onglet **Tableau de bord** en haut, puis cliquez sur **Informations de connexion**. Notez les deux chaînes de connexion.

   	![][6]

Votre concentrateur d'événements est désormais créé et vous disposez des chaînes de connexion dont vous avez besoin pour envoyer et recevoir des événements.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1.	Exécutez la classe **LogTopology** dans Eclipse, puis attendez qu'elle démarre les récepteurs pour toutes les partitions.

2.	Exécutez le projet **Expéditeur**, appuyez sur **Entrée** dans la fenêtre de console. Les événements s'affichent dans la fenêtre de réception.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-csharp-storm-getstarted/create-event-hub6c.png

[22]: ./media/service-bus-event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Hôte du processeur d'événements]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d'ensemble des concentrateurs d'événements]: http://msdn.microsoft.com/library/azure/dn836025.aspx


<!--HONumber=52--> 